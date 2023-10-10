---
title: Instalação do Eden - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: Instalação do Eden - ElysiaJS

  - - meta
    - name: 'description'
      content: Comece instalando o Eden em seu frontend com "bun add elysia @elysiajs/eden", depois exponha o tipo de servidor Elysia e comece a usar o Eden Treaty ou o Eden Fetch.

  - - meta
    - name: 'og:description'
      content: Comece instalando o Eden em seu frontend com "bun add elysia @elysiajs/eden", depois exponha o tipo de servidor Elysia e comece a usar o Eden Treaty ou o Eden Fetch.
---

# Instalação do Eden
Comece instalando o Eden em seu frontend:
```bash
bun add elysia @elysiajs/eden
```

::: tip
Eden não pode inferir tipos corretamente sem o Elysia instalado.

Você pode instalar o Elysia como **dependências de desenvolvimento**, já que o Elysia no cliente é apenas para inferir tipos
:::

Primeiro, exporte o tipo de servidor Elysia existente:
```typescript
// server.ts
import { Elysia, t } from 'elysia'

const app = new Elysia()
    .get('/', () => 'Hi Elysia')
    .get('/id/:id', ({ params: { id } }) => id)
    .post('/mirror', ({ body }) => body, {
        body: t.Object({
            id: t.Number(),
            name: t.String()
        })
    })
    .listen(8080)

export type App = typeof app
```

Depois, consuma a API Elysia no lado do cliente:
```typescript
// client.ts
import { edenTreaty } from '@elysiajs/eden'
import type { App } from './server'

const client = edenTreaty<App>('http://localhost:8080')

// response type: 'Hi Elysia'
client.index.get().then(console.log)

// response type: 1895
client.id.1895.get().then(console.log)

// response type: { id: 1895, name: 'Skadi' }
client.mirror.post({
    id: 1895,
    name: 'Skadi'
}).then(console.log)
```
