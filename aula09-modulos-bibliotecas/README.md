# Aula 09 - Modulos e Bibliotecas Externas

## Objetivos da Aula

- Entender o sistema de modulos do JavaScript/TypeScript (import/export)
- Organizar codigo em multiplos arquivos
- Utilizar o npm para instalar e gerenciar bibliotecas externas
- Conhecer bibliotecas uteis do ecossistema Node.js
- Entender o package.json e o package-lock.json

---

## 1. Por que Modulos?

Conforme o projeto cresce, colocar tudo em um unico arquivo se torna insustentavel. Modulos permitem:

- **Organizar** codigo em arquivos separados por responsabilidade
- **Reutilizar** funcoes e tipos entre diferentes partes do projeto
- **Encapsular** detalhes internos, expondo apenas o necessario

> **Comparando com C:** Em C, voce usa `#include "header.h"` para incluir declaracoes de outros arquivos. Em TypeScript, usamos `import`/`export`.

---

## 2. Export e Import

### 2.1 Named exports (exportacoes nomeadas)

```typescript
// src/matematica.ts
export function somar(a: number, b: number): number {
  return a + b;
}

export function subtrair(a: number, b: number): number {
  return a - b;
}

export const PI: number = 3.14159;
```

```typescript
// src/index.ts
import { somar, subtrair, PI } from "./matematica";

console.log(somar(5, 3));   // 8
console.log(PI);             // 3.14159
```

### 2.2 Default export

Cada modulo pode ter **um** export padrao:

```typescript
// src/calculadora.ts
export default class Calculadora {
  somar(a: number, b: number): number {
    return a + b;
  }

  subtrair(a: number, b: number): number {
    return a - b;
  }
}
```

```typescript
// src/index.ts
import Calculadora from "./calculadora";  // sem chaves

const calc = new Calculadora();
console.log(calc.somar(10, 5));
```

### 2.3 Renomeando imports

```typescript
import { somar as add, subtrair as sub } from "./matematica";

console.log(add(5, 3));  // 8
```

### 2.4 Import tudo como namespace

```typescript
import * as math from "./matematica";

console.log(math.somar(5, 3));
console.log(math.PI);
```

### 2.5 Re-exportando

```typescript
// src/utils/index.ts - barrel file
export { somar, subtrair } from "./matematica";
export { formatar } from "./formatacao";
export { validar } from "./validacao";
```

```typescript
// src/main.ts - import limpo
import { somar, formatar, validar } from "./utils";
```

---

## 3. Organizando um Projeto

### 3.1 Estrutura recomendada

```
meu-projeto/
  src/
    index.ts              # Ponto de entrada
    models/
      aluno.ts            # Interface Aluno
      produto.ts          # Interface Produto
    services/
      aluno-service.ts    # Logica de negocio (alunos)
      produto-service.ts  # Logica de negocio (produtos)
    utils/
      formatacao.ts       # Funcoes utilitarias
      validacao.ts        # Funcoes de validacao
      index.ts            # Barrel file
  package.json
  tsconfig.json
```

### 3.2 Exemplo pratico

```typescript
// src/models/aluno.ts
export interface Aluno {
  nome: string;
  ra: string;
  notas: number[];
}
```

```typescript
// src/services/aluno-service.ts
import { Aluno } from "../models/aluno";

export function calcularMedia(aluno: Aluno): number {
  const soma = aluno.notas.reduce((acc, nota) => acc + nota, 0);
  return soma / aluno.notas.length;
}

export function estaAprovado(aluno: Aluno): boolean {
  return calcularMedia(aluno) >= 6;
}
```

```typescript
// src/index.ts
import { Aluno } from "./models/aluno";
import { calcularMedia, estaAprovado } from "./services/aluno-service";

const aluno: Aluno = {
  nome: "Ana",
  ra: "2024001",
  notas: [8.0, 7.5, 9.0],
};

console.log(`Media: ${calcularMedia(aluno).toFixed(1)}`);
console.log(`Aprovado: ${estaAprovado(aluno) ? "Sim" : "Nao"}`);
```

---

## 4. npm - Gerenciador de Pacotes

### 4.1 Instalando pacotes

```bash
# Dependencia de producao
npm install pacote-nome

# Dependencia de desenvolvimento
npm install -D pacote-nome

# Exemplos
npm install uuid             # Gerar IDs unicos
npm install -D tsx           # Executar TypeScript diretamente
npm install -D @types/uuid   # Tipos para o uuid
```

### 4.2 package.json - dependencias

Apos instalar, o `package.json` e atualizado:

```json
{
  "dependencies": {
    "uuid": "^9.0.0"
  },
  "devDependencies": {
    "typescript": "^5.3.0",
    "@types/node": "^20.0.0",
    "@types/uuid": "^9.0.0",
    "tsx": "^4.0.0"
  }
}
```

| Secao | Quando usar |
|-------|-------------|
| `dependencies` | Pacotes necessarios em producao |
| `devDependencies` | Ferramentas usadas apenas no desenvolvimento |

### 4.3 node_modules e package-lock.json

