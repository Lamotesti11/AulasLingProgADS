# Aula 07 - Objetos, Interfaces e Tipos Personalizados

## Objetivos da Aula

- Criar e manipular objetos em TypeScript
- Definir interfaces para tipar estruturas de dados
- Usar type aliases para criar tipos personalizados
- Entender a diferenca entre interface e type
- Trabalhar com arrays de objetos

---

## 1. Objetos em TypeScript

### 1.1 Criando objetos

```typescript
// Objeto literal com tipo inferido
const aluno = {
  nome: "Ana",
  idade: 21,
  curso: "ADS",
};

console.log(aluno.nome);     // "Ana"
console.log(aluno["idade"]); // 21
```

> **Comparando com C:** Objetos em TypeScript sao semelhantes a `struct` em C:
> ```c
> // C
> struct Aluno {
>   char nome[50];
>   int idade;
>   char curso[10];
> };
> struct Aluno aluno = {"Ana", 21, "ADS"};
>
> // TypeScript
> const aluno = { nome: "Ana", idade: 21, curso: "ADS" };
> ```
> A grande diferenca: em TS nao precisa declarar a struct antes, e os campos sao dinamicos.

### 1.2 Modificando objetos

```typescript
const produto = {
  nome: "Notebook",
  preco: 3500,
};

// Alterar valor de uma propriedade
produto.preco = 3200;

// Adicionar nova propriedade (nao recomendado sem tipagem)
// produto.desconto = 10;  // TS pode reclamar dependendo da tipagem
```

---

## 2. Interfaces

Interfaces definem a **forma** (shape) de um objeto - quais propriedades ele deve ter e de que tipo:

### 2.1 Definindo uma interface

```typescript
interface Aluno {
  nome: string;
  idade: number;
  curso: string;
  matricula: string;
}

// Agora o TypeScript GARANTE que o objeto segue a interface
const aluno1: Aluno = {
  nome: "Bruno",
  idade: 22,
  curso: "ADS",
  matricula: "2024001",
};

// ERRO! Faltou 'matricula'
// const aluno2: Aluno = { nome: "Ana", idade: 21, curso: "ADS" };
```

### 2.2 Propriedades opcionais

```typescript
interface Produto {
  nome: string;
  preco: number;
  descricao?: string;  // opcional
  estoque?: number;    // opcional
}

// Ok - descricao e estoque sao opcionais
const produto: Produto = {
  nome: "Mouse",
  preco: 89.90,
};
```

### 2.3 Propriedades readonly

```typescript
interface Configuracao {
  readonly versao: string;
  readonly criadoEm: string;
  nome: string;
}

const config: Configuracao = {
  versao: "1.0.0",
  criadoEm: "2026-03-10",
  nome: "Meu App",
};

config.nome = "Novo Nome";  // ok
// config.versao = "2.0.0"; // ERRO! readonly
```

---

## 3. Type Aliases

`type` cria um **alias** (apelido) para qualquer tipo:

### 3.1 Tipos basicos

```typescript
type ID = string;
type Nota = number;
type Ativo = boolean;

const matricula: ID = "2024001";
const media: Nota = 8.5;
```

### 3.2 Tipos de objetos

```typescript
type Coordenada = {
  x: number;
  y: number;
};

const ponto: Coordenada = { x: 10, y: 20 };
```

### 3.3 Interface vs Type - Quando usar?

| Caracteristica | interface | type |
|----------------|-----------|------|
| Objetos | Sim | Sim |
| Union types | Nao | Sim |
| Extends/heranca | Sim (extends) | Sim (intersecao &) |
| Reabrir/estender depois | Sim | Nao |
| Tipos primitivos | Nao | Sim |

**Regra pratica:** Use `interface` para objetos e `type` para todo o resto.

---

## 4. Arrays de Objetos

Onde o poder real aparece - combinando arrays com objetos tipados:

```typescript
interface Aluno {
  nome: string;
  idade: number;
  nota: number;
}

const turma: Aluno[] = [
  { nome: "Ana", idade: 21, nota: 8.5 },
  { nome: "Bruno", idade: 22, nota: 6.0 },
  { nome: "Carlos", idade: 20, nota: 9.2 },
  { nome: "Diana", idade: 23, nota: 4.5 },
  { nome: "Eduardo", idade: 21, nota: 7.0 },
];

// Filtrar aprovados
const aprovados: Aluno[] = turma.filter((a) => a.nota >= 6);

// Nomes dos aprovados
const nomesAprovados: string[] = aprovados.map((a) => a.nome);
console.log(nomesAprovados);  // ["Ana", "Bruno", "Carlos", "Eduardo"]

// Media da turma
const media: number = turma.reduce((soma, a) => soma + a.nota, 0) / turma.length;
console.log(`Media: ${media.toFixed(1)}`);  // "Media: 7.0"

// Encontrar aluno por nome
const buscado: Aluno | undefined = turma.find((a) => a.nome === "Carlos");
console.log(buscado);  // { nome: "Carlos", idade: 20, nota: 9.2 }

// Ordenar por nota (decrescente)
const ranking: Aluno[] = [...turma].sort((a, b) => b.nota - a.nota);
```

---

## 5. Objetos Aninhados

