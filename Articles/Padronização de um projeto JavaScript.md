<!-- Simple logo -->
<a href="#padronização-de-um-projeto-javascript"><img width="400px" src="https://res.cloudinary.com/practicaldev/image/fetch/s--mqD0qz2---/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/anp5enlwcmw3ogu13a46.png" align="right" /></a>


# Padronização de um projeto JavaScript

Mesmo se você estiver em um projeto pessoal, trabalhando sozinho, configurar um estilo padrão para o seu código vai ajudar bastante a manter as coisas organizadas. Há diversos estilos no mercado (você pode criar o seu, inclusive), como gosto pessoal, há preferência para _Airbnb JavaScript Style Guide_. É com base nesse guia de estilo que nosso código será formatado. Lembre-se: **organização, padronização, legibilidade e separação de responsabilidades nunca é demais em projetos de desenvolvimento.**

De início, três extensões serão utilizadas para auxiliar na padronização de um projeto como um todo e duas ferramentas para a padronização de commits do Git. Neste documento será apresentado o passo a passo para aplicação destas configurações em seu projeto atual e padronização para posteriores alterações.

Basicamente, o Linter é quem vai definir as regras que serão seguidas pelos desenvolvedores do projeto (utilizando padrões pre-definidos por organizações de alto nível, como Google, Amazon, Airbnb...). Já o Prettier é quem vai realizar a formatação no projeto de acordo com as regras definidas pelo Linter

## Preparando Visual Studio Code

Para realizar a padronização, será utilizadas três extensões do VSCode: EditorConfig, Linter da linguagem (ESlint, Pylint, etc. Para saber qual Linter utilizar para sua linguagem, verificar [All supported linters][1]) e Prettier. Então, baixe estas respectivas extensões no editor pelos seguintes IDs: EditorConfig (_editorconfig.editorconfig_),Prettier (_esbenp.prettier-vscode_) e ESLint(_dbaeumer.vscode-eslint_), ou, utilize um arquivo `extensions.json` dentro da pasta `.vscode/` do diretório do atual projeto:

```json
{
  "recommendations": [
    "editorconfig.editorconfig",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode"
  ]
}
```

Como configuração inicial, é necessário habilitar uma opção de configuração "Format on save". Para isso, siga as instruções abaixo:

- No VScode, abra **Settings** (<kbd>Ctrl</kbd> + <kbd>,</kbd>) e procure por "Format on save". Marque a opção: "**Editor: Format on save** - _Format a file on save. A formatter must be available, the file must not be saved after delay, and the editor must not be shutting down._"

## EditorConfig

Caracterizado por uma fácil configuração, o EditorConfig ajuda a manter estilos de codificação consistentes para vários desenvolvedores que trabalham no mesmo projeto em vários editores e IDEs. Consiste basicamente em um formato de arquivo (`.editorconfig`) para definir estilos de codificação e uma coleção de plug-ins de editores de texto que permitem aos editores ler o formato do arquivo e aderir aos estilos definidos. Os arquivos EditorConfig são facilmente legíveis e funcionam bem com sistemas de controle de versão. A sua instalação na máquina é importante que seja feita em ambiente de desenvolvimento:

```console
npm install -D editorconfig
```

Exemplo inicial de um arquivo `.editorconfig`:

```console
# EditorConfig is awesome: https://EditorConfig.org

# top-most EditorConfig file
root = true

# Unix-style newlines with a newline ending every file
[*]
end_of_line = lf
insert_final_newline = true

# Matches multiple files with brace expansion notation
# Set default charset
[*.{js,py}]
charset = utf-8

# 4 space indentation
[*.py]
indent_style = space
indent_size = 4

# Tab indentation (no size specified)
[Makefile]
indent_style = tab

# Indentation override for all JS under lib directory
[lib/**.js]
indent_style = space
indent_size = 2

# Matches the exact files either package.json or .travis.yml
[{package.json,.travis.yml}]
indent_style = space
indent_size = 2
```

Com o EditorConfig instalado, basta dar um clique com o botão direito na raiz do nosso projeto e criar o arquivo do EditorConfig selecionando `Generate .editorconfig`. Feito isso, será criado um arquivo com as seguintes configurações e suas respectivas abrangências:

```console
root = true

[*]
indent_style = {space|tab}
indent_size = {4|tab}
tab_width = 2
end_of_line = {cr|lf|crlf}
charset = {utf-8|utf-16be|utf-16le|latin1}
trim_trailing_whitespace = false
insert_final_newline = true
max_line_length = 80
```

