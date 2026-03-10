# Aula 03 - TypeScript: Variáveis, Constantes, Operadores e Expressões

## Objetivos da Aula

- Entender o que é TypeScript e por que usar em vez de JavaScript puro
- Configurar um projeto TypeScript com Node.js
- Declarar variáveis e constantes com tipos explícitos
- Utilizar operadores aritméticos, lógicos e de comparação
- Construir expressões e entender precedência de operadores

---

## 1. O que é TypeScript?

TypeScript é um **superset** do JavaScript criado pela Microsoft. Isso significa que todo código JavaScript válido também é TypeScript válido, mas TypeScript adiciona **tipagem estática**.

### Por que TypeScript?

```javascript
// JavaScript - sem tipos
function somar(a, b) {
  return a + b;
}

somar(5, 3);       // 8 (ok)
somar("5", 3);     // "53" (ops! concatenou strings)
somar(true, []);    // "true" (????)
```

```typescript
// TypeScript - com tipos
function somar(a: number, b: number): number {
  return a + b;
}

somar(5, 3);       // 8 (ok)
somar("5", 3);     // ERRO! Argumento string não é number
```

> **Comparando com C:** TypeScript traz para o JavaScript uma segurança de tipos semelhante a que vocês já conhecem do C. A diferença é que TypeScript verifica os tipos **em tempo de compilação**, não em tempo de execução.

---

## 2. Configurando o Ambiente TypeScript

### 2.1 Criando o projeto

```bash
# Crie a pasta e inicialize
mkdir aula03-pratica
cd aula03-pratica
npm init -y

# Instale o TypeScript como dependência de desenvolvimento
npm install -D typescript

# Instale os tipos do Node.js
npm install -D @types/node

# Crie o arquivo de configuração do TypeScript
npx tsc --init
```

### 2.2 Configurando o tsconfig.json

Abra o `tsconfig.json` gerado e ajuste as opções principais:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"]
}
```

| Opção | Significado |
|-------|-------------|
| `target` | Versão do JS gerado |
| `outDir` | Pasta de saída dos arquivos compilados |
| `rootDir` | Pasta dos arquivos fonte (.ts) |
| `strict` | Ativa todas as verificações rigorosas de tipo |

### 2.3 Estrutura do projeto

```
aula03-pratica/
  src/
    index.ts
  dist/           (gerado automaticamente)
  package.json
  tsconfig.json
```

### 2.4 Scripts no package.json

```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsc && node dist/index.js"
  }
}
```

### 2.5 Alternativa rápida: tsx

Para desenvolvimento, você pode usar o `tsx` que executa TypeScript diretamente:

```bash
npm install -D tsx
```

```json
{
  "scripts": {
    "dev": "tsx watch src/index.ts"
  }
}
```

> Isso elimina a etapa de compilação durante o desenvolvimento. O `watch` reinicia ao salvar.

---

## 3. Variáveis e Constantes com Tipos

### 3.1 Tipos primitivos

```typescript
// String - texto
let nome: string = "Ana";

// Number - números inteiros e decimais
let idade: number = 21;
let altura: number = 1.72;

// Boolean - verdadeiro ou falso
let aprovado: boolean = true;
```

> **Comparando com C:**
> - C: `int`, `float`, `double`, `char` -> TS: `number` e `string`
> - C: `int aprovado = 1;` -> TS: `let aprovado: boolean = true;`

### 3.2 Inferência de tipos

TypeScript consegue **inferir** o tipo automaticamente:

```typescript
// Com tipo explícito
let cidade: string = "São Paulo";

// Com inferência (TypeScript sabe que é string)
let estado = "SP";

// Ambas as formas são válidas. Use tipo explícito quando:
// - A inferência não é óbvia
// - Você quer documentar a intenção
```

### 3.3 const vs let

```typescript
// const - valor que NUNCA muda (constante)
const PI: number = 3.14159;
const NOME_CURSO: string = "ADS";

// let - valor que PODE mudar
let contador: number = 0;
contador = 1;  // ok
contador = 2;  // ok

