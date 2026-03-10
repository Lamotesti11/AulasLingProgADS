# Aula 02 - Introdução ao JavaScript e ao Node.js

## Objetivos da Aula

- Entender o que é JavaScript e seu papel no desenvolvimento moderno
- Instalar e configurar o Node.js
- Executar código JavaScript fora do navegador
- Inicializar um projeto Node.js com `npm init`
- Entender o package.json e o ecossistema npm

---

## 1. O que é JavaScript?

JavaScript é a linguagem de programação mais utilizada no mundo. Originalmente criada para rodar dentro de navegadores (Chrome, Firefox etc.), hoje pode ser usada em:

- **Frontend:** interfaces web (React, Angular, Vue)
- **Backend:** servidores e APIs (Node.js)
- **Mobile:** aplicativos (React Native)
- **Desktop:** aplicativos (Electron)

### JavaScript vs C - Diferenças principais

Como vocês já conhecem C, aqui vão as diferenças mais importantes:

| Característica  | C                                  | JavaScript                       |
| --------------- | ---------------------------------- | -------------------------------- |
| Tipagem         | Estática (int, float, char)        | Dinâmica (tipos inferidos)       |
| Compilação      | Compilado (gcc)                    | Interpretado / JIT               |
| Memória         | Gerenciamento manual (malloc/free) | Garbage Collector automático     |
| Ponto e vírgula | Obrigatório                        | Opcional (mas recomendado)       |
| Paradigma       | Procedural                         | Multi-paradigma (funcional, OOP) |

---

## 2. O que é Node.js?

**Node.js** é um **runtime** que permite executar JavaScript fora do navegador. Ele usa o motor V8 do Google Chrome.

Antes do Node (1995-2009): JavaScript só rodava no navegador.
Depois do Node (2009+): JavaScript pode rodar no servidor, em scripts, em CLIs, em qualquer lugar.

### Por que Node.js?

- Mesmo idioma no frontend e backend
- Enorme ecossistema de pacotes (npm)
- Excelente para APIs e aplicações em tempo real
- Usado por Netflix, Uber, LinkedIn e PayPal

---

## 3. Instalação do Node.js

### 3.1 Instalando

**Recomendado:** Baixe a versão **LTS** (Long Term Support) em https://nodejs.org

**macOS (com Homebrew):**

```bash
brew install node
```

**Linux (Ubuntu/Debian):**

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

### 3.2 Verificando a instalação

```bash
node --version
# v20.x.x (ou superior)

npm --version
# 10.x.x (ou superior)
```

O `npm` (Node Package Manager) é instalado automaticamente junto com o Node.

---

## 4. Primeiros passos - Executando JavaScript

### 4.1 Modo interativo (REPL)

O Node tem um modo interativo, semelhante ao Python:

```bash
node
```

Agora você pode digitar JavaScript diretamente:

```javascript
> 2 + 2
4
> "Olá" + " Mundo"
'Olá Mundo'
> Math.random()
0.7234891023
> .exit
```

### 4.2 Executando um arquivo

Crie um arquivo `hello.js`:

```javascript
console.log('Olá, mundo!');
console.log('Meu primeiro programa em Node.js');

const nome = 'Estudante';
console.log('Bem-vindo, ' + nome + '!');
```

Execute no terminal:

```bash
node hello.js
```

> **Comparando com C:** Em C você precisaria de `#include <stdio.h>`, da função `main()`, compilar com `gcc` e depois executar. Em Node, basta escrever e rodar.

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

A flag `-y` aceita todas as opções padrão. Isso cria o arquivo `package.json`.

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

| Campo     | Significado                       |
| --------- | --------------------------------- |
| `name`    | Nome do projeto                   |
| `version` | Versão atual                      |
| `main`    | Arquivo principal de entrada      |
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

Agora você pode executar:

```bash
npm start        # Executa node index.js
npm run dev      # Executa com --watch (reinicia ao salvar)
```

> **Dica:** A flag `--watch` (Node 18+) reinicia automaticamente o programa quando você salva um arquivo. Muito útil durante o desenvolvimento!

---

## 6. Sintaxe Básica do JavaScript

### 6.1 Variáveis

```javascript
// Três formas de declarar variáveis:
var antigaForma = 'evite usar var'; // escopo global/função (problemático)
let idade = 20; // escopo de bloco (use para valores que mudam)
const PI = 3.14159; // constante (use para valores fixos)
```

**Regra prática:** Use `const` por padrão. Só use `let` quando precisar reatribuir.

### 6.2 Tipos de dados

