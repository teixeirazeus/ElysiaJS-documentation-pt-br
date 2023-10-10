---
title: Eden Fetch - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: Eden Fetch - ElysiaJS

  - - meta
    - name: 'description'
      content: Uma alternativa ao Eden Treaty semelhante ao fetch, com inferência de tipo mais rápida. Com o Eden Fetch, você pode fazer requisições para um servidor Elysia com segurança de tipo de ponta a ponta sem a necessidade de geração de código.

  - - meta
    - name: 'og:description'
      content: Uma alternativa ao Eden Treaty semelhante ao fetch, com inferência de tipo mais rápida. Com o Eden Fetch, você pode fazer requisições para um servidor Elysia com segurança de tipo de ponta a ponta sem a necessidade de geração de código.
---

# Eden Fetch
Uma alternativa ao Eden Treaty semelhante ao fetch, com inferência de tipo mais rápida.

Com o Eden Fetch, você pode fazer requisições para um servidor Elysia sem a necessidade de geração de código.
----

T sem a necessidade de utilizar o Eden Fetch, primeiro exporte o tipo do seu servidor Elysia existente:
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

Em seguida, importe o tipo do servidor e consuma a API Elysia no cliente:
```typescript
// client.ts
import { edenFetch } from '@elysiajs/eden'
import type { App } from './server'

const fetch = edenFetch<App>('http://localhost:8080')

// response type: 'Hi Elysia'
const pong = await fetch('/', {})

// response type: 1895
const id = await fetch('/id/:id', {
    params: {
        id: '1895'
    }
})

// response type: { id: 1895, name: 'Skadi' }
const nendoroid = await fetch('/mirror', {
    method: 'POST',
    body: {
        id: 1895,
        name: 'Skadi'
    }
})
```

## Tratamento de Erros
Você pode tratar erros da mesma forma que no Eden Treaty:
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

## Quando devo usar Eden Fetch em vez de Eden Treaty

Usar o Eden Treaty requer muita iteração de nível inferior para mapear todos os tipos possíveis de uma só vez, enquanto, em contraste, o Eden Fetch pode ser executado de forma preguiçosa até você escolher uma rota.

Com tipos complexos e muitas rotas de servidor, usar o Eden Treaty em um dispositivo de desenvolvimento de baixo desempenho pode levar a uma inferência de tipo lenta e auto-completar.

Mas como o Elysia ajustou e otimizou muitos tipos e inferências, o Eden Treaty pode ter um desempenho muito bom em uma quantidade considerável de rotas.

Se o seu único processo contém **mais de 500 rotas**, e você precisa consumir todas as rotas **em um único código base frontend**, então você pode querer usar o Eden Fetch, pois ele tem um desempenho TypeScript significativamente melhor do que o Eden Treaty.