# Aula 11 - Introducao a APIs REST e HTTP

## Objetivos da Aula

- Entender o que e uma API e por que sao essenciais no desenvolvimento moderno
- Compreender o protocolo HTTP: metodos, status codes, headers
- Conhecer os principios da arquitetura REST
- Consumir APIs publicas usando fetch
- Entender JSON como formato de troca de dados

---

## 1. O que e uma API?

**API** (Application Programming Interface) e uma interface que permite que diferentes sistemas se comuniquem.

Analogia: um restaurante.
- Voce (cliente) nao vai ate a cozinha preparar a comida
- Voce faz um **pedido** ao garcom (API)
- O garcom leva o pedido a cozinha (servidor)
- A cozinha prepara e o garcom traz a **resposta**

### APIs no mundo real

- **Google Maps API**: apps usam para exibir mapas
- **API de pagamentos** (Stripe, PagSeguro): e-commerces processam pagamentos
- **API do GitHub**: ferramentas interagem com repositorios
- **APIs de IA** (OpenAI, Anthropic): apps integram inteligencia artificial

### Frontend <-> API <-> Banco de Dados

```
[App Mobile]  \
[Site Web]     --> [API (Servidor)] --> [Banco de Dados]
[Outro Sistema]/
```

A API e o **ponto central** que todos os clientes acessam.

---

## 2. Protocolo HTTP

Toda comunicacao na web usa o protocolo **HTTP** (HyperText Transfer Protocol).

### 2.1 Anatomia de uma requisicao HTTP

```
[METODO] [URL]
[Headers]

[Body (opcional)]
```

Exemplo:

```
POST /api/alunos HTTP/1.1
Host: minhaapi.com
Content-Type: application/json

{
  "nome": "Ana",
  "idade": 21
}
```

### 2.2 Metodos HTTP

| Metodo | Acao | Descricao | Exemplo |
|--------|------|-----------|---------|
| **GET** | Ler | Buscar dados | Listar alunos |
| **POST** | Criar | Enviar dados novos | Cadastrar aluno |
| **PUT** | Atualizar (completo) | Substituir recurso | Atualizar todos os dados do aluno |
| **PATCH** | Atualizar (parcial) | Modificar parte do recurso | Atualizar apenas o email |
| **DELETE** | Remover | Excluir recurso | Excluir aluno |

> **Analogia CRUD:**
> - **C**reate = POST
> - **R**ead = GET
> - **U**pdate = PUT/PATCH
> - **D**elete = DELETE

### 2.3 Status Codes (codigos de resposta)

| Faixa | Significado | Exemplos |
|-------|-------------|----------|
| **2xx** | Sucesso | 200 OK, 201 Created, 204 No Content |
| **3xx** | Redirecionamento | 301 Moved, 304 Not Modified |
| **4xx** | Erro do cliente | 400 Bad Request, 401 Unauthorized, 404 Not Found |
| **5xx** | Erro do servidor | 500 Internal Server Error, 503 Service Unavailable |

Os mais comuns no dia a dia:

```
200 - Tudo certo, aqui estao os dados
201 - Criado com sucesso
400 - Requisicao malformada (dados invalidos)
401 - Nao autenticado (precisa fazer login)
403 - Proibido (sem permissao)
404 - Nao encontrado
500 - Erro interno do servidor
```

### 2.4 Headers (cabecalhos)

Metadados da requisicao/resposta:

```
Content-Type: application/json    # Formato dos dados
Authorization: Bearer token123    # Autenticacao
Accept: application/json          # Formato aceito na resposta
```

---

## 3. O que e REST?

**REST** (Representational State Transfer) e um estilo de arquitetura para APIs. Principios:

### 3.1 Recursos e URLs

Cada "coisa" do sistema e um **recurso** com uma URL propria:

```
GET    /alunos          -> Lista todos os alunos
GET    /alunos/1        -> Busca aluno com id 1
POST   /alunos          -> Cria novo aluno
PUT    /alunos/1        -> Atualiza aluno 1
DELETE /alunos/1        -> Remove aluno 1
```

### 3.2 Convencoes REST

- URLs usam **substantivos** no plural: `/alunos`, `/produtos`, `/pedidos`
- NAO usam verbos: ~~`/criarAluno`~~, ~~`/deletarProduto`~~
- O metodo HTTP define a acao
- Respostas em JSON

### 3.3 Exemplo de fluxo completo

```
Cliente                          Servidor
  |                                 |
  |-- GET /alunos ----------------->|
  |<-- 200 [{...}, {...}] ---------|
  |                                 |
  |-- POST /alunos {nome: "Ana"} ->|
  |<-- 201 {id: 1, nome: "Ana"} --|
  |                                 |
  |-- GET /alunos/1 --------------->|
  |<-- 200 {id: 1, nome: "Ana"} --|
  |                                 |
  |-- DELETE /alunos/1 ------------>|
  |<-- 204 (sem conteudo) ---------|
```

---

## 4. Consumindo APIs com fetch

O `fetch` e a forma nativa de fazer requisicoes HTTP no Node.js (v18+):

### 4.1 GET - Buscando dados

```typescript
async function buscarUsuarios(): Promise<void> {
  const resposta = await fetch("https://jsonplaceholder.typicode.com/users");
  const usuarios = await resposta.json();

  console.log(`Total: ${usuarios.length} usuarios`);
  usuarios.forEach((u: any) => {
    console.log(`- ${u.name} (${u.email})`);
  });
}

buscarUsuarios();
```

### 4.2 GET com tipagem

```typescript
interface Usuario {
  id: number;
  name: string;
  email: string;
  phone: string;
}

async function buscarUsuario(id: number): Promise<Usuario | null> {
  const resposta = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`);

  if (!resposta.ok) {
    console.log(`Erro: ${resposta.status}`);
    return null;
  }

  const usuario: Usuario = await resposta.json();
  return usuario;
}

