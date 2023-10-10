---
title: Eden Treaty - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: Eden Treaty - ElysiaJS

  - - meta
    - name: 'og:description'
      content: Eden Treaty é uma representação semelhante a um objeto de um servidor Elysia, fornecendo segurança de tipo de ponta a ponta e uma experiência de desenvolvedor significativamente melhorada. Com Eden, você pode buscar uma API do servidor Elysia totalmente segura em termos de tipo, sem a necessidade de geração de código.

  - - meta
    - name: 'og:description'
      content: Eden Treaty é uma representação semelhante a um objeto de um servidor Elysia, fornecendo segurança de tipo de ponta a ponta e uma experiência de desenvolvedor significativamente melhorada. Com Eden, você pode buscar uma API do servidor Elysia totalmente segura em termos de tipo, sem a necessidade de geração de código.
---

# Eden Treaty
Eden Treaty é uma representação semelhante a um objeto de um servidor Elysia, fornecendo segurança de tipo de ponta a ponta e uma experiência de desenvolvedor significativamente melhorada.

Com Eden, você pode fazer requisições a um servidor Elysia, com total segurança de tipo, sem a necessidade de geração de código.

---

Para usar Eden Treaty, primeiro exporte o tipo do seu servidor Elysia existente:
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

Então, importe o tipo do servidor e consuma a API Elysia no cliente:
```typescript
// client.ts
import { edenTreaty } from '@elysiajs/eden'
import type { App } from './server'

const app = edenTreaty<App>('http://localhost:8080')

// response type: 'Hi Elysia'
const { data: pong, error } = app.get()

// response type: 1895
const { data: id, error } = app.id['1895'].get()

// response type: { id: 1895, name: 'Skadi' }
const { data: nendoroid, error } = app.mirror.post({
    id: 1895,
    name: 'Skadi'
})
```

::: tip Dica
Eden Treaty é totalmente seguro em termos de tipo com suporte a auto-completar. 
:::

## Anatomia
Eden Treaty transformará todos os caminhos existentes em uma representação semelhante a um objeto, que pode ser descrita como:
```typescript
EdenTreaty.<1>.<2>.<n>.<method>({
    ...body,
    $query?: {},
    $fetch?: RequestInit
})
```

### Caminho
Eden transformará `/` em `.` que pode ser chamado com um `method`, por exemplo:
- **/path** -> .path
- **/nested/path** -> .nested.path

### Parâmetros do caminho
Os parâmetros do caminho serão mapeados automaticamente pelo nome na URL.

- **/id/:id** -> .id.`<anyThing>`
- eg: .id.hi
- eg: .id['123']

::: tip Dica
Se um caminho não suportar parâmetros de caminho, o TypeScript mostrará um erro.
:::

### Consulta
Você pode anexar consultas ao caminho com `$query`:
```typescript
app.get({
    $query: {
        name: 'Eden',
        code: 'Gold'
    }
})
```

### Buscar
Eden Treaty é um invólucro de busca, você pode adicionar quaisquer parâmetros válidos de [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) ao Eden passando-o para `$fetch`:
```typescript
app.post({
    $fetch: {
        headers: {
            'x-origanization': 'MANTIS'
        }
    }
})
```

## Tratamento de Erros
Eden Treaty retornará um valor de `data` e `error` como resultado, ambos totalmente tipados.

```typescript
// response type: { id: 1895, name: 'Skadi' }
const { data: nendoroid, error } = app.mirror.post({
    id: 1895,
    name: 'Skadi'
})

if(error) {
    switch(error.status) {
        case 400:
        case 401:
            warnUser(error.value)
            break

        case 500:
        case 502:
            emergencyCallDev(error.value)
            break

        default:
            reportError(error.value)
            break
    }

    throw error
}

const { id, name } = nendoroid
```

Ambos **data** e **error** serão tipados como nulos até que você possa confirmar seus status com uma guarda de tipo.

Para simplificar, se a busca for bem-sucedida, os dados terão um valor e o erro será nulo, e vice-versa.

::: tip Dica
O erro é envolto com um `Error` e seu valor retornado do servidor pode ser recuperado de `Error.value`.
:::

### Tipo de erro baseado no status
Tanto Eden Treaty quanto Eden Fetch podem restringir um tipo de erro com base no código de status se você fornecer explicitamente um tipo de erro no servidor Elysia.

```typescript
// server.ts
import { Elysia, t } from 'elysia'

const app = new Elysia()
    .model({
        nendoroid: t.Object({
            id: t.Number(),
            name: t.String()
        }),
        error: t.Object({
            message: t.String()
        })
    })
    .get('/', () => 'Hi Elysia')
    .get('/id/:id', ({ params: { id } }) => id)
    .post('/mirror', ({ body }) => body, {
        body: 'nendoroid',
        response: {
            200: 'nendoroid', // [!code ++]
            400: 'error', // [!code ++]
            401: 'error' // [!code ++]
        }
    })
    .listen(8080)

export type App = typeof app
```

No lado do cliente:
```typescript
const { data: nendoroid, error } = app.mirror.post({
    id: 1895,
    name: 'Skadi'
})

if(error) {
    switch(error.status) {
        case 400:
        case 401:
            // narrow down to type 'error' described in the server
            warnUser(error.value)
            break

        default:
            // typed as unknown
            reportError(error.value)
            break
    }

    throw error
}
```

## WebSocket
Eden suporta WebSocket, assim como uma rota normal.
```typescript
// Server
import { Elysia, t, ws } from 'elysia'

const app = new Elysia()
    .use(ws())
    .ws('/chat', {
        message(ws, message) {
            ws.send(message)
        },
        body: t.String(),
        response: t.String()
    })
    .listen(8080)

type App = typeof app
```

Para ouvir os WebSockets, chame `.subscribe`:
```typescript
// Client
import { edenTreaty } from '@elysiajs/eden'
const app = edenTreaty<App>('http://localhost:8080')

const chat = app.chat.subscribe()

chat.subscribe((message) => {
    console.log('got', message)
})

chat.send('hello from client')
```

Você pode usar `schema` para impor segurança de tipo em WebSockets, assim como uma rota normal.

`Eden.subscribe` retorna `EdenWebSocket` que estende a classe `WebSocket` com segurança de tipo. A sintaxe é principalmente idêntica se você estiver familiarizado com a API WebSocket.

Se você precisar de mais controle sobre `EdenWebSocket`, você pode acessar `EdenWebSocket.raw` para acessar a API `WebSocket` nativa.
