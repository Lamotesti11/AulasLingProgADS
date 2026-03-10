# Aula 13 - Express: CRUD Completo e Middlewares

## Objetivos da Aula

- Implementar um CRUD completo com persistencia em arquivo JSON
- Entender o conceito de middleware no Express
- Criar middlewares personalizados (logger, cors, autenticacao)
- Organizar o projeto em camadas (routes, services, data)

---

## 1. O que sao Middlewares?

Middlewares sao funcoes que executam **entre** a requisicao e a resposta. Eles podem:

- Modificar a requisicao (`req`) ou resposta (`res`)
- Executar logica (logs, autenticacao, validacao)
- Encerrar o ciclo de requisicao
- Chamar o proximo middleware com `next()`

```
Requisicao -> [Middleware 1] -> [Middleware 2] -> [Rota] -> Resposta
```

### 1.1 Middleware que ja usamos

```typescript
// express.json() e um middleware que parseia o body JSON
app.use(express.json());
```

### 1.2 Anatomia de um middleware

```typescript
import { Request, Response, NextFunction } from "express";

function meuMiddleware(req: Request, res: Response, next: NextFunction): void {
  // Faca algo com req ou res
  console.log("Middleware executado!");

  // IMPORTANTE: chame next() para passar ao proximo middleware/rota
  next();
}

// Registrar para TODAS as rotas
app.use(meuMiddleware);

// Ou para rotas especificas
app.get("/alunos", meuMiddleware, (req, res) => { ... });
```

---

## 2. Middlewares Uteis

### 2.1 Logger - registrar requisicoes

```typescript
function logger(req: Request, res: Response, next: NextFunction): void {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${req.method} ${req.url}`);
  next();
}

app.use(logger);
```

Saida no terminal:

```
[2026-03-10T14:30:00.000Z] GET /alunos
[2026-03-10T14:30:01.000Z] POST /alunos
[2026-03-10T14:30:02.000Z] DELETE /alunos/1
```

### 2.2 CORS - permitir acesso de outros dominios

```bash
npm install cors
npm install -D @types/cors
```

```typescript
import cors from "cors";

// Permitir requisicoes de qualquer origem
app.use(cors());

// Ou restringir a origens especificas
app.use(cors({
  origin: "http://localhost:5173",  // Apenas seu frontend
}));
```

> **Por que CORS?** Por padrao, navegadores bloqueiam requisicoes de um dominio para outro (seguranca). O CORS libera esse acesso de forma controlada.

### 2.3 Middleware de autenticacao simples

```typescript
function autenticar(req: Request, res: Response, next: NextFunction): void {
  const apiKey = req.headers["x-api-key"];

  if (apiKey !== "minha-chave-secreta") {
    res.status(401).json({ erro: "Nao autorizado. Informe uma API key valida." });
    return;
  }

  next();
}

// Aplicar apenas em rotas que precisam de autenticacao
app.post("/alunos", autenticar, (req, res) => { ... });
app.delete("/alunos/:id", autenticar, (req, res) => { ... });
```

Teste:

```bash
# Sem autenticacao - erro
curl -X POST http://localhost:3000/alunos -H "Content-Type: application/json" -d '{}'

# Com autenticacao - funciona
curl -X POST http://localhost:3000/alunos \
  -H "Content-Type: application/json" \
  -H "x-api-key: minha-chave-secreta" \
  -d '{"nome": "Ana"}'
```

---

## 3. CRUD com Persistencia em JSON

Vamos evoluir a API para salvar dados em arquivo, usando o que aprendemos na Aula 10:

### 3.1 Camada de dados

```typescript
// src/data/aluno-repository.ts
import fs from "fs";
import path from "path";

interface Aluno {
  id: number;
  nome: string;
  email: string;
  curso: string;
  nota: number;
}

const ARQUIVO = path.join(__dirname, "../../data/alunos.json");

function garantirArquivo(): void {
  const dir = path.dirname(ARQUIVO);
  if (!fs.existsSync(dir)) {
    fs.mkdirSync(dir, { recursive: true });
  }
  if (!fs.existsSync(ARQUIVO)) {
    fs.writeFileSync(ARQUIVO, "[]", "utf-8");
  }
}

export function listarTodos(): Aluno[] {
  garantirArquivo();
  const dados = fs.readFileSync(ARQUIVO, "utf-8");
  return JSON.parse(dados);
}

export function buscarPorId(id: number): Aluno | undefined {
  const alunos = listarTodos();
  return alunos.find((a) => a.id === id);
}

export function criar(dados: Omit<Aluno, "id">): Aluno {
  const alunos = listarTodos();
  const novoId = alunos.length > 0 ? Math.max(...alunos.map((a) => a.id)) + 1 : 1;
  const novoAluno: Aluno = { id: novoId, ...dados };
  alunos.push(novoAluno);
  salvar(alunos);
  return novoAluno;
}

export function atualizar(id: number, dados: Partial<Omit<Aluno, "id">>): Aluno | null {
  const alunos = listarTodos();
  const indice = alunos.findIndex((a) => a.id === id);
  if (indice === -1) return null;

  alunos[indice] = { ...alunos[indice], ...dados };
  salvar(alunos);
  return alunos[indice];
}

export function remover(id: number): boolean {
  const alunos = listarTodos();
  const novosAlunos = alunos.filter((a) => a.id !== id);
  if (novosAlunos.length === alunos.length) return false;

  salvar(novosAlunos);
  return true;
}