```javascript
// String (texto)
const nome = 'Maria';

// Number (inteiro e decimal são o mesmo tipo!)
const idade = 25;
const altura = 1.68;

// Boolean
const aprovado = true;

// Null e Undefined
const vazio = null; // ausência intencional de valor
let indefinido; // declarada, mas sem valor atribuído (undefined)
```

> **Diferença do C:** Em C existem `int`, `float`, `double` e `char`. Em JS, `number` abrange todos os numéricos, e não existe `char` (apenas `string`).

### 6.3 Template Literals (Template Strings)

```javascript
const nome = 'Carlos';
const idade = 22;

// Concatenação clássica (como em C com sprintf)
console.log('Nome: ' + nome + ', Idade: ' + idade);

// Template literal (moderno e mais legível)
console.log(`Nome: ${nome}, Idade: ${idade}`);
```

Note o uso de **crases** (`` ` ``) e `${}` para interpolar variáveis.

### 6.4 Entrada de dados pelo terminal

```javascript
// Importe o módulo readline do Node
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

rl.question('Qual é o seu nome? ', (resposta) => {
  console.log(`Olá, ${resposta}!`);
  rl.close();
});
```

> **Comparando com C:** Isso seria equivalente ao `scanf()` ou `fgets()`. A diferença é que em Node a leitura é **assíncrona** (voltaremos nesse conceito nas próximas aulas).

---

## 7. Fluxo de Trabalho com Git

A cada exercício ou etapa concluída, pratique o Git:

```bash
# Verifique o que mudou
git status

# Adicione os arquivos
git add .

# Commit com mensagem descritiva
git commit -m "Adiciona script hello.js com saudação personalizada"

# Envie para o GitHub
git push
```

**Dica:** Faça commits pequenos e frequentes. Não espere terminar tudo para commitar.

---

## 8. VS Code - Configuração Recomendada

### Extensões úteis

1. **GitHub Copilot** - assistente de IA para código
2. **ESLint** - detecta erros e problemas no código
3. **Prettier** - formatação automática

### Atalhos essenciais

| Atalho             | Ação                            |
| ------------------ | ------------------------------- |
| `Ctrl + '`         | Abrir/fechar terminal integrado |
| `Ctrl + S`         | Salvar arquivo                  |
| `Ctrl + Shift + P` | Paleta de comandos              |
| `Ctrl + P`         | Buscar arquivo por nome         |

---

## Exercícios Práticos

### Exercício 1 - Configuração

1. Instale o Node.js e verifique a versão
2. Crie uma pasta `aula02-exercicios`
3. Inicialize com `npm init -y`
4. Inicialize um repositório Git
5. Crie um `.gitignore` com `node_modules/`
6. Commit e push

### Exercício 2 - Calculadora simples

Crie um arquivo `calculadora.js` que:

1. Defina duas variáveis numéricas
2. Exiba no console a soma, a subtração, a multiplicação e a divisão
3. Use template literals para formatar a saída

```
Exemplo de saída:
Operações com 10 e 3:
Soma: 13
Subtração: 7
Multiplicação: 30
Divisão: 3.33
```

### Exercício 3 - Perfil do aluno

Crie um arquivo `perfil.js` que:

1. Declare constantes para: nome, idade, curso e semestre
2. Exiba todos os dados formatados com template literals
3. Calcule e exiba o ano previsto de formatura

### Exercício 4 - Conversor de temperatura

Crie um arquivo `conversor.js` que:

1. Defina uma temperatura em Celsius como constante
2. Converta para Fahrenheit: `F = C * 9/5 + 32`
3. Converta para Kelvin: `K = C + 273.15`
4. Exiba as três temperaturas formatadas

---

## Dica: Usando IA no dia a dia

Com o **GitHub Copilot** ativado no VS Code, experimente:

1. Escreva um comentário descrevendo o que quer fazer:

   ```javascript
   // Função que converte temperatura de Celsius para Fahrenheit
   ```

   O Copilot sugerirá a implementação. Pressione `Tab` para aceitar.

2. Use o **Copilot Chat** (Ctrl+Shift+I) para perguntar:
   - _"Qual é a diferença entre let e const?"_
   - _"Como leio input do usuário no Node.js?"_

> **Lembre-se:** A IA é uma ferramenta de apoio. Sempre entenda o código antes de aceitar uma sugestão. Nos exercícios, tente resolver sozinho primeiro e use a IA para tirar dúvidas ou comparar sua solução.

---

## Leitura Complementar

- [Node.js - Documentação Oficial](https://nodejs.org/docs/latest/api/)
- [MDN - JavaScript Guide](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide)
