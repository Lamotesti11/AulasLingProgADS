# Aula 02 - Introducao ao JavaScript e ao Node.js

## Objetivos da Aula

- Entender o que e JavaScript e seu papel no desenvolvimento moderno
- Instalar e configurar o Node.js
- Executar codigo JavaScript fora do navegador
- Inicializar um projeto Node.js com `npm init`
- Entender o package.json e o ecossistema npm

---

## 1. O que e JavaScript?

JavaScript e a linguagem de programacao mais utilizada no mundo. Originalmente criada para rodar dentro de navegadores (Chrome, Firefox, etc.), hoje pode ser usada em:

- **Frontend:** interfaces web (React, Angular, Vue)
- **Backend:** servidores e APIs (Node.js)
- **Mobile:** aplicativos (React Native)
- **Desktop:** aplicativos (Electron)

### JavaScript vs C - Diferencas principais

Como voces ja conhecem C, aqui vao as diferencas mais importantes:

| Caracteristica | C | JavaScript |
|----------------|---|------------|
| Tipagem | Estatica (int, float, char) | Dinamica (tipos inferidos) |
| Compilacao | Compilado (gcc) | Interpretado / JIT |
| Memoria | Gerenciamento manual (malloc/free) | Garbage Collector automatico |
| Ponto e virgula | Obrigatorio | Opcional (mas recomendado) |
| Paradigma | Procedural | Multi-paradigma (funcional, OOP) |

---

## 2. O que e Node.js?

**Node.js** e um **runtime** que permite executar JavaScript fora do navegador. Ele usa o motor V8 do Google Chrome.

Antes do Node (1995-2009): JavaScript so rodava no navegador.
Depois do Node (2009+): JavaScript pode rodar no servidor, em scripts, em CLIs, em qualquer lugar.

### Por que Node.js?

- Mesmo idioma no frontend e backend
- Enorme ecossistema de pacotes (npm)
- Excelente para APIs e aplicacoes em tempo real
- Usado por Netflix, Uber, LinkedIn, PayPal

---

## 3. Instalacao do Node.js

### 3.1 Instalando

**Recomendado:** Baixe a versao **LTS** (Long Term Support) em https://nodejs.org

**macOS (com Homebrew):**
```bash
brew install node
```

**Linux (Ubuntu/Debian):**
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

### 3.2 Verificando a instalacao

```bash
node --version
# v20.x.x (ou superior)

npm --version
# 10.x.x (ou superior)
```

O `npm` (Node Package Manager) e instalado automaticamente junto com o Node.

---

## 4. Primeiros passos - Executando JavaScript

### 4.1 Modo interativo (REPL)

O Node tem um modo interativo, semelhante ao Python:

```bash
node
```

Agora voce pode digitar JavaScript diretamente:

```javascript
> 2 + 2
4
> "Ola" + " Mundo"
'Ola Mundo'
> Math.random()
0.7234891023
> .exit
```

### 4.2 Executando um arquivo

Crie um arquivo `hello.js`:

```javascript
console.log("Ola, mundo!");
console.log("Meu primeiro programa em Node.js");

const nome = "Estudante";
console.log("Bem-vindo, " + nome + "!");
```

Execute no terminal:

```bash
node hello.js
```

> **Comparando com C:** Em C voce precisaria de `#include <stdio.h>`, funcao `main()`, compilar com `gcc` e depois executar. Em Node, basta escrever e rodar.

---

## 5. Inicializando um Projeto Node.js

### 5.1 Criando o projeto

```bash
# Crie e entre na pasta do projeto
mkdir meu-projeto
cd meu-projeto

# Inicialize o projeto Node.js
npm init -y
```

O flag `-y` aceita todas as opcoes padrao. Isso cria o arquivo `package.json`.

### 5.2 Entendendo o package.json

```json
{
  "name": "meu-projeto",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

| Campo | Significado |
|-------|-------------|
| `name` | Nome do projeto |
| `version` | Versao atual |
| `main` | Arquivo principal de entrada |
| `scripts` | Comandos personalizados (atalhos) |

### 5.3 Adicionando scripts personalizados

Edite o `package.json`:

```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "node --watch index.js"
  }
}
```

Agora voce pode executar:

```bash
npm start        # Executa node index.js
npm run dev      # Executa com --watch (reinicia ao salvar)
```

> **Dica:** O flag `--watch` (Node 18+) reinicia automaticamente o programa quando voce salva um arquivo. Muito util durante o desenvolvimento!

---

## 6. Sintaxe Basica do JavaScript

### 6.1 Variaveis

```javascript
// Tres formas de declarar variaveis:
var antigaForma = "evite usar var";  // escopo global/funcao (problematico)
let idade = 20;                       // escopo de bloco (use para valores que mudam)
const PI = 3.14159;                   // constante (use para valores fixos)
```

**Regra pratica:** Use `const` por padrao. So use `let` quando precisar reatribuir.

### 6.2 Tipos de dados

```javascript
// String (texto)
const nome = "Maria";

