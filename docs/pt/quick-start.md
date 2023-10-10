---
title: Primeiros Passos - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: Getting Start - ElysiaJS

  - - meta
    - name: 'description'
      content: Elysia é uma biblioteca criada para Bun e é o único pré-requisito. Para começar, inicialize um novo projeto com "bun create elysia hi-elysia" e inicie o servidor de desenvolvimento com "bun dev". Isso é tudo que você precisa para um início rápido ou para começar com ElysiaJS

  - - meta
    - property: 'og:description'
      content: Elysia é uma biblioteca feita para Bun, e é tudo que você precisa para começar. Inicialize um novo projeto com "bun create elysia hi-elysia" e inicie o servidor de desenvolvimento com "bun dev". É tudo que você precisa para trabalhar com Elysia.js!

---

# Início Rápido
Elysia é uma biblioteca criada para Bun.

Bun é tudo que você precisa para começar.
```bash
curl https://bun.sh/install | bash
```

Inicialize um novo projeto com `bun create`:
```bash
bun create elysia hi-elysia
```

Em seguida, você deve ver o nome da pasta `hi-elysia` em seu diretório.
```bash
cd hi-elysia
```

Abra `src/index.ts`, e você deve ver:
```typescript
import { Elysia } from "elysia";

const app = new Elysia().get("/", () => "Hello Elysia").listen(3000);

console.log(
  `🦊 Elysia is running at ${app.server?.hostname}:${app.server?.port}`
);
```

Inicie um servidor de desenvolvimento com:
```bash
bun dev
```

Abra seu navegador e acesse `http://localhost:3000`.

Você deve ver que seu servidor está funcionando.

---

Parabéns! Você acabou de criar um novo servidor web com Elysia 🎉🎉
