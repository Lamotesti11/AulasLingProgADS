# Aula 05 - Funcoes e Escopo

## Objetivos da Aula

- Declarar e invocar funcoes com tipos em TypeScript
- Entender parametros, retorno e assinatura de funcoes
- Diferenciar function declaration, function expression e arrow function
- Compreender escopo de variaveis (global, local, bloco)
- Utilizar parametros opcionais e valores padrao

---

## 1. O que e uma Funcao?

Uma funcao e um bloco de codigo reutilizavel que executa uma tarefa especifica.

```typescript
// Declaracao da funcao
function saudacao(nome: string): string {
  return `Ola, ${nome}!`;
}

// Chamada (invocacao) da funcao
const mensagem: string = saudacao("Maria");
console.log(mensagem);  // "Ola, Maria!"
```

> **Comparando com C:**
> ```c
> // C
> char* saudacao(char* nome) { ... }
>
> // TypeScript
> function saudacao(nome: string): string { ... }
> ```
> A estrutura e semelhante: nome, parametros tipados e tipo de retorno.

---

## 2. Anatomia de uma Funcao TypeScript

```typescript
function calcularMedia(nota1: number, nota2: number, nota3: number): number {
//       ^nome         ^parametros tipados                          ^tipo retorno
  const soma: number = nota1 + nota2 + nota3;
  return soma / 3;
//^retorno
}
```

### 2.1 Funcao sem retorno (void)

```typescript
function exibirNota(aluno: string, nota: number): void {
  console.log(`${aluno}: ${nota.toFixed(1)}`);
  // Nao tem return (ou return sem valor)
}
```

### 2.2 Funcao com retorno antecipado

```typescript
function dividir(a: number, b: number): number | null {
  if (b === 0) {
    console.log("Erro: divisao por zero!");
    return null;  // Retorno antecipado
  }
  return a / b;
}
```

---

## 3. Formas de Declarar Funcoes

### 3.1 Function Declaration

```typescript
function somar(a: number, b: number): number {
  return a + b;
}
```

### 3.2 Function Expression

```typescript
const somar = function (a: number, b: number): number {
  return a + b;
};
```

### 3.3 Arrow Function (funcao seta)

Sintaxe mais curta, muito usada no ecossistema JS/TS:

```typescript
// Forma completa
const somar = (a: number, b: number): number => {
  return a + b;
};

// Forma resumida (retorno implicito - sem chaves)
const somar = (a: number, b: number): number => a + b;

// Com um unico parametro (parenteses opcionais)
const dobro = (n: number): number => n * 2;
```

> **Quando usar cada forma?**
> - `function declaration`: funcoes principais, mais visiveis
> - `arrow function`: callbacks, funcoes curtas, funcoes passadas como argumento

---

## 4. Parametros Avancados

### 4.1 Parametros opcionais

Use `?` para tornar um parametro opcional:

```typescript
function saudacao(nome: string, titulo?: string): string {
  if (titulo) {
    return `Ola, ${titulo} ${nome}!`;
  }
  return `Ola, ${nome}!`;
}

console.log(saudacao("Ana"));            // "Ola, Ana!"
console.log(saudacao("Ana", "Profa."));  // "Ola, Profa. Ana!"
```

> Parametros opcionais devem vir **depois** dos obrigatorios.

### 4.2 Valores padrao (default)

```typescript
function calcularDesconto(valor: number, percentual: number = 10): number {
  return valor - (valor * percentual / 100);
}

console.log(calcularDesconto(100));      // 90 (10% padrao)
console.log(calcularDesconto(100, 25));  // 75 (25% informado)
```

### 4.3 Rest parameters (numero variavel de argumentos)

```typescript
function somarTodos(...numeros: number[]): number {
  let total: number = 0;
  for (const num of numeros) {
    total += num;
  }
  return total;
}

console.log(somarTodos(1, 2, 3));        // 6
console.log(somarTodos(10, 20, 30, 40)); // 100
```

> **Comparando com C:** Em C voce usaria `va_list` com `<stdarg.h>`, que e muito mais verboso. O rest parameter do TypeScript e bem mais simples.

---

## 5. Escopo de Variaveis

### 5.1 Escopo global

```typescript
const mensagem: string = "Global";  // Acessivel em todo o arquivo

function teste(): void {
  console.log(mensagem);  // "Global" - acessa a variavel global
}
```

### 5.2 Escopo local (funcao)

```typescript
function calcular(): void {
  const resultado: number = 42;  // Existe APENAS dentro desta funcao
  console.log(resultado);
}

calcular();
// console.log(resultado);  // ERRO! resultado nao existe aqui fora
```

### 5.3 Escopo de bloco

`let` e `const` respeitam o escopo de bloco (chaves `{}`):

