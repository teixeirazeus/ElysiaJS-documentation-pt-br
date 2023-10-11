---
title: Bearer Plugin - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: Bearer Plugin - ElysiaJS

  - - meta
    - name: 'description'
      content: Plugin para Elysia para recuperar o token Bearer conforme especificado no RFC6750. Comece instalando o plugin com "bun add @elysiajs/bearer".

  - - meta
    - name: 'og:description'
      content: Plugin para Elysia para recuperar o token Bearer conforme especificado no RFC6750. Comece instalando o plugin com "bun add @elysiajs/bearer".
---

# Bearer Plugin
Plugin para [elysia](https://github.com/elysiajs/elysia) para recuperar o token Bearer.

Instale com:
```bash
bun add @elysiajs/bearer
```

Depois use-o:
```typescript
import { Elysia } from 'elysia'
import { bearer } from '@elysiajs/bearer'

const app = new Elysia()
    .use(bearer())
    .get('/sign', ({ bearer }) => bearer, {
        beforeHandle({ bearer, set }) {
            if (!bearer) {
                set.status = 400
                set.headers[
                    'WWW-Authenticate'
                ] = `Bearer realm='sign', error="invalid_request"`

                return 'Unauthorized'
            }
        }
    })
    .listen(8080)
```

Este plugin é para recuperar um token Bearer especificado no [RFC6750](https://www.rfc-editor.org/rfc/rfc6750#section-2).

Este plugin NÃO lida com validação de autenticação para o seu servidor, em vez disso, o plugin deixa a decisão para os desenvolvedores aplicarem lógica para verificar a validação por si mesmos.