# Aula 10 - Manipulacao de Arquivos

## Objetivos da Aula

- Ler e escrever arquivos de texto com o modulo `fs` do Node.js
- Trabalhar com arquivos JSON como "banco de dados" simples
- Entender operacoes sincronas vs assincronas
- Manipular caminhos com o modulo `path`
- Implementar persistencia de dados em arquivo

---

## 1. O Modulo fs (File System)

O Node.js tem um modulo nativo para manipulacao de arquivos:

```typescript
import fs from "fs";
import path from "path";
```

> **Comparando com C:** Em C voce usa `fopen()`, `fread()`, `fwrite()`, `fclose()`. Em Node.js, o modulo `fs` oferece funcionalidades equivalentes, mas com APIs mais modernas.

---

## 2. Leitura de Arquivos

### 2.1 Leitura sincrona

```typescript
import fs from "fs";

// Ler arquivo de texto
const conteudo: string = fs.readFileSync("dados.txt", "utf-8");
console.log(conteudo);
```

### 2.2 Leitura assincrona com Promises

```typescript
import fs from "fs/promises";

async function lerArquivo(): Promise<void> {
  const conteudo = await fs.readFile("dados.txt", "utf-8");
  console.log(conteudo);
}

lerArquivo();
```

> **Sincrono vs Assincrono:**
> - **Sincrono** (`readFileSync`): bloqueia a execucao ate terminar. Simples, bom para scripts pequenos.
> - **Assincrono** (`readFile` com `await`): nao bloqueia. Essencial para servidores e aplicacoes que precisam atender multiplas requisicoes.

### 2.3 Verificar se arquivo existe

```typescript
import fs from "fs";

if (fs.existsSync("dados.txt")) {
  const conteudo = fs.readFileSync("dados.txt", "utf-8");
  console.log(conteudo);
} else {
  console.log("Arquivo nao encontrado!");
}
```

---

## 3. Escrita de Arquivos

### 3.1 Escrever (sobrescreve o conteudo)

```typescript
import fs from "fs";

fs.writeFileSync("saida.txt", "Ola, mundo!\nSegunda linha.");
console.log("Arquivo criado com sucesso!");
```

### 3.2 Adicionar ao final (append)

```typescript
fs.appendFileSync("log.txt", `[${new Date().toISOString()}] Evento registrado\n`);
```

### 3.3 Versao assincrona

```typescript
import fs from "fs/promises";

async function escrever(): Promise<void> {
  await fs.writeFile("saida.txt", "Conteudo assincrono");
  console.log("Arquivo salvo!");
}
```

---

## 4. Trabalhando com JSON

A forma mais pratica de persistir dados estruturados em arquivo:

### 4.1 Salvando dados em JSON

```typescript
import fs from "fs";

interface Aluno {
  id: number;
  nome: string;
  nota: number;
}

const alunos: Aluno[] = [
  { id: 1, nome: "Ana", nota: 8.5 },
  { id: 2, nome: "Bruno", nota: 7.0 },
  { id: 3, nome: "Carlos", nota: 9.2 },
];

// Converter para JSON e salvar
const json: string = JSON.stringify(alunos, null, 2);
fs.writeFileSync("alunos.json", json, "utf-8");
```

O segundo parametro `null` e o replacer, e `2` sao os espacos de indentacao.

### 4.2 Lendo dados do JSON

```typescript
// Ler o JSON e converter para objeto
const dados: string = fs.readFileSync("alunos.json", "utf-8");
const alunos: Aluno[] = JSON.parse(dados);

console.log(alunos[0].nome);  // "Ana"
```

### 4.3 CRUD completo com JSON

