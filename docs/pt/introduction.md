---
title: Introdução - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: Introdução - ElysiaJS

  - - meta
    - name: 'description'
      content: ElysiaJS é um framework web bun rápido e amigável. Construído com base em 3 filosofias, desempenho, simplicidade e flexibilidade. Projetado com TypeScript em mente. Elysia entende o que você quer e infere automaticamente o tipo a partir do seu código.

  - - meta
    - property: 'og:description'
      content: ElysiaJS é um framework web bun rápido e amigável. Construído com base em 3 filosofias, desempenho, simplicidade e flexibilidade. Projetado com TypeScript em mente. Elysia entende o que você quer e infere automaticamente o tipo a partir do seu código.
---

# Introdução
ElysiaJS é um framework web rápido e amigável para [Bun](https://bun.sh).

Construído com base em 3 filosofias:
- Desempenho
    - Você não deve se preocupar com o desempenho
- Simplicidade
    - Blocos de construção simples para criar uma abstração, sem repetições
- Flexibilidade
    - Você deve ser capaz de personalizar a maior parte da biblioteca conforme sua necessidade

Projetado com TypeScript em mente, você não precisa entender TypeScript para aproveitar o máximo de Elysia. A biblioteca entende o que você quer e infere automaticamente o tipo a partir do seu código.

Observe:
```typescript
new Elysia()
    .get('/id/:id', (({ params: { id }}) => id))
    .listen(8080)
```

Elysia entende que você quer um parâmetro de caminho chamado `id`.
A biblioteca então registra `id` como um tipo em `params`.

--- 
Você pode definir um tipo personalizado para muitas coisas, por exemplo, o corpo de uma requisição recebida.
```typescript
import { Elysia, t } from 'elysia'

new Elysia()
    .post('/sign-in', ({ body }) => signIn(body), {
        body: t.Object({
            username: t.String(),
            password: t.String()
        })
    })
    .listen(8080)
```

Você dizer explicitamente a Elysia que o corpo da requisição recebida deve ter uma estrutura conforme você define.

Elysia então infere o tipo a partir do código que você escreve. Valida o corpo da requisição recebida para garantir a segurança do tipo.

Então, com [plugins](/plugins/overview), Elysia pode gerar instantaneamente documentação de API com Swagger com uma única linha de código.

```typescript
import { Elysia, t } from 'elysia'
/* [!code ++] */import { swagger } from '@elysiajs/swagger'

new Elysia()
/* [!code ++] */    .use(swagger())
    .post('/sign-in', ({ body }) => signIn(body), {
        body: t.Object({
            username: t.String(),
            password: t.String()
        })
    })
    .listen(8080)
```

E finalmente, você pode criar um cliente totalmente seguro em termos de tipo para consumir a API Elysia com Eden (opcional).

```typescript
// server.ts
import { Elysia, t } from 'elysia'
import { swagger } from '@elysiajs/swagger'

/* [!code ++] */const app = new Elysia()
    .use(swagger())
    .post('/sign-in', ({ body }) => signIn(body), {
        body: t.Object({
            username: t.String(),
            password: t.String()
        })
    })
    .listen(8080)

/* [!code ++] */export type App = typeof app
```

E no cliente:
```typescript
// client.ts
import { edenTreaty } from '@elysiajs/eden'
import type { App } from './server'

const app = edenTreaty<App>('http://localhost:8080')

app.signIn.post({
    username: 'saltyaom',
    password: 12345678
}).then(console.log)
```

Criando uma única fonte de verdade para sua estrutura de dados, eliminando qualquer possível conflito de tipo entre TypeScript, validação por meio de requisições, documentação de API e cliente frontend.

Garantindo que nada dê errado no desenvolvimento, migração e produção.
