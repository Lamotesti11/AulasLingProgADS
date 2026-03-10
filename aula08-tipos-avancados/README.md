# Aula 08 - Union Types, Generics e Tipos Avancados

## Objetivos da Aula

- Utilizar Union Types para valores que podem ter mais de um tipo
- Aplicar Type Guards para verificação segura de tipos
- Entender Literal Types e Enums
- Introduzir Generics para funções e tipos reutilizáveis
- Usar Utility Types do TypeScript

---

## 1. Union Types

Um valor que pode ser de **mais de um tipo**:

```typescript
// A variavel pode ser string OU number
let id: string | number;

id = "ABC123";  // ok
id = 42;        // ok
// id = true;   // ERRO! boolean não está na união
```

> **Comparando com C:** Em C, `union` permite que uma variavel armazene diferentes tipos no mesmo espaco de memoria. Em TypeScript, Union Types sao verificados em tempo de compilacao - mais seguros.

### 1.1 Union em funções

```typescript
function exibirId(id: string | number): void {
  console.log(`ID: ${id}`);
}

exibirId("ABC");  // ok
exibirId(123);    // ok
```

### 1.2 Union com arrays

```typescript
// Array que aceita strings E numbers
const misturado: (string | number)[] = ["Ana", 21, "Bruno", 22];
```

---

## 2. Type Guards (Estreitamento de Tipo)

Quando você tem um Union Type, precisa **verificar** qual tipo é antes de usar métodos específicos:

### 2.1 typeof

```typescript
function processar(valor: string | number): string {
  if (typeof valor === "string") {
    // Aqui o TS sabe que 'valor' é string
    return valor.toUpperCase();
  }
  // Aqui o TS sabe que 'valor' é number
  return valor.toFixed(2);
}

console.log(processar("ola"));   // "OLA"
console.log(processar(3.14159)); // "3.14"
```

### 2.2 Verificacao com propriedades (in)

```typescript
interface Carro {
  marca: string;
  portas: number;
}

interface Moto {
  marca: string;
  cilindradas: number;
}

function exibirVeiculo(veiculo: Carro | Moto): void {
  console.log(`Marca: ${veiculo.marca}`);

  if ("portas" in veiculo) {
    console.log(`Portas: ${veiculo.portas}`);
  } else {
    console.log(`Cilindradas: ${veiculo.cilindradas}`);
  }
}
```

---

## 3. Literal Types

Tipos que aceitam apenas **valores especificos**:

```typescript
// Literal type com strings
type Direcao = "norte" | "sul" | "leste" | "oeste";

let rumo: Direcao = "norte";  // ok
// rumo = "nordeste";          // ERRO! Não está nas opções

// Literal type com numeros
type NotaConceito = 0 | 1 | 2 | 3 | 4 | 5;

let avaliacao: NotaConceito = 4;  // ok
// avaliacao = 6;                  // ERRO!
```

### Uso prático - Status

```typescript
type StatusPedido = "pendente" | "processando" | "enviado" | "entregue" | "cancelado";

interface Pedido {
  id: number;
  produto: string;
  status: StatusPedido;
}

function avancarStatus(pedido: Pedido): StatusPedido {
  switch (pedido.status) {
    case "pendente":
      return "processando";
    case "processando":
      return "enviado";
    case "enviado":
      return "entregue";
    default:
      return pedido.status;
  }
}
```

---

## 4. Enums

Enums definem um conjunto de constantes nomeadas:

```typescript
enum DiaSemana {
  Segunda,    // 0
  Terca,      // 1
  Quarta,     // 2
  Quinta,     // 3
  Sexta,      // 4
  Sabado,     // 5
  Domingo,    // 6
}

const hoje: DiaSemana = DiaSemana.Quarta;
console.log(hoje);  // 2

// Enum com valores customizados
enum HttpStatus {
  OK = 200,
  Created = 201,
  BadRequest = 400,
  NotFound = 404,
  InternalError = 500,
}

function tratarResposta(status: HttpStatus): string {
  switch (status) {
    case HttpStatus.OK:
      return "Sucesso!";
    case HttpStatus.NotFound:
      return "Não encontrado";
    case HttpStatus.InternalError:
      return "Erro do servidor";
    default:
      return "Status desconhecido";
  }
}
```

> **Enum vs Literal Type:** Para a maioria dos casos, **Literal Types sao preferidos** por serem mais simples. Use Enums quando precisar de valores numericos associados ou quando o valor precisa existir em runtime.

---

## 5. Generics

Generics permitem criar funções e tipos que funcionam com **qualquer tipo**, mantendo a segurança:

### 5.1 Funcao generica

```typescript
// Sem generics - perde a informacao do tipo
function primeiroElemento(arr: any[]): any {
  return arr[0];
}

// Com generics - preserva o tipo
function primeiroElemento<T>(arr: T[]): T | undefined {
  return arr[0];
}

const num = primeiroElemento([1, 2, 3]);       // tipo: number
const str = primeiroElemento(["a", "b", "c"]); // tipo: string
```

O `<T>` é um **parâmetro de tipo** - funciona como uma variável para tipos.

### 5.2 Mais exemplos

