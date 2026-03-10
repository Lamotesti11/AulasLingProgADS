# Aula 01 - Git e GitHub

## Objetivos da Aula

- Entender o que e controle de versao e por que e essencial no desenvolvimento de software
- Instalar e configurar o Git
- Aprender os comandos fundamentais do Git
- Criar uma conta no GitHub e conectar com o repositorio local
- Fazer o primeiro commit e push

---

## 1. O que e Controle de Versao?

Imagine que voce esta escrevendo um TCC. Voce salva versoes como:

```
tcc-final.docx
tcc-final-v2.docx
tcc-final-v2-corrigido.docx
tcc-final-AGORA-VAI.docx
```

Controle de versao resolve esse problema. O **Git** registra cada alteracao feita no codigo, permitindo:

- Voltar a qualquer versao anterior
- Trabalhar em equipe sem sobrescrever o trabalho dos outros
- Saber quem fez cada alteracao e quando

### Git vs GitHub

| Git | GitHub |
|-----|--------|
| Ferramenta local instalada no seu computador | Plataforma online que hospeda repositorios Git |
| Funciona offline | Requer internet |
| Controla versoes dos arquivos | Facilita colaboracao, code review, issues |

---

## 2. Instalacao e Configuracao

### 2.1 Instalando o Git

**Windows:**
- Acesse https://git-scm.com/downloads e baixe o instalador
- Durante a instalacao, mantenha as opcoes padrao
- Marque a opcao "Git Bash Here" (util para terminal)

**macOS:**
```bash
# Se tiver Homebrew:
brew install git

# Ou instale as Command Line Tools:
xcode-select --install
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install git
```

### 2.2 Verificando a instalacao

```bash
git --version
# Deve exibir algo como: git version 2.43.0
```

### 2.3 Configuracao inicial

Essas configuracoes sao feitas **uma unica vez** e identificam voce nos commits:

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu.email@exemplo.com"
```

Para verificar as configuracoes:

```bash
git config --list
```

---

## 3. Conceitos Fundamentais

### 3.1 Repositorio (repo)

Um repositorio e uma pasta cujos arquivos sao rastreados pelo Git. Ao inicializar um repositorio, o Git cria uma pasta oculta `.git/` que armazena todo o historico.

### 3.2 Os tres estados do Git

```
 Diretorio de Trabalho  -->  Staging Area  -->  Repositorio Local
   (Working Directory)       (git add)          (git commit)
```

1. **Working Directory:** onde voce edita os arquivos normalmente
2. **Staging Area (Index):** area de preparacao - voce escolhe quais alteracoes vao entrar no proximo commit
3. **Repositorio (.git):** onde os commits ficam salvos permanentemente

### 3.3 O que e um Commit?

Um commit e um "snapshot" (foto) do estado dos seus arquivos em um momento especifico. Cada commit tem:

- Um identificador unico (hash SHA)
- Uma mensagem descritiva
- Autor e data
- Referencia ao commit anterior

---

## 4. Passo a Passo - Primeiros Comandos

### 4.1 Criando um repositorio local

```bash
# Crie uma pasta para o projeto
mkdir meu-primeiro-repo
cd meu-primeiro-repo

# Inicialize o repositorio Git
git init
```

Voce vera a mensagem: `Initialized empty Git repository in .../meu-primeiro-repo/.git/`

### 4.2 Criando e rastreando arquivos

```bash
# Crie um arquivo
echo "# Meu Primeiro Repositorio" > README.md

# Verifique o status
git status
```

O `git status` mostrara o arquivo como **Untracked** (nao rastreado). Isso significa que o Git sabe que o arquivo existe, mas ainda nao o esta monitorando.

### 4.3 Adicionando ao Staging

```bash
# Adicionar um arquivo especifico
git add README.md

# Ou adicionar todos os arquivos modificados
git add .
```

Agora o arquivo esta na **Staging Area**, pronto para ser commitado.

### 4.4 Fazendo o primeiro commit

```bash
git commit -m "Primeiro commit: adiciona README"
```

A flag `-m` permite escrever a mensagem diretamente no comando.

### 4.5 Verificando o historico

```bash
git log
```

Voce vera algo como:

```
commit a1b2c3d4e5f6... (HEAD -> main)
Author: Seu Nome <seu.email@exemplo.com>
Date:   Mon Mar 10 2026

    Primeiro commit: adiciona README
