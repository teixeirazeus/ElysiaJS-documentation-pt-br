---
title: tRPC Plugin - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: tRPC Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Plugin para Elysia que adiciona suporte para usar tRPC no Bun com o Servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/trpc".

    - - meta
      - name: 'og:description'
        content: Plugin para Elysia que adiciona suporte para usar tRPC no Bun com o Servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/trpc".
---

# tRPC Plugin
Este plugin adiciona suporte para usar o [tRPC](https://trpc.io/)

Instale com:
```bash
bun add @elysiajs/trpc @trpc/server @elysiajs/websocket 
```

Em seguida, utilize-o:
```typescript
import { compile as c, trpc } from "@elysiajs/trpc";
import { initTRPC } from "@trpc/server";
import { Elysia, t as T } from "elysia";

const t = initTRPC.create();
const p = t.procedure;

const router = t.router({
  greet: p

    // 💡 Using Zod
    //.input(z.string())
    // 💡 Using Elysia's T
    .input(c(T.String()))
    .query(({ input }) => input),
});

export type Router = typeof router;

const app = new Elysia().use(trpc(router)).listen(8080);
```

## trpc
Aceita o roteador tRPC e registra no manipulador Elysia.

```
trpc(router: Router, option?: {
    endpoint?: string
}): this
```

`Router` é a instância do Roteador TRPC.

### endpoint
O caminho para expor o endpoint TRPC.
