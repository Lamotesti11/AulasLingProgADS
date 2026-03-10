# Aula 15 - Projeto Final: API Completa

## Objetivo

Aplicar todos os conhecimentos adquiridos ao longo da disciplina na construcao de uma **API REST completa**, desde a estrutura de dados ate a validacao e tratamento de erros.

---

## Descricao do Projeto

Cada equipe (ou individualmente) deve construir uma **API REST** para um dos temas propostos abaixo, utilizando:

- **TypeScript** com tipagem rigorosa
- **Node.js** com **Express**
- **Persistencia em arquivo JSON**
- **Validacao com Zod**
- **Tratamento de erros padronizado**
- **Git** com commits organizados e descritivos

---

## Temas Sugeridos

Escolha **um** dos temas abaixo (ou proponha um tema ao professor):

### 1. Sistema de Biblioteca
- Livros (titulo, autor, isbn, genero, disponivel)
- Emprestimos (livroId, nomeAluno, dataEmprestimo, dataDevolucao, status)
- Regras: livro indisponivel nao pode ser emprestado; maximo 3 emprestimos por aluno

### 2. Sistema de Pedidos (Lanchonete)
- Produtos (nome, preco, categoria, disponivel)
- Pedidos (produtos[], cliente, status, total, dataCriacao)
- Regras: calcular total automaticamente; pedido nao pode ter produto indisponivel

### 3. Sistema de Tarefas (Kanban)
- Projetos (nome, descricao, dataCriacao)
- Tarefas (projetoId, titulo, descricao, status: "todo"|"doing"|"done", prioridade, responsavel)
- Regras: tarefa precisa de projeto valido; limitar 5 tarefas "doing" por responsavel

### 4. Sistema de Notas (Boletim)
- Alunos (nome, ra, curso)
- Disciplinas (nome, cargaHoraria, professor)
- Notas (alunoId, disciplinaId, nota, semestre)
- Regras: nota entre 0-10; calcular media ponderada por carga horaria

---

## Requisitos Obrigatorios

### Estrutura do Projeto

```
projeto-final/
  data/                     # Arquivos JSON (banco de dados)
  src/
    server.ts               # Ponto de entrada
    routes/                 # Definicao de rotas
    services/               # Logica de negocio
    data/                   # Repositorios (leitura/escrita JSON)
    schemas/                # Schemas Zod
    middlewares/             # Middlewares customizados
    errors/                 # Classes de erro
    models/                 # Interfaces/Types
  package.json
  tsconfig.json
  .gitignore
  README.md                 # Documentacao da API
```

### Funcionalidades

| Requisito | Descricao |
|-----------|-----------|
| CRUD completo | Pelo menos 2 recursos com todas as operacoes (GET, POST, PUT, DELETE) |
| Validacao | Todos os inputs validados com Zod |
| Tratamento de erros | Middleware centralizado com erros customizados |
| Persistencia | Dados salvos em arquivo JSON |
| Filtros | Pelo menos 2 query params para filtragem |
| Relacionamento | Os recursos devem se relacionar (ex: pedido tem produtos) |
| Regras de negocio | Pelo menos 2 regras na camada de servicos |
| Middlewares | Logger + pelo menos 1 middleware customizado |

### Git

| Requisito | Descricao |
|-----------|-----------|
| Minimo 10 commits | Commits descritivos e atomicos |
| Branches | Pelo menos 1 feature branch mergeada na main |
| .gitignore | node_modules, dist, .env ignorados |
| README.md | Documentacao completa do projeto |

---

## Checklist de Conceitos da Disciplina

Seu projeto deve demonstrar dominio de:

- [ ] Variaveis e constantes com tipagem (Aula 03)
- [ ] Operadores e expressoes (Aula 03)
- [ ] Condicionais - if/else, switch (Aula 04)
- [ ] Lacos de repeticao - for, while (Aula 04)
- [ ] Funcoes com parametros tipados e retorno (Aula 05)
- [ ] Arrays e metodos (map, filter, find, reduce) (Aula 06)
- [ ] Objetos e interfaces (Aula 07)
- [ ] Union types ou generics (Aula 08)
- [ ] Modulos - import/export (Aula 09)
- [ ] Manipulacao de arquivos - fs (Aula 10)
- [ ] Conceitos REST e HTTP (Aula 11)
- [ ] Express - rotas e parametros (Aula 12)
- [ ] Middlewares (Aula 13)
- [ ] Validacao e tratamento de erros (Aula 14)

---

## Documentacao da API (README.md do projeto)

O README deve conter:

```markdown
# Nome do Projeto

Descricao breve do que a API faz.

## Como executar

1. Clone o repositorio
2. `npm install`
3. `npm run dev`
4. Acesse http://localhost:3000

## Endpoints

### Recurso 1

| Metodo | Rota | Descricao |
|--------|------|-----------|
| GET | /recurso | Listar todos |
| GET | /recurso/:id | Buscar por ID |
| POST | /recurso | Criar novo |
| PUT | /recurso/:id | Atualizar |
| DELETE | /recurso/:id | Remover |

### Exemplos de requisicao

(Incluir exemplos com curl ou JSON para cada endpoint)

## Tecnologias

- TypeScript
- Node.js
- Express
- Zod
```

---

## Criterios de Avaliacao

| Criterio | Peso |
|----------|------|
| Funcionalidade (CRUD funciona corretamente) | 25% |
| Qualidade do codigo (tipagem, organizacao, legibilidade) | 25% |
| Validacao e tratamento de erros | 20% |
| Uso do Git (commits, branches, .gitignore) | 15% |
| Documentacao (README com endpoints e exemplos) | 15% |

---

## Cronograma Sugerido

### Em sala (Aula 15)
1. Definir tema e escopo
2. Criar repositorio e estrutura do projeto
3. Definir interfaces e schemas
4. Implementar repositorio de dados (persistencia JSON)
5. Implementar rotas e servicos
6. Testar todos os endpoints

### Em casa (entrega)
7. Refinar validacoes e regras de negocio
8. Adicionar middleware customizado
9. Escrever documentacao
10. Revisar commits e fazer merge das branches

---

## Dica Final: IA como Parceira de Projeto

Ao longo desta disciplina, voces aprenderam a usar a IA como ferramenta de apoio. No projeto final, usem de forma estrategica:

### Quando usar a IA
- Para gerar **schemas Zod** a partir das interfaces
- Para gerar **dados de teste** no JSON
- Para **debugar erros** que voce nao consegue resolver
- Para **revisar codigo** e sugerir melhorias
- Para gerar **comandos curl** de teste

### Quando NAO usar a IA
- Para gerar o projeto inteiro sem entender o que esta acontecendo
- Para copiar codigo sem adaptar ao seu contexto
- Para pular etapas de aprendizado

### Reflexao para carreira
A IA nao substitui o programador - ela **amplifica** suas capacidades. Um dev que entende os fundamentos e usa IA e produtivo e mais eficaz do que:
- Um dev que nao usa IA (mais lento em tarefas repetitivas)
- Alguem que so usa IA sem entender o codigo (nao sabe debugar, nao sabe adaptar)

O diferencial e **entender o que voce esta fazendo** e usar a IA para ir mais rapido.

---

## Entrega

- **Repositorio no GitHub** (publico ou privado com acesso ao professor)
- **Prazo:** (definido pelo professor)
- **Apresentacao:** breve demonstracao da API funcionando (5-10 min por equipe)
