# Aula 04 - Comandos de Desvio e Controle de Malhas

## Objetivos da Aula

- Utilizar estruturas condicionais: if/else, else if, switch
- Implementar lacos de repeticao: for, while, do-while
- Combinar condicionais e lacos para resolver problemas
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

> **Comparando com C:** A sintaxe e identica. A unica diferenca e que em TypeScript a variavel tem tipo anotado.

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

### 1.3 Operador ternario (revisao)

Para condicoes simples, o ternario e mais conciso:

```typescript
const idade: number = 17;
const categoria: string = idade >= 18 ? "adulto" : "menor";
```

### 1.4 switch

Ideal quando ha muitas comparacoes com um mesmo valor:

```typescript
const diaSemana: number = 3;

switch (diaSemana) {
  case 1:
    console.log("Segunda-feira");
    break;
  case 2:
    console.log("Terca-feira");
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
    console.log("Dia invalido");
}
```

> **Atencao ao `break`!** Sem ele, a execucao "cai" para o proximo case (fall-through), exatamente como em C.

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
    console.log("Operacao desconhecida");
}
```

---

## 2. Estruturas de Repeticao (Lacos/Malhas)

### 2.1 for

```typescript
// Estrutura: for (inicio; condicao; incremento)
for (let i: number = 0; i < 5; i++) {
  console.log(`Iteracao ${i}`);
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

Executa enquanto a condicao for verdadeira. Testa **antes** de executar:

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

// continue - pula para a proxima iteracao
for (let i: number = 0; i < 10; i++) {
  if (i % 2 !== 0) continue;  // pula impares
  console.log(i);
}
// Saida: 0, 2, 4, 6, 8
```

### 2.5 for...of (exclusivo do JavaScript/TypeScript)

Itera sobre os **valores** de um iteravel (arrays, strings):

```typescript
const frutas: string[] = ["maca", "banana", "laranja"];

for (const fruta of frutas) {
  console.log(fruta);
}
// maca, banana, laranja

// Tambem funciona com strings
const palavra: string = "FATEC";
for (const letra of palavra) {
  console.log(letra);
}
// F, A, T, E, C
```

> Isso nao existe em C. E muito mais pratico que iterar com indice quando voce so precisa dos valores.

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

### 3.2 Numeros primos

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
  console.log("1. Opcao A");
  console.log("2. Opcao B");
  console.log("3. Opcao C");
  console.log("0. Sair");

  opcao = 1; // Em um programa real, leria a entrada do usuario

  switch (opcao) {
    case 1:
      console.log("Voce escolheu A");
      break;
    case 2:
      console.log("Voce escolheu B");
      break;
    case 3:
      console.log("Voce escolheu C");
      break;
    case 0:
      console.log("Saindo...");
      break;
    default:
      console.log("Opcao invalida!");
  }
} while (opcao !== 0);
```

### 3.4 Fibonacci

```typescript
const quantidade: number = 15;
let anterior: number = 0;
let atual: number = 1;

console.log("Sequencia de Fibonacci:");
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

## 4. Lacos Aninhados

```typescript
// Tabela de multiplicacao 1 a 5
for (let i: number = 1; i <= 5; i++) {
  let linha: string = "";
  for (let j: number = 1; j <= 5; j++) {
    linha += `${(i * j).toString().padStart(4)}`;
  }
  console.log(linha);
}
```

Saida:
```
   1   2   3   4   5
   2   4   6   8  10
   3   6   9  12  15
   4   8  12  16  20
   5  10  15  20  25
```

---

## 5. Git - Commitando por etapas

Ao resolver exercicios, faca commits por etapa:

```bash
git add src/tabuada.ts
git commit -m "Implementa tabuada com for loop"

git add src/primos.ts
git commit -m "Adiciona verificacao de numeros primos"

git add src/fibonacci.ts
git commit -m "Implementa sequencia de Fibonacci"
```

**Dica:** Um commit por exercicio cria um historico que mostra sua evolucao.

---

## Exercicios Praticos

### Exercicio 1 - Classificador de triangulos
Crie `src/triangulo.ts` que:
1. Recebe tres lados de um triangulo (constantes)
2. Verifica se formam um triangulo valido (cada lado < soma dos outros dois)
3. Classifica como: equilatero, isosceles ou escaleno

### Exercicio 2 - FizzBuzz
Crie `src/fizzbuzz.ts` que:
1. Imprime numeros de 1 a 100
2. Para multiplos de 3, imprime "Fizz" em vez do numero
3. Para multiplos de 5, imprime "Buzz"
4. Para multiplos de ambos, imprime "FizzBuzz"

### Exercicio 3 - Jogo de adivinhacao (simplificado)
Crie `src/adivinhacao.ts` que:
1. Define um numero secreto (constante)
2. Simula tentativas em um array: `[50, 25, 37, 42]`
3. Para cada tentativa, diz se e "maior", "menor" ou "acertou!"
4. Ao acertar, exibe quantas tentativas foram necessarias

### Exercicio 4 - Padrao visual
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
Use lacos aninhados. Dica: sao dois lacos for, um crescente e um decrescente.

### Exercicio 5 - Calculadora com menu
Crie `src/calculadora-menu.ts` que:
1. Exibe um menu com operacoes (soma, sub, mult, div)
2. Usa switch para executar a operacao
3. Trata divisao por zero
4. (Desafio) Use do...while para permitir varias operacoes

---

## Dica: Usando IA para Debugar Lacos

Lacos sao uma fonte comum de bugs. O **GitHub Copilot Chat** pode ajudar:

1. Selecione um trecho de codigo com bug
2. Abra o Copilot Chat e pergunte: *"Por que esse laco roda infinitamente?"*
3. Ou peca: *"Trace a execucao desse for mostrando o valor de i a cada passo"*

> **Desafio com IA:** Implemente o Exercicio 4 sozinho. Se travar, peca ao Copilot: *"Me de uma dica para imprimir um padrao de losango com asteriscos, sem me dar a resposta completa."* Isso treina o raciocinio logico com apoio da IA.

---

## Leitura Complementar

- [MDN - if...else](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/if...else)
- [MDN - Loops and iteration](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide/Loops_and_iteration)