// const não pode ser reatribuído
// PI = 3.14;  // ERRO! Cannot assign to 'PI' because it is a constant
```

**Regra:** Use `const` por padrão. Só use `let` quando **realmente** precisar reatribuir o valor.

### 3.4 Tipos especiais

```typescript
// any - desabilita a verificação de tipo (evite!)
let qualquerCoisa: any = "texto";
qualquerCoisa = 42;     // sem erro, mas perde a segurança

// void - ausência de retorno (como void em C)
function exibirMensagem(): void {
  console.log("Olá!");
}

// null e undefined
let vazio: null = null;
let indefinido: undefined = undefined;
```

> **Dica:** Evite `any` ao maximo. Se precisar de flexibilidade, use `unknown` (veremos na Aula 08).

---

## 4. Operadores

### 4.1 Operadores aritméticos

```typescript
const a: number = 10;
const b: number = 3;

console.log(a + b);   // 13   (soma)
console.log(a - b);   // 7    (subtração)
console.log(a * b);   // 30   (multiplicação)
console.log(a / b);   // 3.33 (divisão - sempre decimal!)
console.log(a % b);   // 1    (resto da divisão / módulo)
console.log(a ** b);  // 1000 (exponenciação: 10^3)
```

> **Diferença do C:** Em C, `10 / 3` resulta em `3` (divisão inteira). Em TypeScript, `10 / 3` resulta em `3.3333...`. Para obter divisão inteira, use `Math.floor(10 / 3)`.

### 4.2 Operadores de atribuição

```typescript
let x: number = 10;

x += 5;   // x = x + 5  -> 15
x -= 3;   // x = x - 3  -> 12
x *= 2;   // x = x * 2  -> 24
x /= 4;   // x = x / 4  -> 6
x %= 4;   // x = x % 4  -> 2

// Incremento e decremento (mesmo que em C)
let i: number = 0;
i++;       // i = 1
i--;       // i = 0
```

### 4.3 Operadores de comparação

```typescript
const x: number = 10;
const y: number = 20;

console.log(x > y);    // false
console.log(x < y);    // true
console.log(x >= 10);  // true
console.log(x <= 9);   // false
console.log(x === 10); // true  (igualdade estrita)
console.log(x !== y);  // true  (diferença estrita)
```

> **IMPORTANTE:** Use sempre `===` e `!==` (igualdade estrita). Nunca use `==` e `!=` (igualdade frouxa), pois fazem conversão de tipo automática e geram bugs:
> ```typescript
> // JavaScript/TypeScript
> 0 == ""     // true  (WAT?!)
> 0 === ""    // false (correto)
> ```

### 4.4 Operadores lógicos

```typescript
const a: boolean = true;
const b: boolean = false;

console.log(a && b);   // false (E lógico - ambos precisam ser true)
console.log(a || b);   // true  (OU lógico - pelo menos um true)
console.log(!a);        // false (negação)
```

| Operador | C | TypeScript |
|----------|---|------------|
| E lógico | `&&` | `&&` |
| OU lógico | `\|\|` | `\|\|` |
| Negação | `!` | `!` |

### 4.5 Operador ternário

```typescript
const idade: number = 18;

// Sintaxe: condição ? valor_se_true : valor_se_false
const status: string = idade >= 18 ? "Maior de idade" : "Menor de idade";

console.log(status);  // "Maior de idade"
```

Funciona exatamente como em C.

---

## 5. Expressões e Precedência

### 5.1 Expressões

Uma **expressão** é qualquer trecho de código que produz um valor:

```typescript
// Expressões aritméticas
const total: number = (10 + 5) * 2 - 3;  // 27

// Expressões com strings
const saudacao: string = "Olá, " + "mundo!";

