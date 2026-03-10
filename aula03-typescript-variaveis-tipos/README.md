# Aula 03 - TypeScript: Variaveis, Constantes, Operadores e Expressoes

## Objetivos da Aula

- Entender o que e TypeScript e por que usar em vez de JavaScript puro
- Configurar um projeto TypeScript com Node.js
- Declarar variaveis e constantes com tipos explicitos
- Utilizar operadores aritmeticos, logicos e de comparacao
- Construir expressoes e entender precedencia de operadores

---

## 1. O que e TypeScript?

TypeScript e um **superset** do JavaScript criado pela Microsoft. Isso significa que todo codigo JavaScript valido tambem e TypeScript valido, mas TypeScript adiciona **tipagem estatica**.

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
somar("5", 3);     // ERRO! Argumento string nao e number
```

> **Comparando com C:** TypeScript traz para o JavaScript uma seguranca de tipos semelhante a que voces ja conhecem do C. A diferenca e que TypeScript verifica os tipos **em tempo de compilacao**, nao em tempo de execucao.

---

## 2. Configurando o Ambiente TypeScript

### 2.1 Criando o projeto

```bash
# Crie a pasta e inicialize
mkdir aula03-pratica
cd aula03-pratica
npm init -y

# Instale o TypeScript como dependencia de desenvolvimento
npm install -D typescript

# Instale os tipos do Node.js
npm install -D @types/node

# Crie o arquivo de configuracao do TypeScript
npx tsc --init
```

### 2.2 Configurando o tsconfig.json

Abra o `tsconfig.json` gerado e ajuste as opcoes principais:

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

| Opcao | Significado |
|-------|-------------|
| `target` | Versao do JS gerado |
| `outDir` | Pasta de saida dos arquivos compilados |
| `rootDir` | Pasta dos arquivos fonte (.ts) |
| `strict` | Ativa todas as verificacoes rigorosas de tipo |

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

### 2.5 Alternativa rapida: tsx

Para desenvolvimento, voce pode usar o `tsx` que executa TypeScript diretamente:

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

> Isso elimina a etapa de compilacao durante o desenvolvimento. O `watch` reinicia ao salvar.

---

## 3. Variaveis e Constantes com Tipos

### 3.1 Tipos primitivos

```typescript
// String - texto
let nome: string = "Ana";

// Number - numeros inteiros e decimais
let idade: number = 21;
let altura: number = 1.72;

// Boolean - verdadeiro ou falso
let aprovado: boolean = true;
```

> **Comparando com C:**
> - C: `int`, `float`, `double`, `char` -> TS: `number` e `string`
> - C: `int aprovado = 1;` -> TS: `let aprovado: boolean = true;`

### 3.2 Inferencia de tipos

TypeScript consegue **inferir** o tipo automaticamente:

```typescript
// Com tipo explicito
let cidade: string = "Sao Paulo";

// Com inferencia (TypeScript sabe que e string)
let estado = "SP";

// Ambas as formas sao validas. Use tipo explicito quando:
// - A inferencia nao e obvia
// - Voce quer documentar a intencao
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

// const nao pode ser reatribuido
// PI = 3.14;  // ERRO! Cannot assign to 'PI' because it is a constant
```

**Regra:** Use `const` por padrao. So use `let` quando **realmente** precisar reatribuir o valor.

### 3.4 Tipos especiais

```typescript
// any - desabilita a verificacao de tipo (evite!)
let qualquerCoisa: any = "texto";
qualquerCoisa = 42;     // sem erro, mas perde a seguranca

// void - ausencia de retorno (como void em C)
function exibirMensagem(): void {
  console.log("Ola!");
}

// null e undefined
let vazio: null = null;
let indefinido: undefined = undefined;
```

> **Dica:** Evite `any` ao maximo. Se precisar de flexibilidade, use `unknown` (veremos na Aula 08).

---

## 4. Operadores

### 4.1 Operadores aritmeticos

```typescript
const a: number = 10;
const b: number = 3;

console.log(a + b);   // 13   (soma)
console.log(a - b);   // 7    (subtracao)
console.log(a * b);   // 30   (multiplicacao)
console.log(a / b);   // 3.33 (divisao - sempre decimal!)
console.log(a % b);   // 1    (resto da divisao / modulo)
console.log(a ** b);  // 1000 (exponenciacao: 10^3)
```

> **Diferenca do C:** Em C, `10 / 3` resulta em `3` (divisao inteira). Em TypeScript, `10 / 3` resulta em `3.3333...`. Para obter divisao inteira, use `Math.floor(10 / 3)`.

### 4.2 Operadores de atribuicao

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

### 4.3 Operadores de comparacao

```typescript
const x: number = 10;
const y: number = 20;