```

### 4.6 Fluxo completo resumido

```bash
# Editar arquivos...
git status          # Ver o que mudou
git add .           # Preparar alteracoes
git commit -m "mensagem descritiva"  # Salvar snapshot
git log --oneline   # Ver historico resumido
```

---

## 5. GitHub - Repositorio Remoto

### 5.1 Criando uma conta

1. Acesse https://github.com
2. Crie uma conta com seu email institucional (se possivel)
3. Confirme o email

### 5.2 Criando um repositorio no GitHub

1. Clique em **"New repository"** (botao verde)
2. De um nome ao repositorio (ex: `meu-primeiro-repo`)
3. **Nao** marque "Initialize this repository with a README" (ja temos um local)
4. Clique em **"Create repository"**

### 5.3 Conectando local ao remoto

O GitHub mostrara os comandos. Copie e execute:

```bash
# Adicionar o endereco do repositorio remoto
git remote add origin https://github.com/seu-usuario/meu-primeiro-repo.git

# Enviar os commits locais para o GitHub
git push -u origin main
```

> **Nota:** Na primeira vez, o Git pedira autenticacao. Recomenda-se configurar um **Personal Access Token** ou **SSH Key**.

### 5.4 Clonando um repositorio existente

Para baixar um repositorio que ja existe no GitHub:

```bash
git clone https://github.com/usuario/nome-do-repo.git
```

---

## 6. Boas Praticas de Commit

### Mensagens de commit

**Ruim:**
```
git commit -m "alteracoes"
git commit -m "fix"
git commit -m "atualizacao"
```

**Bom:**
```
git commit -m "Adiciona validacao de email no formulario de cadastro"
git commit -m "Corrige calculo de desconto para valores acima de 100"
git commit -m "Remove funcao duplicada de formatacao de data"
```

### Regras praticas

1. Use o imperativo: "Adiciona", "Corrige", "Remove" (nao "Adicionei", "Corrigido")
2. Seja especifico: diga **o que** e **onde**
3. Commits pequenos e frequentes > commits gigantes e raros

---

## 7. Comandos Essenciais - Resumo

| Comando | O que faz |
|---------|-----------|
| `git init` | Inicializa um repositorio |
| `git status` | Mostra o estado atual dos arquivos |
| `git add <arquivo>` | Adiciona arquivo ao staging |
| `git add .` | Adiciona todos os arquivos modificados |
| `git commit -m "msg"` | Cria um commit com mensagem |
| `git log` | Mostra historico de commits |
| `git log --oneline` | Historico resumido (uma linha por commit) |
| `git remote add origin <url>` | Conecta a um repositorio remoto |
| `git push` | Envia commits para o remoto |
| `git pull` | Baixa atualizacoes do remoto |
| `git clone <url>` | Clona um repositorio existente |
| `git diff` | Mostra alteracoes nao commitadas |

---

## 8. .gitignore

O arquivo `.gitignore` diz ao Git quais arquivos **nao** devem ser rastreados:

```gitignore
# Dependencias do Node
node_modules/

# Variaveis de ambiente
.env

# Arquivos de build
dist/

# Arquivos do sistema
.DS_Store
Thumbs.db
```

Crie este arquivo na raiz do projeto **antes** de adicionar os arquivos ao Git.

---

## Exercicios Praticos

### Exercicio 1 - Repositorio pessoal
1. Crie uma pasta chamada `exercicios-git`
2. Inicialize um repositorio Git
3. Crie um arquivo `sobre-mim.txt` com seu nome e curso
4. Faca o commit
5. Crie um repositorio no GitHub e faca o push

### Exercicio 2 - Multiplos commits
1. No mesmo repositorio, crie um arquivo `linguagens.txt`
2. Adicione 3 linguagens que voce conhece, fazendo **um commit por linguagem**
3. Use `git log --oneline` para ver o historico

### Exercicio 3 - .gitignore
1. Crie um arquivo `segredo.txt` com uma senha ficticia
2. Crie um `.gitignore` que ignore esse arquivo
3. Faca `git status` e confirme que o arquivo nao aparece
4. Commit e push

---

## Dica: Usando IA para aprender Git

O **GitHub Copilot Chat** no VS Code pode te ajudar com Git. Experimente perguntar:

- *"Como desfaco o ultimo commit sem perder as alteracoes?"*
- *"Qual a diferenca entre git merge e git rebase?"*
- *"Me explique o que esse comando faz: git reset --soft HEAD~1"*

> **Importante:** Use a IA como ferramenta de aprendizado. Entenda o que cada comando faz antes de executar, especialmente comandos destrutivos como `reset` e `force push`.

---

## Leitura Complementar

- [Git - Documentacao Oficial](https://git-scm.com/doc)
- [GitHub Docs - Quickstart](https://docs.github.com/en/get-started/quickstart)