```typescript
if (true) {
  const dentroDoIf: string = "visivel aqui";
  let tambemAqui: number = 10;
  console.log(dentroDoIf);  // ok
}

// console.log(dentroDoIf);  // ERRO! Nao existe fora do bloco
```

```typescript
for (let i = 0; i < 5; i++) {
  // 'i' so existe dentro deste for
}
// console.log(i);  // ERRO! 'i' nao existe aqui
```

> **Diferenca do C:** Em C, variaveis declaradas dentro de um `for` tambem tem escopo de bloco (C99+). O comportamento e o mesmo com `let`/`const`.

### 5.4 Shadowing (sombreamento)

```typescript
const valor: number = 100;

function teste(): void {
  const valor: number = 50;  // "sombra" a variavel externa
  console.log(valor);  // 50
}

teste();
console.log(valor);  // 100 (a externa nao foi alterada)
```

---

## 6. Funcoes como Valores

Em TypeScript (e JavaScript), funcoes sao **valores**. Podem ser atribuidas a variaveis, passadas como argumentos e retornadas por outras funcoes:

```typescript
// Funcao como argumento
function executarOperacao(a: number, b: number, operacao: (x: number, y: number) => number): number {
  return operacao(a, b);
}

const soma = (x: number, y: number): number => x + y;
const mult = (x: number, y: number): number => x * y;

console.log(executarOperacao(5, 3, soma));  // 8
console.log(executarOperacao(5, 3, mult));  // 15
```

> Esse conceito e fundamental. Voltaremos a ele com mais profundidade na proxima aula.

---

## 7. Tipo de Funcao

Em TypeScript, voce pode tipar uma variavel que armazena uma funcao:

```typescript
// Tipo de funcao como type alias
type Operacao = (a: number, b: number) => number;

const somar: Operacao = (a, b) => a + b;
const subtrair: Operacao = (a, b) => a - b;
```

---

## 8. Git - Branches para Exercicios

Nesta aula, vamos usar **branches** do Git para organizar exercicios:

```bash
# Crie uma branch para o exercicio
git checkout -b exercicio/calculadora

# ... resolva o exercicio, faca commits ...
git add src/calculadora.ts
git commit -m "Implementa calculadora com funcoes"

# Volte para a branch principal
git checkout main

# Mescle o exercicio na main
git merge exercicio/calculadora
```

> **Por que branches?** Permitem trabalhar em funcionalidades isoladas sem afetar o codigo principal. E uma pratica essencial em equipes.

---

## Exercicios Praticos

### Exercicio 1 - Funcoes matematicas
Crie `src/matematica.ts` com funcoes para:
1. `calcularFatorial(n: number): number` - fatorial de n
2. `ehPar(n: number): boolean` - verifica se e par
3. `calcularPotencia(base: number, expoente: number): number` - sem usar `**`
4. Teste todas as funcoes com diferentes valores

### Exercicio 2 - Calculadora com funcoes
Refatore a calculadora da Aula 04:
1. Crie funcoes separadas: `somar`, `subtrair`, `multiplicar`, `dividir`
2. Crie uma funcao `calcular(a, b, operacao)` que recebe a operacao como parametro
3. Use arrow functions

### Exercicio 3 - Validador de dados
Crie `src/validador.ts` com funcoes:
1. `validarIdade(idade: number): boolean` - entre 0 e 150
2. `validarNome(nome: string): boolean` - nao vazio e minimo 2 caracteres
3. `validarEmail(email: string): boolean` - contem `@` e `.`
4. Crie uma funcao `validarCadastro` que use todas as anteriores

### Exercicio 4 - Funcoes com rest parameters
Crie `src/estatistica.ts` com:
1. `media(...notas: number[]): number`
2. `maior(...numeros: number[]): number`
3. `menor(...numeros: number[]): number`
4. Teste com diferentes quantidades de argumentos

---

## Dica: GitHub Copilot e Funcoes

O Copilot brilha ao sugerir funcoes. Experimente:

1. **Nomeie bem a funcao** e o Copilot deduz a implementacao:
   ```typescript
   function calcularIMC(peso: number, altura: number): number {
     // Copilot sugere: return peso / (altura * altura);
   }
   ```

2. **Escreva os testes primeiro** e peca ao Copilot a implementacao:
   ```typescript
   // calcularFatorial(5) deve retornar 120
   // calcularFatorial(0) deve retornar 1
   // calcularFatorial(1) deve retornar 1
   function calcularFatorial(n: number): number {
     // Copilot implementa baseado nos exemplos acima
   }
   ```

> **Dica de aprendizado:** Tente implementar sozinho primeiro. Depois compare com a sugestao do Copilot. Se a sugestao for diferente, entenda **por que** - ambas podem estar corretas, ou a do Copilot pode ensinar um padrao novo.

---

## Leitura Complementar

- [TypeScript - Functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
- [MDN - Functions](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide/Functions)