```typescript
import fs from "fs";
import path from "path";

interface Tarefa {
  id: number;
  titulo: string;
  concluida: boolean;
}

const ARQUIVO = path.join(__dirname, "tarefas.json");

// Ler todas as tarefas
function lerTarefas(): Tarefa[] {
  if (!fs.existsSync(ARQUIVO)) {
    return [];
  }
  const dados = fs.readFileSync(ARQUIVO, "utf-8");
  return JSON.parse(dados);
}

// Salvar tarefas
function salvarTarefas(tarefas: Tarefa[]): void {
  fs.writeFileSync(ARQUIVO, JSON.stringify(tarefas, null, 2), "utf-8");
}

// Adicionar tarefa
function adicionar(titulo: string): Tarefa {
  const tarefas = lerTarefas();
  const novaTarefa: Tarefa = {
    id: tarefas.length > 0 ? tarefas[tarefas.length - 1].id + 1 : 1,
    titulo,
    concluida: false,
  };
  tarefas.push(novaTarefa);
  salvarTarefas(tarefas);
  return novaTarefa;
}

// Listar tarefas
function listar(): void {
  const tarefas = lerTarefas();
  tarefas.forEach((t) => {
    const status = t.concluida ? "[x]" : "[ ]";
    console.log(`${status} ${t.id}. ${t.titulo}`);
  });
}

// Concluir tarefa
function concluir(id: number): void {
  const tarefas = lerTarefas();
  const tarefa = tarefas.find((t) => t.id === id);
  if (tarefa) {
    tarefa.concluida = true;
    salvarTarefas(tarefas);
    console.log(`Tarefa "${tarefa.titulo}" concluida!`);
  } else {
    console.log("Tarefa nao encontrada.");
  }
}

// Remover tarefa
function remover(id: number): void {
  let tarefas = lerTarefas();
  tarefas = tarefas.filter((t) => t.id !== id);
  salvarTarefas(tarefas);
  console.log(`Tarefa ${id} removida.`);
}

// Uso
adicionar("Estudar TypeScript");
adicionar("Fazer exercicios");
adicionar("Commitar no GitHub");
listar();
concluir(1);
listar();
```

---

## 5. Manipulando Caminhos com path

```typescript
import path from "path";

// Juntar partes de um caminho (resolve separadores do SO)
const arquivo = path.join("src", "models", "aluno.ts");
// Linux/Mac: "src/models/aluno.ts"
// Windows:   "src\\models\\aluno.ts"

// Caminho absoluto
const absoluto = path.resolve("src", "index.ts");
// "/Users/felipe/.../src/index.ts"

// Extrair partes
console.log(path.basename("src/models/aluno.ts"));   // "aluno.ts"
console.log(path.extname("aluno.ts"));                // ".ts"
console.log(path.dirname("src/models/aluno.ts"));     // "src/models"

// __dirname - diretorio do arquivo atual
console.log(__dirname);
```

---

## 6. Manipulando Diretorios

```typescript
import fs from "fs";

// Criar diretorio
if (!fs.existsSync("output")) {
  fs.mkdirSync("output");
}

// Criar diretorios aninhados
fs.mkdirSync("output/relatorios/2026", { recursive: true });

// Listar conteudo de um diretorio
const arquivos: string[] = fs.readdirSync("src");
console.log(arquivos);  // ["index.ts", "models", "services"]

// Remover arquivo
fs.unlinkSync("arquivo-temporario.txt");
```

---

## 7. Tratamento de Erros

```typescript
import fs from "fs";

try {
  const dados = fs.readFileSync("nao-existe.txt", "utf-8");
  console.log(dados);
} catch (erro) {
  if (erro instanceof Error) {
    console.log(`Erro ao ler arquivo: ${erro.message}`);
  }
}
```

Versao assincrona:

```typescript
import fs from "fs/promises";

async function lerComSeguranca(caminho: string): Promise<string | null> {
  try {
    return await fs.readFile(caminho, "utf-8");
  } catch {
    console.log(`Arquivo "${caminho}" nao encontrado.`);
    return null;
  }
}
```

---

## 8. Exemplo Completo - Gerador de Relatorio