// Expressões lógicas
const podeVotar: boolean = idade >= 16 && idade < 70;
```

### 5.2 Precedência de operadores

A ordem é similar ao C e à matemática:

| Prioridade | Operadores |
|-----------|------------|
| 1 (maior) | `()` parenteses |
| 2 | `**` exponenciacao |
| 3 | `*`, `/`, `%` |
| 4 | `+`, `-` |
| 5 | `<`, `>`, `<=`, `>=` |
| 6 | `===`, `!==` |
| 7 | `&&` |
| 8 | `\|\|` |
| 9 (menor) | `=`, `+=`, `-=` |

**Dica prática:** Na dúvida, use parênteses para deixar a intenção clara.

```typescript
// Sem parênteses - funciona mas é confuso
const resultado = 2 + 3 * 4 > 10 && true;

// Com parênteses - muito mais claro
const resultado = ((2 + (3 * 4)) > 10) && true;
```

---

## 6. Conversão de Tipos

```typescript
// String para Number
const textoIdade: string = "25";
const idade: number = Number(textoIdade);       // 25
const idade2: number = parseInt("25");           // 25 (inteiro)
const altura: number = parseFloat("1.72");       // 1.72 (decimal)

// Number para String
const valor: number = 42;
const texto: string = String(valor);             // "42"
const texto2: string = valor.toString();         // "42"

// Para Boolean
const ativo: boolean = Boolean(1);               // true
const inativo: boolean = Boolean(0);             // false
const vazio: boolean = Boolean("");              // false
const cheio: boolean = Boolean("texto");         // true
```

> **Comparando com C:** Em C você usa `atoi()`, `atof()`, ou casting `(int)`. Em TypeScript, usa `Number()`, `parseInt()`, `parseFloat()`.

---

## 7. Prática com Git

Ao terminar cada exercício, pratique o fluxo Git:

```bash
git add src/exercicio1.ts
git commit -m "Resolve exercício 1: calculadora com tipos"
```

**Lembre-se:** Cada exercício pode ser um commit separado. Isso treina o hábito e cria um histórico limpo.

---

## Exercícios Práticos

### Exercício 1 - Setup TypeScript
1. Crie um projeto TypeScript seguindo a seção 2
2. No `src/index.ts`, declare variáveis tipadas para: nome, idade, curso, média
3. Exiba tudo formatado com template literals
4. Compile e execute com `npm run dev`
5. Commit: `"Configura projeto TypeScript e cria perfil tipado"`

### Exercício 2 - Calculadora tipada
Crie `src/calculadora.ts` que:
1. Declare duas constantes `number`
2. Calcule e exiba: soma, subtração, multiplicação, divisão, resto e potência
3. Use `Math.floor()` para exibir também a divisão inteira
4. Formate a saída com 2 casas decimais usando `.toFixed(2)`

### Exercício 3 - Conversor de unidades
Crie `src/conversor.ts` que:
1. Declare uma distância em **quilômetros** (const)
2. Converta para: metros, centímetros, milhas e pés
3. Exiba todas as conversões formatadas

### Exercício 4 - Expressões lógicas
Crie `src/expressoes.ts` que:
1. Declare variáveis para: idade, temCNH, temMultas
2. Crie expressões lógicas que determinem:
   - Pode dirigir? (idade >= 18 E temCNH E NÃO temMultas)
   - Pode votar? (idade >= 16)
   - É adolescente? (idade >= 12 E idade < 18)
3. Exiba os resultados

---

## Dica: GitHub Copilot para TypeScript

O Copilot funciona muito bem com TypeScript por conta da tipagem. Experimente:

1. **Autocomplete de tipos:** Comece a digitar uma variável e veja o Copilot sugerir o tipo correto
2. **Documentação inline:** Escreva um comentário e o Copilot gera o código:
   ```typescript
   // Converte quilometros para milhas (1 km = 0.621371 mi)
   // <Tab para aceitar a sugestao do Copilot>
   ```
3. **Pergunte ao Copilot Chat:**
   - *"O que significa 'strict: true' no tsconfig.json?"*
   - *"Qual a diferença entre Number() e parseInt()?"*

> **Exercício com IA:** Resolva o Exercício 4 sozinho. Depois, peça ao Copilot Chat para revisar seu código e sugerir melhorias. Compare as abordagens.

---

## Leitura Complementar

- [TypeScript - Documentação Oficial](https://www.typescriptlang.org/docs/)
- [TypeScript for JavaScript Programmers](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)