function salvar(alunos: Aluno[]): void {
  fs.writeFileSync(ARQUIVO, JSON.stringify(alunos, null, 2), "utf-8");
}
```

### 3.2 Rotas usando o repositorio

```typescript
// src/routes/alunos.ts
import { Router, Request, Response } from "express";
import * as repo from "../data/aluno-repository";

const router = Router();

router.get("/", (req: Request, res: Response) => {
  const alunos = repo.listarTodos();
  res.json(alunos);
});

router.get("/:id", (req: Request, res: Response) => {
  const aluno = repo.buscarPorId(Number(req.params.id));
  if (!aluno) {
    res.status(404).json({ erro: "Aluno nao encontrado" });
    return;
  }
  res.json(aluno);
});

router.post("/", (req: Request, res: Response) => {
  const { nome, email, curso, nota } = req.body;

  if (!nome || !email || !curso || nota === undefined) {
    res.status(400).json({ erro: "Campos obrigatorios: nome, email, curso, nota" });
    return;
  }

  const aluno = repo.criar({ nome, email, curso, nota });
  res.status(201).json(aluno);
});

router.put("/:id", (req: Request, res: Response) => {
  const aluno = repo.atualizar(Number(req.params.id), req.body);
  if (!aluno) {
    res.status(404).json({ erro: "Aluno nao encontrado" });
    return;
  }
  res.json(aluno);
});

router.delete("/:id", (req: Request, res: Response) => {
  const removido = repo.remover(Number(req.params.id));
  if (!removido) {
    res.status(404).json({ erro: "Aluno nao encontrado" });
    return;
  }
  res.status(204).send();
});

export default router;
```

### 3.3 Server principal

```typescript
// src/server.ts
import express from "express";
import cors from "cors";
import alunosRouter from "./routes/alunos";

const app = express();
const PORT = 3000;

// Middlewares globais
app.use(cors());
app.use(express.json());

// Logger
app.use((req, res, next) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  next();
});

// Rotas
app.use("/alunos", alunosRouter);

// Rota raiz
app.get("/", (req, res) => {
  res.json({
    mensagem: "API de Alunos",
    versao: "1.0.0",
    endpoints: {
      alunos: "/alunos",
    },
  });
});

app.listen(PORT, () => {
  console.log(`Servidor rodando em http://localhost:${PORT}`);
});
```

---

## 4. Estrutura Final do Projeto

```
api-alunos/
  data/
    alunos.json           # "Banco de dados" JSON
  src/
    server.ts             # Ponto de entrada
    routes/
      alunos.ts           # Rotas de alunos
    data/
      aluno-repository.ts # Operacoes de dados
    middlewares/
      logger.ts           # Middleware de log
      auth.ts             # Middleware de autenticacao
  package.json
  tsconfig.json
  .gitignore
```

---

## 5. Middleware de Tratamento de Erros

Middleware de erro tem **4 parametros** (o primeiro e o erro):

```typescript
import { Request, Response, NextFunction } from "express";

function tratarErros(
  erro: Error,
  req: Request,
  res: Response,
  next: NextFunction
): void {
  console.error(`[ERRO] ${erro.message}`);
  res.status(500).json({
    erro: "Erro interno do servidor",
    mensagem: erro.message,
  });
}

// DEVE ser o ultimo middleware registrado
app.use(tratarErros);
```

---

## 6. Git - Ignorando dados e mantendo estrutura

```gitignore
node_modules/
dist/

# Ignorar dados gerados, mas manter o diretorio
data/*.json
!data/.gitkeep
```

Crie um `data/.gitkeep` para que o Git mantenha a pasta:

```bash
touch data/.gitkeep
git add data/.gitkeep
git commit -m "Adiciona estrutura de dados com persistencia JSON"
```

---

## Exercicios Praticos

### Exercicio 1 - CRUD de Produtos com persistencia
1. Crie uma API de produtos com persistencia em `produtos.json`
2. Endpoints: GET (listar e buscar), POST, PUT, DELETE
3. Adicione middleware de logger
4. Teste todos os endpoints

### Exercicio 2 - Middleware de tempo de resposta
Crie um middleware que:
1. Registre o tempo de inicio da requisicao
2. Apos a resposta, calcule o tempo total
3. Exiba: `[GET /alunos] 15ms`
4. Dica: use `res.on("finish", () => { ... })`

### Exercicio 3 - Paginacao
Adicione paginacao a rota GET /alunos:
1. Query params: `?page=1&limit=10`
2. Retorne os dados paginados com metadados:
```json
{
  "dados": [...],
  "paginacao": {
    "total": 50,
    "pagina": 1,
    "porPagina": 10,
    "totalPaginas": 5
  }
}
```

### Exercicio 4 - Multiplos recursos
Expanda a API para ter dois recursos relacionados:
1. `/cursos` - CRUD de cursos (id, nome, duracao)
2. `/alunos` - adicione campo `cursoId` referenciando um curso
3. `GET /cursos/:id/alunos` - listar alunos de um curso

---

## Dica: IA e Arquitetura de APIs

1. **Gerar CRUD rapidamente:** Defina a interface e peca ao Copilot:
   *"Gere um repository com CRUD completo persistindo em JSON"*

2. **Criar middlewares:** *"Crie um middleware Express que limita requisicoes a 100 por minuto por IP"*

3. **Revisar codigo:** Selecione suas rotas e peca: *"Quais melhorias de seguranca posso fazer nessas rotas?"*

> **Lembrete:** Middlewares sao um padrao importantissimo. Eles aparecem em praticamente todos os frameworks web, nao so no Express.

---

## Leitura Complementar

- [Express - Middleware](https://expressjs.com/en/guide/using-middleware.html)
- [Express - Error Handling](https://expressjs.com/en/guide/error-handling.html)
