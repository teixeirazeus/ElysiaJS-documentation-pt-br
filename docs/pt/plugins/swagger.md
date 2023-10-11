---
title: Swagger Plugin - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: Swagger Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Plugin para Elysia que adiciona suporte para gerar documentação de API Swagger para o Servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/swagger".

    - - meta
      - name: 'og:description'
        content: Plugin para Elysia que adiciona suporte para gerar documentação de API Swagger para o Servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/swagger".
---

# Swagger Plugin
Este plugin gera um endpoint Swagger para um servidor Elysia.

Instale com:
```bash
bun add @elysiajs/swagger
```

Em seguida, use-o:
```typescript
import { Elysia } from 'elysia'
import { swagger } from '@elysiajs/swagger'

new Elysia()
    .use(swagger())
    .get('/', () => 'hi')
    .post('/hello', () => 'world')
    .listen(8080)
```

Ao acessar `/swagger`, você verá um endpoint Swagger com o endpoint gerado pelo servidor Elysia.

## Configuração
Abaixo está uma configuração aceita pelo plugin

### swagger
Configuração para personalizar o Swagger.

Consulte a [especificação Swagger](https://swagger.io/specification/v2/).

### excludeStaticFile
@default `true`

Determina se o Swagger deve excluir arquivos estáticos.

### path
@default `/swagger`

Endpoint para expor o Swagger

### exclude
Caminhos para excluir da documentação Swagger.

O valor pode ser um dos seguintes:
- **string**
- **RegExp**
- **Array<string | RegExp>**

## Padrão
Abaixo, você pode encontrar os padrões comuns para usar o plugin.

## Alterar Endpoint Swagger
Você pode alterar o endpoint swagger definindo [path](#path) na configuração do plugin.

```typescript
import { Elysia } from 'elysia'
import { swagger } from '@elysiajs/swagger'

new Elysia()
    .use(swagger({
        path: '/v2/swagger'
    }))
    .listen(8080)
```

## Personalizar informações do Swagger
```typescript
import { Elysia } from 'elysia'
import { swagger } from '@elysiajs/swagger'

new Elysia()
    .use(swagger({
        documentation: {
            info: {
                title: 'Elysia Documentation',
                version: '1.0.0'
            }
        }
    }))
    .listen(8080)
```

## Usando Tags
Elysia tem a capacidade de separar os endpoints em grupos usando o sistema de tags do Swagger.

Primeiro, defina as tags disponíveis no objeto de configuração swagger:

```typescript
app.use(
  swagger({
    documentation: {
      tags: [
        { name: 'App', description: 'General endpoints' },
        { name: 'Auth', description: 'Authentication endpoints' }
      ]
    }
  })
)
```

Em seguida, use a propriedade details da seção de configuração do endpoint para atribuir esse endpoint ao grupo:

```typescript
app.get('/', () => 'Hello Elysia', {
  detail: {
    tags: ['App']
  }
})

app.group('/auth', (app) =>
  app.post(
    '/sign-up',
    async ({ body }) =>
      db.user.create({
        data: body,
        select: {
          id: true,
          username: true
        }
      }),
    {
      detail: {
        tags: ['Auth']
      }
    }
  )
)
```

Isso produzirá uma página swagger como a seguinte
<img width="1446" alt="image" src="https://github.com/elysiajs/documentation/assets/184729/8caee6c0-4262-4a5c-b225-196cf74c338b">
