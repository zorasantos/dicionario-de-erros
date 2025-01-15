# Erro ao Buildar Aplicação Node.js com TypeScript e Fastify Usando `tsup`

```bash
✘ [ERROR] No loader is configured for ".html" files: ../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/util/nw-pre-gyp/index.html

    ../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/node-pre-gyp.js:86:21:
      86 │       return require('./' + command)(self, argvx, callback);
         ╵                      ~~~~~~~~~~~~~~

✘ [ERROR] Could not resolve "mock-aws-s3"

    ../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/util/s3_setup.js:43:28:
      43 │     const AWSMock = require('mock-aws-s3');
         ╵                             ~~~~~~~~~~~~~

  You can mark the path "mock-aws-s3" as external to exclude it from the bundle, which will remove
  this error and leave the unresolved path in the bundle. You can also surround this "require" call
  with a try/catch block to handle this failure at run-time instead of bundle-time.

✘ [ERROR] Could not resolve "aws-sdk"

    ../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/util/s3_setup.js:76:22:
      76 │   const AWS = require('aws-sdk');
         ╵                       ~~~~~~~~~

  You can mark the path "aws-sdk" as external to exclude it from the bundle, which will remove this
  error and leave the unresolved path in the bundle. You can also surround this "require" call with
  a try/catch block to handle this failure at run-time instead of bundle-time.

✘ [ERROR] Could not resolve "nock"

    ../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/util/s3_setup.js:112:23:
      112 │   const nock = require('nock');
          ╵                        ~~~~~~

  You can mark the path "nock" as external to exclude it from the bundle, which will remove this
  error and leave the unresolved path in the bundle. You can also surround this "require" call with
  a try/catch block to handle this failure at run-time instead of bundle-time.

CJS Build failed
Error: Build failed with 4 errors:
../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/node-pre-gyp.js:86:21: ERROR: No loader is configured for ".html" files: ../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/util/nw-pre-gyp/index.html
../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/util/s3_setup.js:43:28: ERROR: Could not resolve "mock-aws-s3"
../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/util/s3_setup.js:76:22: ERROR: Could not resolve "aws-sdk"
../../../.local/share/nvm/v20.18.1/lib/node_modules/bcrypt/node_modules/@mapbox/node-pre-gyp/lib/util/s3_setup.js:112:23: ERROR: Could not resolve "nock"
    at failureErrorWithLog (/Users/zorasantos/workspace/cl-tech/form-system-be/node_modules/esbuild/lib/main.js:1476:15)
    at /Users/zorasantos/workspace/cl-tech/form-system-be/node_modules/esbuild/lib/main.js:945:25
    at runOnEndCallbacks (/Users/zorasantos/workspace/cl-tech/form-system-be/node_modules/esbuild/lib/main.js:1316:45)
    at buildResponseToResult (/Users/zorasantos/workspace/cl-tech/form-system-be/node_modules/esbuild/lib/main.js:943:7)
    at /Users/zorasantos/workspace/cl-tech/form-system-be/node_modules/esbuild/lib/main.js:970:16
    at responseCallbacks.<computed> (/Users/zorasantos/workspace/cl-tech/form-system-be/node_modules/esbuild/lib/main.js:622:9)
    at handleIncomingPacket (/Users/zorasantos/workspace/cl-tech/form-system-be/node_modules/esbuild/lib/main.js:677:12)
    at Socket.readFromStdout (/Users/zorasantos/workspace/cl-tech/form-system-be/node_modules/esbuild/lib/main.js:600:7)
    at Socket.emit (node:events:514:28)
    at addChunk (node:internal/streams/readable:545:12)
```

## Causa do Erro

1. Arquivos `.html`: O `tsup` não possui um loader configurado para processar arquivos `.html` encontrados em algumas dependências, como `bcrypt`.

2. Dependências externas: Algumas bibliotecas (`mock-aws-s3, aws-sdk`, `nock`) são módulos externos que não precisam ser incluídos no bundle, mas o `tsup` tenta resolvê-los durante o build, causando falhas.

## Solução

### Configurar dependências como externas

Foi necessário informar ao `tsup` que determinadas dependências deveriam ser tratadas como externas, ou seja, não incluídas no processo de empacotamento.

### Configurando no `package.json`

Adicione a flag `--external` no script de build para listar as dependências a serem ignoradas:

```json
"scripts": {
  "build": "tsup src/index.ts --external bcrypt,mock-aws-s3,aws-sdk,nock"
}
```

