# Configurando o **ESLint** e o **Prettier** em um projeto **Angular 18**

Olá, sou a Bianca Maxine, Dev Fullstack e UI/UX Designer. Hoje, vou compartilhar um passo a passo para configurar o **ESLint** e o **Prettier** em uma aplicação **Angular 18**. Esta configuração ajuda a padronizar o código, garantindo legibilidade, consistência e fácil manutenção.

---

## Por que o @angular-eslint?

A partir de fevereiro de 2021, com o lançamento do pacote `@angular-eslint/schematics`, o Angular adotou o **ESLint** como substituto oficial do TSLint, que foi descontinuado. Este pacote oferece suporte integrado ao Angular, incluindo templates e TypeScript, permitindo que você mantenha o código consistente e fácil de manter.

Você pode ver um pouco mais na documentação oficial do [`angular-eslint`](https://github.com/angular-eslint/angular-eslint).

---

## Passo 1: Adicionando o ESLint ao Projeto

Primeiro, precisamos instalar o pacote `@angular-eslint/schematics`, que configura o **ESLint** e substitui o **TSLint** (caso esteja presente):

```bash
ng add @angular-eslint/schematics
```

Após executar o comando, será criado o arquivo `/eslint.config.js`. Este é um exemplo de configuração básica gerada:

```javascript
// @ts-check
const eslint = require('@eslint/js');
const tseslint = require('typescript-eslint');
const angular = require('angular-eslint');

module.exports = tseslint.config(
  {
    files: ['**/*.ts'],
    extends: [
      eslint.configs.recommended,
      ...tseslint.configs.recommended,
      ...tseslint.configs.stylistic,
      ...angular.configs.tsRecommended,
    ],
    processor: angular.processInlineTemplates,
    rules: {
      '@angular-eslint/directive-selector': [
        'error',
        {
          type: 'attribute',
          prefix: 'app',
          style: 'camelCase',
        },
      ],
      '@angular-eslint/component-selector': [
        'error',
        {
          type: 'element',
          prefix: 'app',
          style: 'kebab-case',
        },
      ],
    },
  },
  {
    files: ['**/*.html'],
    extends: [
      ...angular.configs.templateRecommended,
      ...angular.configs.templateAccessibility,
    ],
    rules: {},
  },
);
```

O comando também adiciona o seguinte script ao `package.json`:

```json
"scripts": {
  ...
  "lint": "ng lint",
},
```

> A versão do `angular-eslint` usada para este artigo foi a `19.0.1`. A geração da configuração e do comando pode ser diferente em atualiazações futuras.

Não se esqueça de instalar a extensão do **ESLint** na sua IDE para garantir que o linting funcione corretamente.

Para regras personalizadas e mais informações, acesse a documentação oficial do [**ESLint**](https://eslint.org/docs/latest/).

---

## Passo 2: Adicionando o Prettier ao Projeto

Para integrar o **Prettier** ao seu projeto, instale as seguintes dependências:

```bash
yarn add -D prettier eslint-config-prettier eslint-plugin-prettier
# ou
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
```

Em seguida, crie um arquivo de configuração do **Prettier** chamado `/.prettierrc`:

```json
{
  "singleQuote": true,
  "semi": true,
  "printWidth": 80,
  "arrowParens": "avoid"
}
```

> O arquivo `/.prettierrc` é interpretado como um arquivo **JSON**. As configurações no exemplo acima não são obrigatórias, mas apenas sugestões que uso nos meus projetos para garantir consistência na formatação do código. Para entender melhor as configurações acesse a documentação oficial do [**Prettier**](https://prettier.io/docs/en/).

Crie também um arquivo `/.prettierignore` para definir os arquivos que o **Prettier** não precisará verificar.

```ignore
/node_modules
/dist
/build
/**/*.js
```

Agora, modifique o arquivo `/eslint.config.js` para incluir o **Prettier**. Primeiro, importe as configurações e o plugin:

```javascript
const prettierConfig = require('eslint-config-prettier');
const prettier = require('eslint-plugin-prettier');
```

O `prettierConfig` será responsável por configurar o ESLint para ignorar regras que conflitem com as configurações do Prettier. Para isso, inclua o `prettierConfig` às extensões _(é importante que seja adicionado sempre **no final** da lista)_:

```javascript
extends: [
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
  ...tseslint.configs.stylistic,
  ...angular.configs.tsRecommended,
  prettierConfig, // sempre no final
],
```

O `prettier` é um plugin que irá adicionar as configurações do Prettier ao ESLint. Adicione o `prettier` aos plugins:

```javascript
files: [...],
extends: [...],
plugins: { prettier },
rules: {...},
```

Por fim, configure a regra para garantir que o **Prettier** seja validado pelo **ESLint**:

```javascript
rules: {
  ...
  'prettier/prettier': 'error',
},
```

> A regra de validação do Prettier deve condizer com a variável do plugin. Como a variável neste exemplo se chama `prettier`, a regra de validação fica como 'prettier/prettier'. Se a variável se chamasse `prettierPlugin`, então a regra de validação precisaria ser modificada para 'prettierPlugin/prettier'.

Não se esqueça de instalar a extensão do **Prettier** na sua IDE para garantir que a formatação automática funcione corretamente.

---

> Desde a versão 12, o Angular já gera automaticamente um arquivo `/.editorconfig` para seus projetos. Ele ajuda a manter a consistência de estilos no código. Certifique-se de que a extensão **EditorConfig** esteja instalada na sua IDE para que as configurações de estilo sejam aplicadas automaticamente.

---

## Conclusão

Com o **ESLint** e o **Prettier** configurados, você terá um ambiente Angular bem estruturado e com padrões de código unificados. Essa configuração facilita o desenvolvimento, melhora a legibilidade e evita problemas durante revisões de código e integrações com equipes.

Espero que este guia tenha sido útil! Se tiver dúvidas ou precisar de mais ajuda, deixe um comentário ou entre em contato comigo.

Boas codificações! 🚀
