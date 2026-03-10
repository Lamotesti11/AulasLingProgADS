# Aula 14 - Express: Validacao e Tratamento de Erros

## Objetivos da Aula

- Validar dados de entrada com Zod
- Implementar tratamento de erros robusto
- Padronizar respostas de erro da API
- Criar middlewares de validacao reutilizaveis
- Organizar o projeto com camada de servicos

---

## 1. Por que Validar?

Nunca confie nos dados que chegam de fora. Qualquer pessoa pode enviar qualquer coisa para sua API:

```bash
# Isso pode acontecer:
curl -X POST http://localhost:3000/alunos \
  -H "Content-Type: application/json" \
  -d '{"nome": "", "email": "nao-e-email", "nota": "abc"}'
```

Sem validacao, isso pode:
- Corromper seus dados
- Causar erros inesperados
- Abrir brechas de seguranca

---

## 2. Validacao com Zod

O **Zod** e uma biblioteca de validacao com integracao nativa de tipos TypeScript:

```bash
npm install zod
```

### 2.1 Definindo schemas

```typescript
import { z } from "zod";

const AlunoSchema = z.object({
  nome: z.string()
    .min(2, "Nome deve ter pelo menos 2 caracteres")
    .max(100, "Nome deve ter no maximo 100 caracteres"),
  email: z.string()
    .email("Email invalido"),
  curso: z.string()
    .min(1, "Curso e obrigatorio"),
  nota: z.number()
    .min(0, "Nota minima e 0")
    .max(10, "Nota maxima e 10"),
});

// O Zod GERA o tipo TypeScript automaticamente!
type Aluno = z.infer<typeof AlunoSchema>;
```

### 2.2 Validando dados

```typescript
// Validacao segura (nao lanca excecao)
const resultado = AlunoSchema.safeParse({
  nome: "Ana",
  email: "ana@email.com",
  curso: "ADS",
  nota: 8.5,
});

if (resultado.success) {
  console.log("Dados validos:", resultado.data);
  // resultado.data e tipado como Aluno!
} else {
  console.log("Erros:", resultado.error.issues);
}
```

### 2.3 Schema para atualizacao parcial

```typescript
// partial() torna todos os campos opcionais (para PATCH/PUT parcial)
const AtualizarAlunoSchema = AlunoSchema.partial();

// pick() seleciona campos especificos
const AtualizarNotaSchema = AlunoSchema.pick({ nota: true });
```

---

## 3. Middleware de Validacao

Crie um middleware reutilizavel que valida o body contra qualquer schema Zod:

```typescript
// src/middlewares/validar.ts
import { Request, Response, NextFunction } from "express";
import { ZodSchema } from "zod";

export function validar(schema: ZodSchema) {
  return (req: Request, res: Response, next: NextFunction): void => {
    const resultado = schema.safeParse(req.body);

    if (!resultado.success) {
      const erros = resultado.error.issues.map((issue) => ({
        campo: issue.path.join("."),
        mensagem: issue.message,
      }));

      res.status(400).json({
        erro: "Dados invalidos",
        detalhes: erros,
      });
      return;
    }

    // Substitui o body pelos dados validados e tipados
    req.body = resultado.data;
    next();
  };
}
```

### Usando nas rotas

```typescript
import { validar } from "../middlewares/validar";
import { AlunoSchema, AtualizarAlunoSchema } from "../schemas/aluno";

// POST usa validacao completa
router.post("/", validar(AlunoSchema), (req, res) => {
  // req.body ja esta validado e tipado!
  const aluno = repo.criar(req.body);
  res.status(201).json(aluno);
});

// PUT usa validacao parcial
router.put("/:id", validar(AtualizarAlunoSchema), (req, res) => {
  const aluno = repo.atualizar(Number(req.params.id), req.body);
  if (!aluno) {
    res.status(404).json({ erro: "Aluno nao encontrado" });
    return;
  }
  res.json(aluno);
});
```

Agora um POST com dados invalidos retorna:

```json
{
  "erro": "Dados invalidos",
  "detalhes": [
    { "campo": "nome", "mensagem": "Nome deve ter pelo menos 2 caracteres" },
    { "campo": "email", "mensagem": "Email invalido" },
    { "campo": "nota", "mensagem": "Expected number, received string" }
  ]
}
```