```typescript
import fs from "fs";
import path from "path";

interface Venda {
  produto: string;
  quantidade: number;
  precoUnitario: number;
}

// Ler vendas do JSON
const vendas: Venda[] = JSON.parse(
  fs.readFileSync(path.join(__dirname, "vendas.json"), "utf-8")
);

// Gerar relatorio em texto
let relatorio = "=== RELATORIO DE VENDAS ===\n\n";

let totalGeral = 0;
vendas.forEach((venda, i) => {
  const subtotal = venda.quantidade * venda.precoUnitario;
  totalGeral += subtotal;
  relatorio += `${i + 1}. ${venda.produto}\n`;
  relatorio += `   Qtd: ${venda.quantidade} x R$ ${venda.precoUnitario.toFixed(2)}\n`;
  relatorio += `   Subtotal: R$ ${subtotal.toFixed(2)}\n\n`;
});

relatorio += `TOTAL GERAL: R$ ${totalGeral.toFixed(2)}\n`;

// Salvar relatorio
const outputDir = path.join(__dirname, "relatorios");
if (!fs.existsSync(outputDir)) {
  fs.mkdirSync(outputDir);
}

const nomeArquivo = `relatorio-${new Date().toISOString().split("T")[0]}.txt`;
fs.writeFileSync(path.join(outputDir, nomeArquivo), relatorio, "utf-8");

console.log(`Relatorio salvo em: relatorios/${nomeArquivo}`);
```

---

## 9. Git - Ignorando arquivos gerados

Adicione ao `.gitignore` arquivos que sao **gerados** pelo programa:

```gitignore
# Dados gerados
*.log
relatorios/
output/

# Mas COMMITE os dados de exemplo:
# !vendas.json  (se quiser forcar a inclusao)
```

```bash
git add src/ vendas.json
git commit -m "Implementa gerador de relatorio de vendas"
```

---

## Exercicios Praticos

### Exercicio 1 - Logger simples
Crie `src/logger.ts`:
1. Funcao `log(mensagem: string): void` que adiciona ao arquivo `app.log`
2. Cada linha deve ter timestamp: `[2026-03-10 14:30:00] Mensagem aqui`
3. Funcao `lerLogs(): string[]` que retorna todas as linhas do log
4. Funcao `limparLogs(): void` que apaga o conteudo

### Exercicio 2 - Agenda persistente
Evolua a agenda da Aula 07 para persistir em `contatos.json`:
1. Funcoes: adicionar, remover, buscar, listar
2. Todas as operacoes devem ler e salvar no JSON
3. Os dados devem sobreviver ao reiniciar o programa

### Exercicio 3 - Conversor CSV para JSON
Crie `src/csv-to-json.ts` que:
1. Le um arquivo CSV (ex: `alunos.csv` com nome, nota, faltas)
2. Converte para array de objetos tipados
3. Salva como JSON
4. Bonus: crie tambem o conversor inverso (JSON para CSV)

### Exercicio 4 - Sistema de configuracao
Crie `src/config.ts`:
1. Interface `Config` com propriedades da aplicacao
2. Funcao `carregarConfig()` que le de `config.json` (ou cria com valores padrao)
3. Funcao `salvarConfig(config: Config)` que persiste as alteracoes
4. Funcao `resetarConfig()` que restaura os valores padrao

---

## Dica: IA e Manipulacao de Arquivos

1. **Gerar dados de teste:** Peca ao Copilot: *"Gere um JSON com 20 produtos de uma loja virtual com nome, preco, categoria e estoque"*

2. **Converter formatos:** *"Como leio um arquivo CSV e converto para JSON em Node.js?"*

3. **Tratar erros:** Selecione seu codigo de leitura e peca: *"Adicione tratamento de erros robusto para esse codigo"*

> **Importante:** Ao manipular arquivos, sempre trate erros. Um arquivo pode nao existir, estar corrompido, ou voce pode nao ter permissao de escrita.

---

## Leitura Complementar

- [Node.js - File System](https://nodejs.org/docs/latest/api/fs.html)
- [Node.js - Path](https://nodejs.org/docs/latest/api/path.html)
