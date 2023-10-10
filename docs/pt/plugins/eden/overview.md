---
title: Eden - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: Eden - ElysiaJS

  - - meta
    - name: 'og:description'
      content: Eden é um cliente fetch para um servidor Elysia com **segurança de tipo de ponta a ponta** usando apenas a inferência de tipo do TypeScript em vez de geração de código. Permitindo que você sincronize os tipos de cliente e servidor sem esforço. Eden é composto por 3 módulos, "Eden Treaty", um cliente simplificado semelhante a um objeto para se comunicar com o servidor Elysia. "Eden Fn", chama a função do servidor no frontend com auto-completar e suporte total de tipo, e "Eden Fetch", um cliente semelhante ao Fetch para inferência de tipo instantânea.

  - - meta
    - name: 'og:description'
      content: Eden é um cliente fetch para um servidor Elysia com **segurança de tipo de ponta a ponta** usando apenas a inferência de tipo do TypeScript em vez de geração de código. Permitindo que você sincronize os tipos de cliente e servidor sem esforço. Eden é composto por 3 módulos, "Eden Treaty", um cliente simplificado semelhante a um objeto para se comunicar com o servidor Elysia. "Eden Fn", chama a função do servidor no frontend com auto-completar e suporte total de tipo, e "Eden Fetch", um cliente semelhante ao Fetch para inferência de tipo instantânea.
---

# Eden
Eden é um cliente fetch para um servidor Elysia com **segurança de tipo de ponta a ponta** usando apenas a inferência de tipo do TypeScript em vez de geração de código.

Permitindo que você sincronize os tipos de cliente e servidor sem esforço, pesando menos de 2KB.

Eden é composto por 3 módulos:
- Eden Treaty: Cliente simplificado semelhante a um objeto para se comunicar com o servidor Elysia.
- Eden Fn: Chame a função do servidor no frontend com auto-completar e suporte total de tipo.
- Eden Fetch: Cliente semelhante ao Fetch para inferência de tipo instantânea.

## Visão Geral
Abaixo está uma visão geral, caso de uso e comparação para cada módulo.

## Eden Treaty (Recomendado)
Eden Treaty é uma representação semelhante a um objeto de um servidor Elysia, proporcionando segurança de tipo de ponta a ponta e uma experiência de desenvolvedor significativamente melhorada.

Com Eden Treaty, você pode conectar sem esforço o servidor Elysia com suporte total de tipo e auto-completar, confiante de que o código está livre de erro de tipo.

Exemplo de uso do Eden Treaty:
```typescript
import { edenTreaty } from '@elysiajs/eden'
import type { App } from './server'

const app = edenTreaty<App>('http://localhost:8080')

// Call [GET] at '/'
const { data, error } = app.get()

// Call [POST] at '/nendoroid/id/:id'
const { data: nendoroid, error } = await app.nendoroid.id['1895'].post({
    id: 1895,
    name: 'Skadi'
})
```

## Eden Fn
Eden Fn permite que você exponha funções de backend para serem executadas no frontend com segurança de tipo de ponta a ponta, auto-completar, comentário JsDoc original e "click-to-definition", permitindo que você acelere seu ciclo de desenvolvimento.

```typescript
import { edenFn } from '@elysiajs/eden'
import type { App } from './server'

const fn = edenFn<App>('http://localhost:8080')

const data = await fn.prisma.user.create({
    data,
    select: {
        name: true
    }
})
```

Quanto à preocupação com a segurança, você pode definir escopos de permissão ou verificar o cabeçalho de autorização para limitar o acesso às funções programaticamente.

## Eden Fetch
Uma alternativa ao Eden Treaty semelhante ao Fetch com inferência de tipo mais rápida.

```typescript
import { edenFetch } from '@elysiajs/eden'
import type { App } from './server'

const fetch = edenFetch<App>('http://localhost:8080')

const data = await fetch('/name/:name', {
    method: 'POST',
    params: {
        name: 'Saori'
    },
    body: {
        branch: 'Arius',
        type: 'Striker'
    }
})
```

Usar Eden Treaty com um tipo complexo e muitas rotas (mais de 500 rotas por servidor) em um dispositivo de desenvolvimento de baixo desempenho pode levar a uma inferência de tipo lenta e auto-completar.

Eden Fetch é uma alternativa e solução para a inferência de tipo mais rápida possível, enquanto fornece suporte total de tipo como Eden Treaty.

::: tip NOTA
Ao contrário do Eden Treaty, o Eden Fetch não fornece implementação de Web Socket para o servidor Elysia.
:::