---

## 4. Tratamento de Erros Centralizado

### 4.1 Erros customizados

```typescript
// src/errors/api-error.ts
export class ApiError extends Error {
  constructor(
    public statusCode: number,
    message: string
  ) {
    super(message);
    this.name = "ApiError";
  }
}

export class NotFoundError extends ApiError {
  constructor(recurso: string) {
    super(404, `${recurso} nao encontrado(a)`);
  }
}

export class BadRequestError extends ApiError {
  constructor(mensagem: string) {
    super(400, mensagem);
  }
}
```

### 4.2 Middleware de erros

```typescript
// src/middlewares/error-handler.ts
import { Request, Response, NextFunction } from "express";
import { ApiError } from "../errors/api-error";

export function errorHandler(
  erro: Error,
  req: Request,
  res: Response,
  next: NextFunction
): void {
  console.error(`[ERRO] ${erro.message}`);

  if (erro instanceof ApiError) {
    res.status(erro.statusCode).json({
      erro: erro.message,
    });
    return;
  }

  // Erro inesperado
  res.status(500).json({
    erro: "Erro interno do servidor",
  });
}
```

### 4.3 Usando nas rotas

```typescript
import { NotFoundError } from "../errors/api-error";

router.get("/:id", (req, res, next) => {
  try {
    const aluno = repo.buscarPorId(Number(req.params.id));
    if (!aluno) {
      throw new NotFoundError("Aluno");
    }
    res.json(aluno);
  } catch (erro) {
    next(erro);  // Passa o erro para o middleware de erros
  }
});
```

### 4.4 Wrapper para async

Para nao precisar de try/catch em toda rota:

```typescript
// src/utils/async-handler.ts
import { Request, Response, NextFunction, RequestHandler } from "express";

export function asyncHandler(
  fn: (req: Request, res: Response, next: NextFunction) => Promise<void>
): RequestHandler {
  return (req, res, next) => {
    fn(req, res, next).catch(next);
  };
}
```

```typescript
// Uso - sem try/catch manual
router.get("/:id", asyncHandler(async (req, res) => {
  const aluno = repo.buscarPorId(Number(req.params.id));
  if (!aluno) throw new NotFoundError("Aluno");
  res.json(aluno);
}));
```

---

## 5. Camada de Servicos

Separe a logica de negocio das rotas:

```typescript
// src/services/aluno-service.ts
import * as repo from "../data/aluno-repository";
import { NotFoundError, BadRequestError } from "../errors/api-error";

interface Aluno {
  id: number;
  nome: string;
  email: string;
  curso: string;
  nota: number;
}

export function listarAlunos(filtros?: { curso?: string }): Aluno[] {
  let alunos = repo.listarTodos();
  if (filtros?.curso) {
    alunos = alunos.filter(
      (a) => a.curso.toLowerCase() === filtros.curso!.toLowerCase()
    );
  }
  return alunos;
}

export function buscarAluno(id: number): Aluno {
  const aluno = repo.buscarPorId(id);
  if (!aluno) throw new NotFoundError("Aluno");
  return aluno;
}

export function criarAluno(dados: Omit<Aluno, "id">): Aluno {
  // Verificar email duplicado
  const existente = repo.listarTodos().find((a) => a.email === dados.email);
  if (existente) {
    throw new BadRequestError("Ja existe um aluno com esse email");
  }
  return repo.criar(dados);
}

export function atualizarAluno(id: number, dados: Partial<Omit<Aluno, "id">>): Aluno {
  const aluno = repo.atualizar(id, dados);
  if (!aluno) throw new NotFoundError("Aluno");
  return aluno;
}

export function removerAluno(id: number): void {
  const removido = repo.remover(id);
  if (!removido) throw new NotFoundError("Aluno");
}
```

Rotas ficam limpas:

