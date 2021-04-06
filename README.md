# alura-webpack
Alura - Webpack - Formação Vue
* https://cursos.alura.com.br/course/webpack
* https://cursos.alura.com.br/formacao-vuejs

----
# Para saber mais: webpack e sua estreita relação com SPA

Apesar de o Webpack ser altamente customizável, sua aplicação é voltada para Single Page Applications. Essas páginas que não recarregam durante seu uso tendem a carregar todos os scripts e arquivos CSS que dependem logo no primeiro carregamento e toda mudança de troca de página é feita por um sistema de rotas que manipula a única página carregada trocando seu conteúdo. Para o usuário, essa aplicação se comporta como se fosse uma aplicação nativa, pelo não carregamento durante o uso.

Por mais fabulosa que essa abordagem possa ser para o usuário, se o escopo da aplicação for grande, o primeiro carregamento será sofrível, primeiro pelo tempo de download de arquivos JS e CSS gigantes (concatenados e minificados) além do tempo para que o interpretador JavaScript realize o parser desses arquivos. Esse problema já não acontece com aplicações web tradicionais, pois cada página carrega os scripts e arquivos CSS de que precisam. Webpack soluciona esse problema das Single Page Applications.

Webpack permite dividir um bundle da aplicação em outros bundles menores que são carregados apenas quando forem utilizados pela aplicação, diminuindo assim o tempo de carregamento dessas aplicações e por conseguinte melhorando a experiência do usuário. Aprenderemos a realizar essas divisões, mas ainda há muita coisa para aprendemos antes de chegarmos a este ponto.

----

# Executando o projeto

## Client

* Acessar a pasta `../client`
* Executar `npm install` para instalar as dependências do node
* Executar `npm run watch` para monitorar atualizações nos arquivos (babel)

----

# Server

* Acessar a pasta `../server`
* Executar o comando `npm start` para inicializar o servidor
* Acessar o navegador pelo host `http://localhost:3000`


----

# Configurando o projeto para o Webpack (Aula 01, Atividade 05)

* Apagaremos a pasta `.../client/app`
* Substuiremos no index.html o código abaixo da div `<div id="negociacoes"></div>`
```html
<script src="node_modules/systemjs/dist/system.js"></script>
<script>
    System 
        .import('app/app.js')
        .catch(err => console.error(err)) 
</script>
```

```html
<script src="node_modules/reflect-metadata/Reflect.js"></script>
<script src="dist/bundle.js"></script>
```

* Remover `babel-cli` e `systemjs` do `client/package.json`
```ssh
npm uninstall babel-cli --save-dev
```
```ssh
npm uninstall systemjs --save
```

* Instalar Webpack e babel-core
```ssh
npm install webpack@3.1.0 babel-core@6.25.0 --save-dev
```


# O temível webpack.config.js (Aula1, Atividade 7)

* Criar o arquivo `client/webpack.config.js` que é um módulo do nodejs que indica o ponto de entrada da aplicação
```
 Lembre-se que é a plataforma Node.js que executa o Webpack para nós. Aliás, o sistema de módulos da plataforma utilizado pela plataforma é o CommonJS. Diferente do ESM módulos, o CommonJS utiliza a sintaxe require e module.exports
```
* No arquivo `client/package.json` remover os scripts `build` e `watch` e incluir `"build-dev": "webpack --config webpack.config.js"`
* Executar `npm run build-dev` que irá resutlar em erro pois não registramos o babel para o webpack ainda 

```ssh
    ERROR in ./app-src/controllers/NegociacaoController.js
    Module parse failed: /home/jeiel.lopes/development/github/jeielml/alura-webpack/client/app-src/controllers/NegociacaoController.js Unexpected character '@' (5:0)
    You may need an appropriate loader to handle this file type.
    | import { getNegociacaoDao, Bind, getExceptionMessage, debounce, controller, bindEvent } from '../util/index.js';
    | 
    | @controller('#data', '#quantidade', '#valor')
    | export class NegociacaoController {
    | 
    @ ./app-src/app.js 1:0-77
    npm ERR! code ELIFECYCLE
    npm ERR! errno 2
    npm ERR! client@1.0.0 build-dev: `webpack --config webpack.config.js`
    npm ERR! Exit status 2
    npm ERR! 
    npm ERR! Failed at the client@1.0.0 build-dev script.
    npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
    npm ERR! A complete log of this run can be found in:
    npm ERR!     /home/jeiel.lopes/.npm/_logs/2021-04-06T01_19_16_861Z-debug.log
```
----