async function main(): Promise<void> {
  const user = await buscarUsuario(1);
  if (user) {
    console.log(`Nome: ${user.name}`);
    console.log(`Email: ${user.email}`);
  }
}

main();
```

### 4.3 POST - Enviando dados

```typescript
interface NovoPost {
  title: string;
  body: string;
  userId: number;
}

async function criarPost(post: NovoPost): Promise<void> {
  const resposta = await fetch("https://jsonplaceholder.typicode.com/posts", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(post),
  });

  const criado = await resposta.json();
  console.log(`Post criado com id: ${criado.id}`);
}

criarPost({
  title: "Meu primeiro post",
  body: "Aprendendo APIs REST com TypeScript!",
  userId: 1,
});
```

### 4.4 Tratando erros

```typescript
async function buscarComSeguranca(url: string): Promise<any> {
  try {
    const resposta = await fetch(url);

    if (!resposta.ok) {
      throw new Error(`HTTP ${resposta.status}: ${resposta.statusText}`);
    }

    return await resposta.json();
  } catch (erro) {
    if (erro instanceof Error) {
      console.log(`Erro na requisicao: ${erro.message}`);
    }
    return null;
  }
}
```

---

## 5. APIs Publicas para Praticar

Algumas APIs gratuitas para usar nos exercicios:

| API | URL | Descricao |
|-----|-----|-----------|
| JSONPlaceholder | jsonplaceholder.typicode.com | Posts, users, comments (fake) |
| PokeAPI | pokeapi.co/api/v2 | Dados de Pokemon |
| ViaCEP | viacep.com.br/ws/{cep}/json | Consulta de CEP |
| IBGE | servicodados.ibge.gov.br/api | Dados do Brasil |

Exemplo - Consultar CEP:

```typescript
interface Endereco {
  cep: string;
  logradouro: string;
  bairro: string;
  localidade: string;
  uf: string;
}

async function consultarCEP(cep: string): Promise<Endereco | null> {
  const resposta = await fetch(`https://viacep.com.br/ws/${cep}/json/`);

  if (!resposta.ok) return null;

  const endereco: Endereco = await resposta.json();
  return endereco;
}

async function main(): Promise<void> {
  const end = await consultarCEP("01001000");
  if (end) {
    console.log(`${end.logradouro}, ${end.bairro}`);
    console.log(`${end.localidade} - ${end.uf}`);
  }
}

main();
```

---

## 6. Async/Await - Revisao Rapida

```typescript
// Funcao assincrona - retorna uma Promise
async function buscarDados(): Promise<string> {
  // await "espera" a Promise resolver
  const resposta = await fetch("https://api.exemplo.com/dados");
  const dados = await resposta.json();
  return dados;
}

// Chamando funcao assincrona
async function main(): Promise<void> {
  const resultado = await buscarDados();
  console.log(resultado);
}

main();
```

> **Regra:** Sempre que usar `await`, a funcao precisa ser `async`. E o ponto de entrada (`main`) tambem precisa ser `async`.

---

## 7. Git - Branches para features

Ao comecar uma nova funcionalidade:

```bash
# Criar branch de feature
git checkout -b feature/consumir-api-cep

# Trabalhar, commitar...
git add .
git commit -m "Implementa consulta de CEP via ViaCEP API"

# Voltar para main e mergear
git checkout main
git merge feature/consumir-api-cep

# Deletar branch usada
git branch -d feature/consumir-api-cep
```

---

## Exercicios Praticos

### Exercicio 1 - Consulta de CEP
Crie `src/cep.ts` que:
1. Consulta um CEP na API ViaCEP
2. Exibe o endereco formatado
3. Trate o caso de CEP invalido
4. Consulte 3 CEPs diferentes e exiba os resultados

### Exercicio 2 - Listagem de usuarios
Crie `src/usuarios.ts` que:
1. Busca usuarios da JSONPlaceholder API
2. Exiba nome, email e cidade de cada usuario
3. Filtre usuarios de uma cidade especifica
4. Calcule quantas cidades diferentes existem

### Exercicio 3 - Pokedex simples
Crie `src/pokedex.ts` que:
1. Busque dados de um Pokemon por nome ou id na PokeAPI
2. Exiba: nome, tipo(s), peso, altura
3. Busque 5 Pokemons e exiba uma tabela comparativa

### Exercicio 4 - Preparacao para a proxima aula
Crie `src/mini-api.ts` que:
1. Defina a interface `Produto` (id, nome, preco, estoque)
2. Crie um array em memoria com 5 produtos
3. Implemente funcoes: `listar()`, `buscar(id)`, `criar(produto)`, `remover(id)`
4. Pense: como transformar essas funcoes em endpoints de uma API?

---

## Dica: IA e APIs

1. **Gerar interfaces a partir de APIs:**
   Faca uma requisicao e peca ao Copilot: *"Gere uma interface TypeScript para essa resposta JSON"*

2. **Entender status codes:**
   *"Quando devo usar 200 vs 201 vs 204?"*

3. **Testar APIs:** Recomende aos alunos instalar o **Thunder Client** (extensao do VS Code) para testar APIs visualmente, ou usar o `curl` no terminal.

> **Exercicio com IA:** No Exercicio 3, use o Copilot para gerar a interface do Pokemon baseada na resposta da API. Depois, adicione propriedades que o Copilot pode ter esquecido.

---

## Leitura Complementar

- [MDN - HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP)
- [MDN - Fetch API](https://developer.mozilla.org/pt-BR/docs/Web/API/Fetch_API)
- [REST API Tutorial](https://restfulapi.net/)