As alterações podem ser feitas de acordo com as especificações dos devs ou projetos. Como melhoria inicial do script acima, temos:

```console
root = true

[*]
indent_style = space
indent_size = 2
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
end_of_line = lf
```

Para visualizar um arquivo .editorconfig mais abrangente, vide o repositório do [RehanSaeed][2] no GitHub.

## ESLint

Antes de dar início à instalação das dependências, é necessária uma introdução à alguns conceitos importantes:

### NPM/NPX vs YARN

#### NPM e NPX

NPM (Node Package Manager) é um gerenciador de pacotes que permite desenvolvedores a compartilhar seus pacotes próprios ou instalar pacotes de outros devs. Gera um arquivo `package-lock.json` para versionar e gerenciar as dependências dos pacotes.

Já o NPX é um package runner do NPM. Ele executa as bibliotecas que podem ser baixadas do site npmjs. Essas bibliotecas ficam em um banco de dados chamado _NPM Registry_, que também podem ser baixadas via **CLI** com **npm** ou **yarn**. A diferença entre este e o NPM é que o NPX busca a biblioteca na _NPM Registry_, instala em uma pasta temporária, executa o comando e depois remove a biblioteca da máquina, ela não fica na node_modules global.

#### YARN

Outro gerenciador de pacotes popular para a linguagem de programação JavaScript. A intenção por trás da criação do Yarn era abordar algumas das deficiências de desempenho e segurança de trabalhar com o **npm** (naquela época). Gera um arquivo `yarn.lock` para versionar e gerenciar as dependências dos pacotes. Mas ambos (npm e yarn) buscam da fonte `packages.json`.

Desde então, o npm passou por várias melhorias para corrigir algumas de suas ineficiências. Como resultado, npm e Yarn estão agora em uma disputa pescoço a pescoço para decidir qual gerenciador de pacotes supera o outro. A tabela abaixo mostra algumas das diferenças de usos dos dois gerenciadores.

| NPM             | YARN             |
| --------------- | ---------------- |
| npm init        | yarn init        |
| npm install ... | yarn add ...     |
| npm update ...  | yarn upgrade ... |
| npm remove ...  | yarn remove ...  |

Para mais informações sobre as demais diferenças, conferir o artigo [NPM vs Yarn: Which one to choose][3]

Visto isso, basta instalar e configurar o ESLint de acordo com o gerenciador de sua preferência. É importante ressaltar a necessidade de um repositório node ou npm para instalação do ESlint (por ser um Linter Javascript). Assim, antes de iniciar a instalação, certifique-se de ter um package.json no diretório do projeto. Se não existir, é necessário rodar `npm init` para gravar o seu diretório.

### Instalação - ESLint

Caso opte por uma instalação global, já com as dependências para o Prettier:

```console
npm i -g eslint
```

Ou para instalar localmente e em ambiente de desenvolvimento (RECOMENDADO), trocar a opção `-g` por `-D`:

```console
npm i -D eslint
```

Se não quiser instalar nada em sua máquina, apenas rodar:

```console
npx eslint --init
```

### Configuração - ESLint

O processo de configuração é dado de forma automática pelo ESLint. Abaixo estão definidas as respostas usuais:

**How would you like to use ESLint?**

- [ ] To check syntax only
- [ ] To check syntax and find problems
- [x] To check syntax, find problems, and enforce code style

**What type of modules does your project use?**

- [x] JavaScript modules (import/export)
- [ ] CommonJS (require/exports)
- [ ] None of these

**Which framework does your project use?**

- [x] React
- [ ] Vue.js
- [ ] None of these

**Does your project use TypeScript?**(Y/n) NO

**Where does your code run?**

- [x] Browser
- [ ] Node

**How would you like to define a style for your project?**

- [x] Use a popular style guide
- [ ] Answer questions about your style
- [ ] Inspect your JavaScript file(s)

**Which style guide do you want to follow?**

- [x] Airbnb: <https://github.com/airbnb/javascript>
- [ ] Standard: <https://github.com/standard/standard>
- [ ] Google: <https://github.com/google/eslint-confg-goole>

**What format do you want your config file to be in?**

- [ ] JavaScript
- [ ] YAML
- [x] JSON

**Would you like to install them now with npm?**(Y/n) YES

Após a finalização do formulário acima, um arquivo `.eslintrc.[EXTENSÃOescolhida]` será criado na pasta do projeto. Verificar a documentação para entendimento das partes deste arquivo. Todas as regras criadas e utilizadas, devem ser acrescentadas na parte `rules` do projeto.