```typescript
interface Endereco {
  rua: string;
  numero: number;
  cidade: string;
  estado: string;
}

interface Funcionario {
  nome: string;
  cargo: string;
  salario: number;
  endereco: Endereco;
}

const func: Funcionario = {
  nome: "Maria",
  cargo: "Desenvolvedora",
  salario: 5000,
  endereco: {
    rua: "Av. Brasil",
    numero: 1000,
    cidade: "Sao Paulo",
    estado: "SP",
  },
};

console.log(func.endereco.cidade);  // "Sao Paulo"
```

---

## 6. Funcoes com Objetos

```typescript
interface Retangulo {
  largura: number;
  altura: number;
}

function calcularArea(ret: Retangulo): number {
  return ret.largura * ret.altura;
}

function calcularPerimetro(ret: Retangulo): number {
  return 2 * (ret.largura + ret.altura);
}

// Funcao que retorna um objeto
function criarRetangulo(largura: number, altura: number): Retangulo {
  return { largura, altura };  // shorthand: { largura: largura, altura: altura }
}

const r = criarRetangulo(10, 5);
console.log(`Area: ${calcularArea(r)}`);          // 50
console.log(`Perimetro: ${calcularPerimetro(r)}`); // 30
```

---

## 7. Destructuring de Objetos

```typescript
const aluno = {
  nome: "Ana",
  idade: 21,
  curso: "ADS",
  nota: 8.5,
};

// Extrair propriedades em variaveis
const { nome, nota } = aluno;
console.log(nome);  // "Ana"
console.log(nota);  // 8.5

// Renomear durante destructuring
const { nome: nomeAluno, curso: cursoAluno } = aluno;
console.log(nomeAluno);  // "Ana"

// Em parametros de funcao
function exibirAluno({ nome, nota }: Aluno): void {
  console.log(`${nome}: ${nota}`);
}
```

---

## 8. Spread com Objetos

```typescript
const base = { nome: "Produto", preco: 100 };

// Copiar e adicionar/sobrescrever propriedades
const comDesconto = { ...base, desconto: 10, preco: 90 };
// { nome: "Produto", preco: 90, desconto: 10 }

// Merge de objetos
const dadosPessoais = { nome: "Ana", idade: 21 };
const dadosAcademicos = { curso: "ADS", semestre: 3 };
const aluno = { ...dadosPessoais, ...dadosAcademicos };
// { nome: "Ana", idade: 21, curso: "ADS", semestre: 3 }
```

---

## 9. Git - Tags para marcos

Use tags do Git para marcar versoes ou marcos importantes:

```bash
# Criar uma tag
git tag -a v1.0 -m "Aula 07 completa - Objetos e Interfaces"

# Listar tags
git tag

# Enviar tags para o GitHub
git push --tags
```

---

## Exercicios Praticos

### Exercicio 1 - Cadastro de alunos
Crie `src/alunos.ts`:
1. Defina a interface `Aluno` com: nome, ra, notas (array), faltas
2. Crie um array com pelo menos 5 alunos
3. Implemente funcoes:
   - `calcularMedia(aluno: Aluno): number`
   - `verificarAprovacao(aluno: Aluno): string` (media >= 6 E faltas <= 10)
   - `gerarRelatorio(turma: Aluno[]): void` (exibe relatorio formatado)

### Exercicio 2 - Catalogo de produtos
Crie `src/catalogo.ts`:
1. Interface `Produto` com: id, nome, preco, categoria, estoque
2. Array com pelo menos 8 produtos de categorias diferentes
3. Funcoes:
   - Buscar por nome (parcial, case-insensitive)
   - Filtrar por categoria
   - Filtrar por faixa de preco (min e max)
   - Calcular valor total do estoque

### Exercicio 3 - Agenda de contatos
Crie `src/agenda.ts`:
1. Interfaces para `Telefone` (numero, tipo) e `Contato` (nome, email, telefones[])
2. Funcoes para: adicionar, remover, buscar por nome, listar todos
3. Use objetos aninhados e arrays de objetos

### Exercicio 4 - Sistema de notas
Crie `src/boletim.ts`:
1. Interfaces: `Disciplina` (nome, nota, carga_horaria), `Boletim` (aluno, semestre, disciplinas[])
2. Calcule: media ponderada (peso = carga horaria), coeficiente de rendimento
3. Liste disciplinas aprovadas e reprovadas
4. Exiba relatorio completo

---

## Dica: IA e Interfaces

O Copilot e excelente para gerar interfaces a partir de exemplos:

1. **Cole um JSON e peca a interface:**
   ```
   Copilot Chat: "Gere uma interface TypeScript para esse JSON: { nome: 'Ana', notas: [8, 7, 9] }"
   ```

2. **Gere dados de teste:**
   Apos definir a interface, escreva:
   ```typescript
   // Array com 10 alunos de teste
   ```
   O Copilot gerara os dados seguindo a interface.

> **Importante:** Sempre revise as interfaces geradas pela IA. Verifique se os tipos estao corretos e se propriedades que deveriam ser opcionais estao marcadas com `?`.

---

## Leitura Complementar

- [TypeScript - Object Types](https://www.typescriptlang.org/docs/handbook/2/objects.html)
- [TypeScript - Interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#interfaces)