# Babel-loader, a ponte entre o Webpack e o Babel (Aula 1, Atividade 11)

* Instalar o babel-loader
```ssh
npm install babel-loader@7.1.0 --save-dev
```

* Configurar o babel-loader no webpack em `client/webpack.config.js`
```
...
module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader'
                }
            }
        ]
    }
```


* Executar `npm run build-dev` que irá resutlar em warning por incompatibilidade do system.js
```
WARNING in ./app-src/app.js
System.register is not supported by webpack.
```

* Remover `System.js` do `client/.babelrc` (`"transform-es2015-modules-systemjs",`)

* Desinstalar o módulo do babel que transforma os módulos de ES para JS
```ssh
npm uninstall babel-plugin-transform-es2015-modules-systemjs --save-dev
```

* Executar `npm run build-dev` com sucesso
client/.babelrc

# Resumo do conteúdo capítulo

Aprendemos neste capítulo:

* O papel do webpack
* Instalação através do npm
* Configuração do webpack.config.js
* Como executar webpack através de um npm script
* O conceito de entry e output
* O papel de um loader
* Instalação e configuração de um loader

----

# Preparando o Build de produção (Aula 02 -  Atividdade 1)

* No arquivo `client/package.json` incluir o script `build-prod`, o parâmetro `-p` realiza o "Uglify" do JS
```ssh
    "build-prod": "webpack -p --config webpack.config.js"
```

* Ao executar `npm run build-prod` ocorre o seguinte erro pois o Uglify suporta até ES 2015 e estamos utilizando ES 8 (2017):
```ssh
ERROR in bundle.js from UglifyJs
Unexpected token: name (Negociacao) [bundle.js:75,4]
npm ERR! code ELIFECYCLE
npm ERR! errno 2
npm ERR! client@1.0.0 build-prod: `webpack -p --config webpack.config.js`
npm ERR! Exit status 2
npm ERR! 
npm ERR! Failed at the client@1.0.0 build-prod script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/jeiel.lopes/.npm/_logs/2021-04-06T10_28_21_985Z-debug.log
```

* Substituir o parâmetro `-p` que utiliza Uglify pelo babily

  * Instalar o Babili
     ```ssh
     npm install babili-webpack-plugin@0.1.1 --save-dev
     ```
  * Configurar o NODE_ENV no `client/package.json` >`build-prod`
     ```ssh
     "build-prod": "NODE_ENV=production webpack --config webpack.config.js"
     ```
  * Configurar o Babili no `client/webpack.config.js`
     ```ssh
     const babiliPlugin = require('babili-webpack-plugin');

     let plugins = [];
     ```

     ```ssh
     ...
      module: {
        ...
        plugins: plugins
      }
     ```
  * Incluir plugins no `client/webpack.config.js` 
    ```ssh
    if(process.env.NODE_ENV == 'production') {
        plugins.push(new babiliPlugin());
    }

    ```

* Testando se está tudo funcionando    
  * Nao pode quebrar
    ```ssh
    npm run build-dev
    ```
   * O arquivo `client/dist/bundle.js` deverá ficar minificado
    ```ssh
    npm run build-prod
    ```

# Mudando o ambiente com cross-env (Aula 2, Atividade 3)

Esta configuração busca viabilizar que com o mesmo código fonte seja possível executar o projeto em diferentes sistemas operacionais.

* Instalar o `cross-env`
```ssh
npm install cross-env@5.0.1 --save-dev
```

* Alterar `client/package.json` >`build-prod`, adicionando o comando `cross-env`
```ssh
"build-prod": "cross-env NODE_ENV=production webpack  --config webpack.config.js"
```

* Testando se está tudo funcionando    
  * Nao pode quebrar
    ```ssh
    npm run build-dev
    ```
   * O arquivo `client/dist/bundle.js` deverá ficar minificado
    ```ssh
    npm run build-prod
    ```

# O que aprendemos neste capítulo?

Neste capítulo vimos:

* O efeito do parâmetro -p para o build de produção.
* A incompatibilidade do UglifyJS com código que não sejam escritos em ECMASCRIPT 5.
* babili como plugin que ajuda no processo de minificação.
* pegadinhas na atribuição de variáveis de ambiente.
* o módulo cross-env para garantir compatibilidade do nosso npm script entre diferentes sistemas operacionais.    

----