---
title: Static Plugin - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: Static Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Plugin para Elysia que adiciona suporte para servir arquivos/pastas estáticos para o Servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/static".

    - - meta
      - name: 'og:description'
        content: Plugin para Elysia que adiciona suporte para servir arquivos/pastas estáticos para o Servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/static".
---

# Static Plugin
Este plugin pode servir arquivos/pastas estáticos para o Servidor Elysia

Instale com:
```bash
bun add @elysiajs/static
```

Em seguida, use-o:
```typescript
import { Elysia } from 'elysia'
import { staticPlugin } from '@elysiajs/static'

new Elysia()
    .use(staticPlugin())
    .listen(8080)
```

Por padrão, a pasta padrão do plugin estático é `public`, e é registrada com o prefixo `/public`.

Suponha que a estrutura do seu projeto seja:
```
| - src
  | - index.ts
| - public
  | - takodachi.png
  | - nested
    | - takodachi.png
```

Os caminhos disponíveis se tornarão:
- /public/takodachi.png
- /public/nested/takodachi.png

## Configuração
Abaixo está uma configuração aceita pelo plugin

### assets
@default `"public"`

Caminho para a pasta a ser exposta como estática

### prefix
@default `"/public"`

Prefixo de caminho para registrar arquivos públicos

### ignorePatterns
@default `[]`

Lista de arquivos a serem ignorados ao servir como arquivos estáticos

### staticLimits
@default `1024`

Por padrão, o plugin estático registrará caminhos no Roteador com um nome estático, se os limites forem excedidos, os caminhos serão adicionados ao Roteador de forma preguiçosa para reduzir o uso de memória.
Compromisso entre memória e desempenho.

### alwaysStatic
@default `false`

Se definido como verdadeiro, os arquivos estáticos terão caminho registrado no Roteador, ignorando os `staticLimits`.

### headers
@default `{}`

Defina os cabeçalhos de resposta dos arquivos

## Padrão
Abaixo, você pode encontrar os padrões comuns para usar o plugin.

## Arquivo único
Suponha que você queira retornar apenas um único arquivo, você pode usar `Bun.file`  em vez de usar o plugin estático
```typescript
new Elysia()
    .get('/file', () => Bun.file('public/takodachi.png'))
```