// Number (inteiro e decimal sao o mesmo tipo!)
const idade = 25;
const altura = 1.68;

// Boolean
const aprovado = true;

// Null e Undefined
const vazio = null;        // ausencia intencional de valor
let indefinido;            // declarada mas sem valor atribuido (undefined)
```

> **Diferenca do C:** Em C existem `int`, `float`, `double`, `char`. Em JS, `number` abrange todos os numericos, e nao existe `char` (apenas `string`).

### 6.3 Template Literals (Template Strings)

```javascript
const nome = "Carlos";
const idade = 22;

// Concatenacao classica (como em C com sprintf)
console.log("Nome: " + nome + ", Idade: " + idade);

// Template literal (moderno e mais legivel)
console.log(`Nome: ${nome}, Idade: ${idade}`);
```

Note o uso de **crases** (`` ` ``) e `${}` para interpolar variaveis.

### 6.4 Entrada de dados pelo terminal

```javascript
// Importe o modulo readline do Node
const readline = require("readline");

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

rl.question("Qual seu nome? ", (resposta) => {
  console.log(`Ola, ${resposta}!`);
  rl.close();
});
```

> **Comparando com C:** Isso seria equivalente ao `scanf()` ou `fgets()`. A diferenca e que em Node a leitura e **assincrona** (voltaremos nesse conceito nas proximas aulas).

---

## 7. Fluxo de Trabalho com Git

A cada exercicio ou etapa concluida, pratique o Git:

```bash
# Verifique o que mudou
git status

# Adicione os arquivos
git add .

# Commit com mensagem descritiva
git commit -m "Adiciona script hello.js com saudacao personalizada"

# Envie para o GitHub
git push
```

**Dica:** Faca commits pequenos e frequentes. Nao espere terminar tudo para commitar.

---

## 8. VS Code - Configuracao Recomendada

### Extensoes uteis

1. **GitHub Copilot** - assistente de IA para codigo
2. **ESLint** - detecta erros e problemas no codigo
3. **Prettier** - formatacao automatica

### Atalhos essenciais

| Atalho | Acao |
|--------|------|
| `Ctrl + '` | Abrir/fechar terminal integrado |
| `Ctrl + S` | Salvar arquivo |
| `Ctrl + Shift + P` | Paleta de comandos |
| `Ctrl + P` | Buscar arquivo por nome |

---

## Exercicios Praticos

### Exercicio 1 - Configuracao
1. Instale o Node.js e verifique a versao
2. Crie uma pasta `aula02-exercicios`
3. Inicialize com `npm init -y`
4. Inicialize um repositorio Git
5. Crie um `.gitignore` com `node_modules/`
6. Commit e push

### Exercicio 2 - Calculadora simples
Crie um arquivo `calculadora.js` que:
1. Define duas variaveis numericas
2. Exibe no console a soma, subtracao, multiplicacao e divisao
3. Use template literals para formatar a saida

```
Exemplo de saida:
Operacoes com 10 e 3:
Soma: 13
Subtracao: 7
Multiplicacao: 30
Divisao: 3.33
```

### Exercicio 3 - Perfil do aluno
Crie um arquivo `perfil.js` que:
1. Declara constantes para: nome, idade, curso, semestre
2. Exibe todos os dados formatados com template literals
3. Calcule e exiba o ano previsto de formatura

### Exercicio 4 - Conversor de temperatura
Crie um arquivo `conversor.js` que:
1. Define uma temperatura em Celsius como constante
2. Converte para Fahrenheit: `F = C * 9/5 + 32`
3. Converte para Kelvin: `K = C + 273.15`
4. Exibe as tres temperaturas formatadas

---

## Dica: Usando IA no dia a dia

Com o **GitHub Copilot** ativado no VS Code, experimente:

1. Escreva um comentario descrevendo o que quer fazer:
   ```javascript
   // Funcao que converte temperatura de Celsius para Fahrenheit
   ```
   O Copilot sugerira a implementacao. Pressione `Tab` para aceitar.

2. Use o **Copilot Chat** (Ctrl+Shift+I) para perguntar:
   - *"Qual a diferenca entre let e const?"*
   - *"Como leio input do usuario no Node.js?"*

> **Lembre-se:** A IA e uma ferramenta de apoio. Sempre entenda o codigo antes de aceitar uma sugestao. Nos exercicios, tente resolver sozinho primeiro e use a IA para tirar duvidas ou comparar sua solucao.

---

## Leitura Complementar

- [Node.js - Documentacao Oficial](https://nodejs.org/docs/latest/api/)
- [MDN - JavaScript Guide](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide)
