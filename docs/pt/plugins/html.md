---
title: HTML Plugin - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: HTML Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Plugin para Elysia que adiciona suporte de atalho para retornar HTML no servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/html".

    - - meta
      - name: 'og:description'
        content: Plugin para Elysia que adiciona suporte de atalho para retornar HTML no servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/html".
---

# HTML Plugin

Permite que você use [JSX](#jsx) e HTML com cabeçalhos adequados e suporte.

Instale com:

```bash
bun add @elysiajs/html
```

Depois use:

```tsx
import { Elysia } from 'elysia'
import { html } from '@elysiajs/html'

new Elysia()
    .use(html())
    .get(
        '/html',
        () => `
            <html lang='en'>
                <head>
                    <title>Hello World</title>
                </head>
                <body>
                    <h1>Hello World</h1>
                </body>
            </html>`
    )
    .get('/jsx', () => (
        <html lang='en'>
            <head>
                <title>Hello World</title>
            </head>
            <body>
                <h1>Hello World</h1>
            </body>
        </html>
    ))
    .listen(3000)
```

Este plugin adicionará automaticamente o cabeçalho `Content-Type: text/html; charset=utf8` à resposta, adicionará `<!doctype html>` e o converterá em um objeto de resposta.

## JSX
O HTML Elysia é baseado em [@kitajs/html](https://github.com/kitajs/html), permitindo-nos definir JSX para string em tempo de compilação para obter alta performance.

Nomeie seu arquivo que precisa usar JSX para terminar com o sufixo **"x"**:
- .js -> .jsx
- .ts -> .tsx

Para registrar o tipo TypeScript, adicione o seguinte a **tsconfig.json**:
```jsonc
// tsconfig.json
{
    "compilerOptions": {
        "jsx": "react",
        "jsxFactory": "Html.createElement",
        "jsxFragmentFactory": "Html.Fragment"
    }
}
```

Pronto, agora você pode usar JSX como seu mecanismo de template:
```tsx
import { Elysia } from 'elysia'
import { html } from '@elysiajs/html' // [!code ++]

new Elysia()
    .use(html()) // [!code ++]
    .get('/', () => (
        <html lang="en">
            <head>
                <title>Hello World</title>
            </head>
            <body>
                <h1>Hello World</h1>
            </body>
        </html>
    ))
    .listen(3000)
```

## XSS
O HTML Elysia é usado com o plugin Kita HTML para detectar possíveis ataques XSS em tempo de compilação.

Você pode usar um atributo `safe` dedicado para sanitizar o valor do usuário e prevenir vulnerabilidades XSS.
```tsx
import { Elysia, t } from 'elysia'
import { html } from '@elysiajs/html'

new Elysia()
    .use(html())
    .post('/', ({ body }) => (
        <html lang="en">
            <head>
                <title>Hello World</title>
            </head>
            <body>
                <h1 safe>{body}</h1>
            </body>
        </html>
    ), {
        body: t.String()
    })
    .listen(3000)
```

No entanto, ao construir um aplicativo em grande escala, é melhor ter um lembrete de tipo para detectar possíveis vulnerabilidades XSS em sua base de código.

Para adicionar um lembrete com segurança de tipo, instale:
```sh
bun add @kitajs/ts-html-plugin
```

Em seguida, adicione o seguinte a **tsconfig.json
```jsonc
// tsconfig.json
{
    "compilerOptions": {
        "jsx": "react",
        "jsxFactory": "Html.createElement",
        "jsxFragmentFactory": "Html.Fragment",
        "plugins": [{ "name": "@kitajs/ts-html-plugin" }]
    }
}
```

## Opções

### contentType

-   Tipo: `string`
-   Padrão: `'text/html; charset=utf8'`

O tipo de conteúdo da resposta..

### autoDetect

-   Tipo: `boolean`
-   Padrão: `true`

Se deve detectar automaticamente o conteúdo HTML e definir o tipo de conteúdo.

### autoDoctype

-   Tipo: `boolean | 'full'`
-   Padrão: `true`

Se deve adicionar automaticamente `<!doctype html>` a uma resposta que começa com `<html>`, se não for encontrado.

Use `full` para também adicionar automaticamente doctypes em respostas retornadas sem este plugin

```ts
// without the plugin
app.get('/', () => '<html></html>')

// With the plugin
app.get('/', ({ html }) => html('<html></html>'))
```

### isHtml

-   Tipo: `(value: string) => boolean`
-   Padrão: `isHtml` (função exportada)

A função usada para detectar se uma string é um html ou não. A implementação padrão verifica se o comprimento é maior que 7, começa com `<` e termina com `>`.

Tenha em mente que não há uma maneira real de validar o HTML, então a implementação padrão é um palpite.
