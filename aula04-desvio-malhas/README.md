# Aula 04 - Comandos de Desvio e Controle de Malhas

## Objetivos da Aula

- Utilizar estruturas condicionais: if/else, else if, switch
- Implementar laços de repetição: for, while, do-while
- Combinar condicionais e laços para resolver problemas
- Conhecer o for...of e for...in

---

## 1. Estruturas Condicionais

### 1.1 if / else

```typescript
const nota: number = 7.5;

if (nota >= 6) {
  console.log("Aprovado");
} else {
  console.log("Reprovado");
}
```

> **Comparando com C:** A sintaxe é idêntica. A única diferença é que em TypeScript a variável tem tipo anotado.

### 1.2 else if

```typescript
const nota: number = 8.5;

if (nota >= 9) {
  console.log("Conceito A");
} else if (nota >= 7) {
  console.log("Conceito B");
} else if (nota >= 5) {
  console.log("Conceito C");
} else {
  console.log("Conceito D");
}
```

### 1.3 Operador ternário (revisão)

Para condições simples, o ternário é mais conciso:

```typescript
const idade: number = 17;
const categoria: string = idade >= 18 ? "adulto" : "menor";
```

### 1.4 switch

Ideal quando há muitas comparações com um mesmo valor:

```typescript
const diaSemana: number = 3;

switch (diaSemana) {
  case 1:
    console.log("Segunda-feira");
    break;
  case 2:
    console.log("Terça-feira");
    break;
  case 3:
    console.log("Quarta-feira");
    break;
  case 4:
    console.log("Quinta-feira");
    break;
  case 5:
    console.log("Sexta-feira");
    break;
  case 6:
  case 7:
    console.log("Final de semana");
    break;
  default:
    console.log("Dia inválido");
}
```

> **Atenção ao `break`!** Sem ele, a execução "cai" para o próximo case (fall-through), exatamente como em C.

### 1.5 switch com strings

Diferente do C, TypeScript permite `switch` com strings:

```typescript
const operacao: string = "soma";

switch (operacao) {
  case "soma":
    console.log(10 + 5);
    break;
  case "subtracao":
    console.log(10 - 5);
    break;
  case "multiplicacao":
    console.log(10 * 5);
    break;
  default:
    console.log("Operação desconhecida");
}
```

---

## 2. Estruturas de Repetição (Laços/Malhas)

### 2.1 for

```typescript
// Estrutura: for (início; condição; incremento)
for (let i: number = 0; i < 5; i++) {
  console.log(`Iteração ${i}`);
}
// Saida: 0, 1, 2, 3, 4
```

Regressivo:

```typescript
for (let i: number = 10; i >= 0; i--) {
  console.log(i);
}
// Contagem regressiva: 10, 9, 8, ..., 0
```

### 2.2 while

Executa enquanto a condição for verdadeira. Testa **antes** de executar:

```typescript
let contador: number = 0;

while (contador < 5) {
  console.log(`Contador: ${contador}`);
  contador++;
}
```

### 2.3 do...while

Semelhante ao while, mas executa **pelo menos uma vez** (testa depois):

```typescript
let tentativas: number = 0;

do {
  console.log(`Tentativa ${tentativas + 1}`);
  tentativas++;
} while (tentativas < 3);
```

> **Quando usar cada um?**
> - `for`: quando voce sabe o numero de iteracoes
> - `while`: quando depende de uma condicao dinamica
> - `do...while`: quando precisa executar pelo menos uma vez (ex: menus)

### 2.4 break e continue

```typescript
// break - interrompe o laco
for (let i: number = 0; i < 100; i++) {
  if (i === 5) break;
  console.log(i);
}
// Saida: 0, 1, 2, 3, 4

// continue - pula para a próxima iteração
for (let i: number = 0; i < 10; i++) {
  if (i % 2 !== 0) continue;  // pula ímpares
  console.log(i);
}
// Saida: 0, 2, 4, 6, 8
```

### 2.5 for...of (exclusivo do JavaScript/TypeScript)

Itera sobre os **valores** de um iterável (arrays, strings):

```typescript
const frutas: string[] = ["maçã", "banana", "laranja"];

for (const fruta of frutas) {
  console.log(fruta);
}
// maçã, banana, laranja

// Também funciona com strings
const palavra: string = "FATEC";
for (const letra of palavra) {
  console.log(letra);
}
// F, A, T, E, C
```

> Isso não existe em C. É muito mais prático que iterar com índice quando você só precisa dos valores.

---

## 3. Exemplos Praticos Combinados

### 3.1 Tabuada

```typescript
const numero: number = 7;

console.log(`Tabuada do ${numero}:`);
for (let i: number = 1; i <= 10; i++) {
  console.log(`${numero} x ${i} = ${numero * i}`);
}
```