```typescript
// Funcao que retorna o ultimo elemento
function ultimo<T>(arr: T[]): T | undefined {
  return arr[arr.length - 1];
}

// Funcao que filtra por tipo
function filtrar<T>(arr: T[], condição: (item: T) => boolean): T[] {
  return arr.filter(condição);
}

const numeros: number[] = [1, 2, 3, 4, 5, 6];
const pares = filtrar(numeros, (n) => n % 2 === 0);  // number[]
```

### 5.3 Interface generica

```typescript
interface Resposta<T> {
  sucesso: boolean;
  dados: T;
  mensagem: string;
}

// Resposta com array de strings
const resp1: Resposta<string[]> = {
  sucesso: true,
  dados: ["Ana", "Bruno"],
  mensagem: "Usuários encontrados",
};

// Resposta com number
const resp2: Resposta<number> = {
  sucesso: true,
  dados: 42,
  mensagem: "Total calculado",
};
```

> Esse padrao de `Resposta<T>` e muito usado em APIs - veremos isso nas aulas 11-14.

---

## 6. Utility Types

TypeScript vem com tipos utilitários prontos:

### 6.1 Partial - torna todas as propriedades opcionais

```typescript
interface Usuario {
  nome: string;
  email: string;
  idade: number;
}

// Útil para atualizações parciais
function atualizar(id: number, dados: Partial<Usuario>): void {
  console.log(`Atualizando ${id}:`, dados);
}

atualizar(1, { nome: "Novo Nome" });          // ok
atualizar(2, { email: "novo@email.com" });    // ok
atualizar(3, { nome: "Ana", idade: 22 });     // ok
```

### 6.2 Pick - seleciona propriedades especificas

```typescript
type UsuarioResumido = Pick<Usuario, "nome" | "email">;
// Equivale a: { nome: string; email: string }
```

### 6.3 Omit - remove propriedades

```typescript
type UsuarioSemEmail = Omit<Usuario, "email">;
// Equivale a: { nome: string; idade: number }
```

### 6.4 Record - mapa de chave-valor tipado

```typescript
type NotasPorAluno = Record<string, number>;

const notas: NotasPorAluno = {
  "Ana": 8.5,
  "Bruno": 7.0,
  "Carlos": 9.2,
};
```

---

## 7. Type Assertions

Quando você sabe mais que o TypeScript sobre o tipo:

```typescript
// Assertion com 'as'
const valor: unknown = "Ola Mundo";
const tamanho: number = (valor as string).length;

// Útil ao trabalhar com DOM ou dados externos
```

> **Cuidado:** Type assertions podem esconder bugs. Prefira Type Guards sempre que possivel.

---

## 8. Git - Stash para guardar trabalho temporário

Quando você precisa trocar de branch mas tem alterações não commitadas:

```bash
# Guardar alteracoes temporariamente
git stash

# Trocar de branch, fazer outra coisa...
git checkout outra-branch

# Voltar e recuperar as alteracoes
git checkout main
git stash pop
```

---

## Exercícios Práticos

### Exercício 1 - Sistema de formas geométricas
Crie `src/formas.ts`:
1. Defina interfaces: `Circulo` (raio), `Retangulo` (largura, altura), `Triangulo` (base, altura)
2. Crie um Union Type: `Forma = Circulo | Retangulo | Triangulo`
3. Use uma propriedade `tipo` (literal type) para diferenciar
4. Funcao `calcularArea(forma: Forma): number` com type guards

### Exercício 2 - Enums na prática
Crie `src/pedidos.ts`:
1. Enum `StatusPedido` e `FormaPagamento`
2. Interface `Pedido` usando os enums
3. Array de pedidos com diferentes status
4. Funções para: filtrar por status, calcular total por forma de pagamento

### Exercício 3 - Função genérica de busca
Crie `src/busca.ts`:
1. Função genérica `buscarPorCampo<T>(arr: T[], campo: keyof T, valor: unknown): T[]`
2. Teste com arrays de diferentes interfaces (Aluno, Produto, etc.)
3. Função genérica `ordenarPor<T>(arr: T[], campo: keyof T): T[]`

### Exercício 4 - API Response tipada
Crie `src/api-response.ts`:
1. Interface genérica `ApiResponse<T>` com: dados, sucesso, erro?, timestamp
2. Função `criarSucesso<T>(dados: T): ApiResponse<T>`
3. Função `criarErro<T>(mensagem: string): ApiResponse<T>`
4. Teste com diferentes tipos de dados

---

## Dica: IA e Tipos Complexos

Tipos avançados podem ser difíceis de escrever. O Copilot é muito bom nisso:

1. **Gerar tipos a partir de dados:**
   Cole um objeto e peca: *"Gere um tipo TypeScript para essa estrutura, usando union types onde fizer sentido"*

2. **Explicar Utility Types:**
   Peca: *"Me explique o que `Partial<Pick<Usuario, 'nome' | 'email'>>` significa"*

3. **Desafio:** Tente resolver o Exercício 3 sem ajuda. Depois peça ao Copilot: *"Tem como simplificar esse tipo?"* - você pode aprender truques novos.

---

## Leitura Complementar

- [TypeScript - Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [TypeScript - Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [TypeScript - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