```typescript
// src/routes/alunos.ts
import { Router } from "express";
import * as service from "../services/aluno-service";
import { validar } from "../middlewares/validar";
import { AlunoSchema } from "../schemas/aluno";
import { asyncHandler } from "../utils/async-handler";

const router = Router();

router.get("/", asyncHandler(async (req, res) => {
  const alunos = service.listarAlunos({ curso: req.query.curso as string });
  res.json(alunos);
}));

router.get("/:id", asyncHandler(async (req, res) => {
  const aluno = service.buscarAluno(Number(req.params.id));
  res.json(aluno);
}));

router.post("/", validar(AlunoSchema), asyncHandler(async (req, res) => {
  const aluno = service.criarAluno(req.body);
  res.status(201).json(aluno);
}));

router.put("/:id", asyncHandler(async (req, res) => {
  const aluno = service.atualizarAluno(Number(req.params.id), req.body);
  res.json(aluno);
}));

router.delete("/:id", asyncHandler(async (req, res) => {
  service.removerAluno(Number(req.params.id));
  res.status(204).send();
}));

export default router;
```

---

## 6. Estrutura Final do Projeto

```
api-alunos/
  data/
    alunos.json
  src/
    server.ts
    routes/
      alunos.ts
    services/
      aluno-service.ts
    data/
      aluno-repository.ts
    schemas/
      aluno.ts              # Schemas Zod
    middlewares/
      logger.ts
      validar.ts
      error-handler.ts
    errors/
      api-error.ts
    utils/
      async-handler.ts
  package.json
  tsconfig.json
```

---

## 7. Respostas Padronizadas

Padronize todas as respostas da API:

```typescript
// Sucesso
{ "dados": { ... } }
{ "dados": [{ ... }, { ... }] }

// Erro
{
  "erro": "Mensagem do erro",
  "detalhes": [...]  // opcional
}

// Criacao
// Status 201 + { "dados": { ... } }

// Exclusao
// Status 204 (sem body)
```

---

## 8. Git - Pull Requests

Agora que a API esta madura, pratique Pull Requests:

```bash
# Crie uma branch para a feature
git checkout -b feature/validacao-zod

# Implemente, commite...
git add .
git commit -m "Adiciona validacao com Zod e middleware de validacao"

# Push da branch
git push -u origin feature/validacao-zod
```

No GitHub:
1. Abra um Pull Request
2. Descreva o que foi feito
3. Revise o codigo
4. Faca o merge

---

## Exercicios Praticos

### Exercicio 1 - Validacao completa
Adicione validacao Zod em todas as rotas da API de alunos:
1. Crie schemas para criacao e atualizacao
2. Aplique o middleware `validar` nas rotas POST e PUT
3. Teste com dados validos e invalidos

### Exercicio 2 - Erros customizados
1. Implemente `ApiError`, `NotFoundError` e `BadRequestError`
2. Crie o middleware `errorHandler`
3. Aplique em todas as rotas
4. Teste: buscar ID inexistente, criar com email duplicado

### Exercicio 3 - Camada de servicos
1. Refatore a API separando a logica em `services/`
2. As rotas devem apenas chamar o servico e retornar a resposta
3. Adicione regra: aluno so pode ser removido se nota for < 3

### Exercicio 4 - API de Tarefas completa
Crie uma API completa de tarefas (to-do) com:
1. Schema Zod para Tarefa (titulo, descricao?, prioridade: "baixa"|"media"|"alta", concluida)
2. CRUD completo com persistencia JSON
3. Validacao em todas as rotas
4. Tratamento de erros padronizado
5. Filtros: por prioridade, por status (concluida/pendente)
6. Organize em routes/, services/, data/, schemas/, middlewares/

---

## Dica: IA e Validacao

1. **Gerar schemas Zod a partir de interfaces:**
   Peca ao Copilot: *"Converta essa interface TypeScript em um schema Zod com validacoes apropriadas"*

2. **Descobrir validacoes:**
   *"Quais validacoes o Zod oferece para strings? (email, url, uuid, etc.)"*

3. **Revisar seguranca:**
   Selecione suas rotas e peca: *"Quais validacoes estao faltando nessas rotas?"*

> **Reflexao:** Validacao e a primeira linha de defesa da sua API. Toda vez que voce aceitar dados sem validar, esta confiando que o cliente enviou algo correto - e isso raramente e verdade.

---

## Leitura Complementar

- [Zod - Documentacao](https://zod.dev/)
- [Express - Error Handling](https://expressjs.com/en/guide/error-handling.html)
