# nextjs-study

https://nextjs.org/

## What is it?

To build a complete web application with React from scratch,<br>
there are many important details you need to consider.

Such as, webpack, babel, loaders, router, state manager, css, typescript, ...

Next.js is an **all in one solution** for it.<br>
It aims to have best-in-class **developer experience**.

![image](https://user-images.githubusercontent.com/11723334/155971320-9be2175d-d682-4579-b34a-42ccd1a87fdb.png)

## Features

- Routing
  - Page-baed routing
  - Client-side routing (with prefetching)
- Per-page based renderings
  - Pre-rendering
    - Static generation (SSG)
    - Server-side rendering (SSR)
- Automatic code splitting
- CSS
  - Built-in CSS
  - Sass
  - CSS-in-JS
- Dev environmentt
  - Fast refresh
- API routes to build API endpoints with Serverless Functions
- Fully extendable

## Where are tools (like webpack)?

- webpack5: node_modules/next/dist/compiled/webpack/bundle5.js
- babel: node_modules/next/dist/compiled/babel/bundle.js
- postcss-scss: node_modules/next/dist/compiled/postcss-scss/scss-syntax.js
- and all the other pre-compiled tools are under `node_modules/next/dist/compiled`

## cli

- node_modules/.bin/next

> References `node_modules/next/dist/cli` path

  ```js
  const commands = {
      build: ()=>Promise.resolve(require('../cli/next-build').nextBuild)
      ,
      start: ()=>Promise.resolve(require('../cli/next-start').nextStart)
      ,
      export: ()=>Promise.resolve(require('../cli/next-export').nextExport)
      ,
      dev: ()=>Promise.resolve(require('../cli/next-dev').nextDev)
      ,
      lint: ()=>Promise.resolve(require('../cli/next-lint').nextLint)
      ,
      telemetry: ()=>Promise.resolve(require('../cli/next-telemetry').nextTelemetry)
      ,
      info: ()=>Promise.resolve(require('../cli/next-info').nextInfo)
  };
  ```

- node_modules/next/dist/cli/next-build.js

  ```js
  #!/usr/bin/env node
  "use strict";
  Object.defineProperty(exports, "__esModule", {
      value: true
  });
  exports.nextBuild = void 0;
  var _fs = require("fs");
  var _indexJs = _interopRequireDefault(require("next/dist/compiled/arg/index.js"));
  var Log = _interopRequireWildcard(require("../build/output/log"));
  var _build = _interopRequireDefault(require("../build"));

  const nextBuild = (argv)=>{

      // omitted...

      return (0, _build).default(dir, null, args['--profile'], args['--debug'], !args['--no-lint']).catch((err)=>{
          console.error('');
          if ((0, _isError).default(err) && (err.code === 'INVALID_RESOLVE_ALIAS' || err.code === 'WEBPACK_ERRORS' || err.code === 'BUILD_OPTIMIZATION_FAILED' || err.code === 'EDGE_RUNTIME_UNSUPPORTED_API')) {
              (0, _utils).printAndExit(`> ${err.message}`);
          } else {
              console.error('> Build error occurred');
              (0, _utils).printAndExit(err);
          }
      });
  };
  exports.nextBuild = nextBuild;
  ```

- node_modules/next/dist/build/index.js

  ```js
  var _compiler = require("./compiler");

  async function build(dir, conf = null, reactProductionProfiling = false, debugOutput = false, runLint = true) {

              // We run client and server compilation separately to optimize for memory usage
              await runWebpackSpan.traceAsyncFn(async ()=>{
                  const clientResult = await (0, _compiler).runCompiler(clientConfig, {
                      runWebpackSpan
                  });
                  // Fail build if clientResult contains errors
                  if (clientResult.errors.length > 0) {
                      result = {
                          warnings: [
                              ...clientResult.warnings
                          ],
                          errors: [
                              ...clientResult.errors
                          ]
                      };
                  } else {
                      const serverResult = await (0, _compiler).runCompiler(configs[1], {
                          runWebpackSpan
                      });
  ```

- node_modules/next/dist/build/compiler.js

  ```js
  "use strict";
  Object.defineProperty(exports, "__esModule", {
      value: true
  });
  exports.runCompiler = runCompiler;
  var _webpack = require("next/dist/compiled/webpack/webpack");
  function runCompiler(config, { runWebpackSpan  }) {
      return new Promise((resolve, reject)=>{
          const compiler = (0, _webpack).webpack(config);
          compiler.run((err, stats)=>{
              const webpackCloseSpan = runWebpackSpan.traceChild('webpack-close', {
                  name: config.name
              });
              webpackCloseSpan.traceAsyncFn(()=>closeCompiler(compiler)
              ).then(()=>{
                  if (err) {
  ```