### 3.2 Números primos

```typescript
function ehPrimo(n: number): boolean {
  if (n < 2) return false;
  for (let i: number = 2; i <= Math.sqrt(n); i++) {
    if (n % i === 0) return false;
  }
  return true;
}

// Listar primos de 1 a 50
for (let num: number = 1; num <= 50; num++) {
  if (ehPrimo(num)) {
    console.log(num);
  }
}
```

### 3.3 Menu com do...while

```typescript
let opcao: number;

do {
  console.log("\n=== MENU ===");
  console.log("1. Opção A");
  console.log("2. Opção B");
  console.log("3. Opção C");
  console.log("0. Sair");

  opcao = 1; // Em um programa real, leria a entrada do usuário

  switch (opcao) {
    case 1:
      console.log("Você escolheu A");
      break;
    case 2:
      console.log("Você escolheu B");
      break;
    case 3:
      console.log("Você escolheu C");
      break;
    case 0:
      console.log("Saindo...");
      break;
    default:
      console.log("Opção inválida!");
  }
} while (opcao !== 0);
```

### 3.4 Fibonacci

```typescript
const quantidade: number = 15;
let anterior: number = 0;
let atual: number = 1;

console.log("Sequência de Fibonacci:");
console.log(anterior);
console.log(atual);

for (let i: number = 2; i < quantidade; i++) {
  const proximo: number = anterior + atual;
  console.log(proximo);
  anterior = atual;
  atual = proximo;
}
```

---

## 4. Laços Aninhados

```typescript
// Tabela de multiplicação 1 a 5
for (let i: number = 1; i <= 5; i++) {
  let linha: string = "";
  for (let j: number = 1; j <= 5; j++) {
    linha += `${(i * j).toString().padStart(4)}`;
  }
  console.log(linha);
}
```

Saída:
```
   1   2   3   4   5
   2   4   6   8  10
   3   6   9  12  15
   4   8  12  16  20
   5  10  15  20  25
```

---

## 5. Git - Commitando por etapas

Ao resolver exercícios, faça commits por etapa:

```bash
git add src/tabuada.ts
git commit -m "Implementa tabuada com for loop"

git add src/primos.ts
git commit -m "Adiciona verificação de números primos"

git add src/fibonacci.ts
git commit -m "Implementa sequência de Fibonacci"
```

**Dica:** Um commit por exercício cria um histórico que mostra sua evolução.

---

## Exercícios Práticos

### Exercício 1 - Classificador de triângulos
Crie `src/triangulo.ts` que:
1. Recebe três lados de um triângulo (constantes)
2. Verifica se formam um triângulo válido (cada lado < soma dos outros dois)
3. Classifica como: equilátero, isósceles ou escaleno

### Exercício 2 - FizzBuzz
Crie `src/fizzbuzz.ts` que:
1. Imprime números de 1 a 100
2. Para múltiplos de 3, imprime "Fizz" em vez do número
3. Para múltiplos de 5, imprime "Buzz"
4. Para múltiplos de ambos, imprime "FizzBuzz"

### Exercício 3 - Jogo de adivinhação (simplificado)
Crie `src/adivinhacao.ts` que:
1. Define um número secreto (constante)
2. Simula tentativas em um array: `[50, 25, 37, 42]`
3. Para cada tentativa, diz se é "maior", "menor" ou "acertou!"
4. Ao acertar, exibe quantas tentativas foram necessárias

### Exercício 4 - Padrão visual
Crie `src/padrao.ts` que imprime:
```
*
**
***
****
*****
****
***
**
*
```
Use laços aninhados. Dica: são dois laços for, um crescente e um decrescente.

### Exercício 5 - Calculadora com menu
Crie `src/calculadora-menu.ts` que:
1. Exibe um menu com operações (soma, sub, mult, div)
2. Usa switch para executar a operação
3. Trata divisão por zero
4. (Desafio) Use do...while para permitir várias operações

---

## Dica: Usando IA para Debugar Laços

Laços são uma fonte comum de bugs. O **GitHub Copilot Chat** pode ajudar:

1. Selecione um trecho de código com bug
2. Abra o Copilot Chat e pergunte: *"Por que esse laço roda infinitamente?"*
3. Ou peça: *"Trace a execução desse for mostrando o valor de i a cada passo"*

> **Desafio com IA:** Implemente o Exercício 4 sozinho. Se travar, peça ao Copilot: *"Me dê uma dica para imprimir um padrão de losango com asteriscos, sem me dar a resposta completa."* Isso treina o raciocínio lógico com apoio da IA.

---

## Leitura Complementar

- [MDN - if...else](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/if...else)
- [MDN - Loops and iteration](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide/Loops_and_iteration)