console.log(x > y);    // false
console.log(x < y);    // true
console.log(x >= 10);  // true
console.log(x <= 9);   // false
console.log(x === 10); // true  (igualdade estrita)
console.log(x !== y);  // true  (diferenca estrita)
```

> **IMPORTANTE:** Use sempre `===` e `!==` (igualdade estrita). Nunca use `==` e `!=` (igualdade frouxa), pois fazem conversao de tipo automatica e geram bugs:
> ```typescript
> // JavaScript/TypeScript
> 0 == ""     // true  (WAT?!)
> 0 === ""    // false (correto)
> ```

### 4.4 Operadores logicos

```typescript
const a: boolean = true;
const b: boolean = false;

console.log(a && b);   // false (E logico - ambos precisam ser true)
console.log(a || b);   // true  (OU logico - pelo menos um true)
console.log(!a);        // false (negacao)
```

| Operador | C | TypeScript |
|----------|---|------------|
| E logico | `&&` | `&&` |
| OU logico | `\|\|` | `\|\|` |
| Negacao | `!` | `!` |

### 4.5 Operador ternario

```typescript
const idade: number = 18;

// Sintaxe: condicao ? valor_se_true : valor_se_false
const status: string = idade >= 18 ? "Maior de idade" : "Menor de idade";

console.log(status);  // "Maior de idade"
```

Funciona exatamente como em C.

---

## 5. Expressoes e Precedencia

### 5.1 Expressoes

Uma **expressao** e qualquer trecho de codigo que produz um valor:

```typescript
// Expressoes aritmeticas
const total: number = (10 + 5) * 2 - 3;  // 27

// Expressoes com strings
const saudacao: string = "Ola, " + "mundo!";

// Expressoes logicas
const podeVotar: boolean = idade >= 16 && idade < 70;
```

### 5.2 Precedencia de operadores

A ordem e similar ao C e a matematica:

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

**Dica pratica:** Na duvida, use parenteses para deixar a intencao clara.

```typescript
// Sem parenteses - funciona mas e confuso
const resultado = 2 + 3 * 4 > 10 && true;

// Com parenteses - muito mais claro
const resultado = ((2 + (3 * 4)) > 10) && true;
```

---

## 6. Conversao de Tipos

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

> **Comparando com C:** Em C voce usa `atoi()`, `atof()`, ou casting `(int)`. Em TypeScript, usa `Number()`, `parseInt()`, `parseFloat()`.

---

## 7. Pratica com Git

Ao terminar cada exercicio, pratique o fluxo Git:

```bash
git add src/exercicio1.ts
git commit -m "Resolve exercicio 1: calculadora com tipos"
```

**Lembre-se:** Cada exercicio pode ser um commit separado. Isso treina o habito e cria um historico limpo.

---

## Exercicios Praticos

### Exercicio 1 - Setup TypeScript
1. Crie um projeto TypeScript seguindo a secao 2
2. No `src/index.ts`, declare variaveis tipadas para: nome, idade, curso, media
3. Exiba tudo formatado com template literals
4. Compile e execute com `npm run dev`
5. Commit: `"Configura projeto TypeScript e cria perfil tipado"`

### Exercicio 2 - Calculadora tipada
Crie `src/calculadora.ts` que:
1. Declare duas constantes `number`
2. Calcule e exiba: soma, subtracao, multiplicacao, divisao, resto e potencia
3. Use `Math.floor()` para exibir tambem a divisao inteira
4. Formate a saida com 2 casas decimais usando `.toFixed(2)`

### Exercicio 3 - Conversor de unidades
Crie `src/conversor.ts` que:
1. Declare uma distancia em **quilometros** (const)
2. Converta para: metros, centimetros, milhas e pes
3. Exiba todas as conversoes formatadas

### Exercicio 4 - Expressoes logicas
Crie `src/expressoes.ts` que:
1. Declare variaveis para: idade, temCNH, temMultas
2. Crie expressoes logicas que determinem:
   - Pode dirigir? (idade >= 18 E temCNH E NAO temMultas)
   - Pode votar? (idade >= 16)
   - E adolescente? (idade >= 12 E idade < 18)
3. Exiba os resultados

---

## Dica: GitHub Copilot para TypeScript

O Copilot funciona muito bem com TypeScript por conta da tipagem. Experimente:

1. **Autocomplete de tipos:** Comece a digitar uma variavel e veja o Copilot sugerir o tipo correto
2. **Documentacao inline:** Escreva um comentario e o Copilot gera o codigo:
   ```typescript
   // Converte quilometros para milhas (1 km = 0.621371 mi)
   // <Tab para aceitar a sugestao do Copilot>
   ```
3. **Pergunte ao Copilot Chat:**
   - *"O que significa 'strict: true' no tsconfig.json?"*
   - *"Qual a diferenca entre Number() e parseInt()?"*

> **Exercicio com IA:** Resolva o Exercicio 4 sozinho. Depois, peca ao Copilot Chat para revisar seu codigo e sugerir melhorias. Compare as abordagens.

---

## Leitura Complementar

- [TypeScript - Documentacao Oficial](https://www.typescriptlang.org/docs/)
- [TypeScript for JavaScript Programmers](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)