- **node_modules/**: pasta com todo o codigo dos pacotes instalados (NUNCA commite no Git!)
- **package-lock.json**: registra as versoes exatas instaladas (SEMPRE commite no Git!)

```gitignore
# .gitignore
node_modules/
dist/
```

### 4.4 Restaurando dependencias

Quando clonar um projeto, execute:

```bash
npm install
```

Isso le o `package.json` e reinstala tudo na `node_modules/`.

---

## 5. Bibliotecas Uteis

### 5.1 uuid - Gerar IDs unicos

```bash
npm install uuid
npm install -D @types/uuid
```

```typescript
import { v4 as gerarId } from "uuid";

const id: string = gerarId();
console.log(id);  // "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"
```

### 5.2 date-fns - Manipulacao de datas

```bash
npm install date-fns
```

```typescript
import { format, addDays, differenceInDays } from "date-fns";
import { ptBR } from "date-fns/locale";

const hoje = new Date();
console.log(format(hoje, "dd/MM/yyyy"));              // "10/03/2026"
console.log(format(hoje, "EEEE, dd 'de' MMMM", { locale: ptBR }));
// "terca-feira, 10 de marco"

const proximaSemana = addDays(hoje, 7);
console.log(differenceInDays(proximaSemana, hoje));    // 7
```

### 5.3 chalk - Cores no terminal

```bash
npm install chalk
```

```typescript
import chalk from "chalk";

console.log(chalk.green("Sucesso!"));
console.log(chalk.red.bold("Erro critico!"));
console.log(chalk.blue.underline("Informacao"));
```

### 5.4 zod - Validacao de dados

```bash
npm install zod
```

```typescript
import { z } from "zod";

const AlunoSchema = z.object({
  nome: z.string().min(2),
  idade: z.number().min(16).max(100),
  email: z.string().email(),
});

// Validar dados
const resultado = AlunoSchema.safeParse({
  nome: "Ana",
  idade: 21,
  email: "ana@email.com",
});

if (resultado.success) {
  console.log("Dados validos:", resultado.data);
} else {
  console.log("Erros:", resultado.error.issues);
}
```

> O `zod` sera muito util nas aulas de API (14).

---

## 6. Modulos Built-in do Node.js

O Node.js vem com modulos prontos que nao precisam de instalacao:

```typescript
// path - manipulacao de caminhos
import path from "path";

const caminho = path.join("src", "models", "aluno.ts");
console.log(caminho);  // "src/models/aluno.ts"

// os - informacoes do sistema
import os from "os";
console.log(os.platform());  // "darwin", "linux", "win32"
console.log(os.homedir());   // "/Users/usuario"
```

> O modulo `fs` (file system) sera tema da proxima aula.

---

## 7. Git - .gitignore robusto e dependencias

Atualize seu `.gitignore` conforme o projeto cresce:

```gitignore
# Dependencias
node_modules/

# Build
dist/

# Ambiente
.env
.env.local

# Sistema
.DS_Store
Thumbs.db

# IDE
.vscode/settings.json
```

**Importante:** Sempre commite o `package-lock.json`! Ele garante que todos instalem as mesmas versoes.

```bash
git add package.json package-lock.json
git commit -m "Adiciona dependencias uuid e date-fns"
```

---

## Exercicios Praticos

### Exercicio 1 - Refatorar em modulos
Pegue o exercicio do catalogo de produtos (Aula 07) e:
1. Separe em arquivos: `models/produto.ts`, `services/produto-service.ts`, `index.ts`
2. Exporte e importe corretamente
3. Mantenha a mesma funcionalidade

### Exercicio 2 - Gerador de IDs
Crie um modulo `utils/id-generator.ts` que:
1. Use a biblioteca `uuid` para gerar IDs
2. Exporte funcao `gerarId(): string`
3. Crie uma interface `Entidade` com campo `id: string`
4. Crie funcao `criarAluno(nome, notas)` que gera o ID automaticamente

### Exercicio 3 - Formatador de datas
Crie `utils/data.ts` usando `date-fns`:
1. `formatarData(data: Date): string` - formato "dd/MM/yyyy"
2. `calcularIdade(nascimento: Date): number`
3. `diasAteProvaFinal(dataProva: Date): number`
4. Integre com a interface Aluno adicionando campo `nascimento: Date`

### Exercicio 4 - Validador com Zod
Crie `validators/aluno-validator.ts` usando Zod:
1. Schema de validacao para Aluno (nome, email, idade, notas)
2. Funcao que valida dados de entrada e retorna erros formatados
3. Teste com dados validos e invalidos

---

## Dica: IA e Bibliotecas

O Copilot conhece as APIs das bibliotecas mais populares:

1. **Descobrir metodos:** Escreva o import e comece a usar - o Copilot sugere metodos disponiveis
2. **Pergunte ao Chat:** *"Quais funcoes do date-fns sao uteis para calcular diferenca entre datas?"*
3. **Migrar entre bibliotecas:** *"Converta esse codigo que usa moment.js para date-fns"*

> **Cuidado:** Bibliotecas mudam entre versoes. Se o Copilot sugerir algo que nao funciona, pode ser uma API antiga. Sempre consulte a documentacao oficial para confirmar.

---

## Leitura Complementar

- [TypeScript - Modules](https://www.typescriptlang.org/docs/handbook/2/modules.html)
- [npm Docs](https://docs.npmjs.com/)
- [Node.js Built-in Modules](https://nodejs.org/docs/latest/api/)