Feito isso, adicionar um novo arquivo `.eslintignore` no diretório raiz do projeto para passar ao ESLint quais arquivos ignorar na formatação:

```ignore
**/*.js
node_modules
build
```

Tem uma opção que é adicionar o código abaixo dento do setting.json do .vscode/, para que seja feita automaticamente a configuração para o eslint formatar:

```json
{
  "editor.formatOnSave": false,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

## Prettier

A primeira configuração opcional do Prettier é alterar para aspas simples ao invés de dupla:

- Em **Settings**, procure pelas configurações do Prettier e marque as opções "**Prettier: Single Quote** - _If true, will use singles instead of double quotes_", "**Prettier: Semi** - _Wheather to add a semicolon at the end of every line_".

### Instalação - Prettier

Para a instalação local:

```console
npm i -D prettier eslint-config-prettier eslint-plugin-prettier
```

Note a importância de instalar todos os módulos utilizados na padronização, em ambiente de desenvolvimento (`-D`).

### Configuração - Prettier

Após instalado, ainda dentro do arquivo `.eslintrc.js`, em `extends`, adicionar "prettier" como segundo parâmetro do array. Depois, declarar uma propriedade chamada `plugins` passando o plugin do prettier que acabamos de instalar: `plugins: ['prettier']`, e dentro das rules, acrescentar: `"prettier/prettier": "error"`.

Um exemplo de um arquivo `.eslintrc.js` configurado com o Prettier:

```js
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: ['airbnb-base', 'prettier'],
  plugins: ['prettier'],
  parserOptions: {
    ecmaVersion: 12,
    sourceType: 'module',
  },
  rules: {
    'prettier/prettier': 'error',
    'class-methods-use-this': 'off', // desativa uma regra que diz que todo método de uma classe precisa usar a palavra this
    'no-param-reassign': 'off', // desativa uma regra que diz que não é permitido receber parâmetro e fazer alterações nele
    camelcase: 'off', // desativa uma regra que diz que toda variável precisa ser escrita no formato camelCase
    'no-unused-vars': ['error', { argsIgnorePattern: 'next' }], // não reclamar caso a variável utilizada seja next que é usada pelos middlewares do express
  },
};
```

Já para um arquivo `.eslintrc.json`, temos (com alguns plugins a mais para typescript):

```json
{
  "env": {
    "es2020": true,
    "node": true
  },
  "extends": [
    "airbnb-base",
    "plugin:@typescript-eslint/recommended",
    "prettier/@typescript-eslint",
    "plugin:prettier/recommended"
  ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": 2018,
    "sourceType": "module"
  },
  "plugins": ["@typescript-eslint", "prettier"],
  "rules": {
    "prettier/prettier": "error",
    "import/extensions": [
      "error",
      "ignorePackages",
      {
        "ts": "never"
      }
    ]
  },
  "settings": {
    "import/resolver": {
      "typescript": {}
    }
  }
}
```

Após isso, criar na pasta do projeto um arquivo `prettier.config.js` ou `.prettierrc.js` para realizar algumas configurações extras do Prettier:

```js
module.exports = {
  singleQuote: true, // Sempre usar aspas simples
  trailingComma: 'all', // Para adicionar ',' no objeto que foi quebrado em várias linhas
  arrowParens: 'avoid', // Adicionar parenteses quando um arrow function tiver apenas um parâmetro
};
```

Também deve ser criado um arquivo `.prettierignore` para que, assim como o ESLint, ele ignore arquivos padrões do projeto.

```ignore
node_modules
build
dist/
```

Configurações adicionais para este arquivo podem ser buscadas no site do [Prettier][4].

**Conclusão** - Abordou-se nesta seção que toda a configuração necessária para o padronizar um documento JavaScript. Ao final da padronização com EditorConfig, ESLint e Prettier deve-se ter no diretório do projeto os seguintes arquivos:

- .editorconfig
- .eslintignore
- .eslintrc.json (ou .js ou .yaml)
- .prettierrc.js

<!-- Markdown Links -->
<!-- SITES -->

[1]: https://nvuillam.github.io/mega-linter/supported-linters/
[2]: https://github.com/RehanSaeed/EditorConfig
[3]: https://www.section.io/engineering-education/npm-vs-yarn-which-one-to-choose/
[4]: https://prettier.io/docs/en/configuration.html
