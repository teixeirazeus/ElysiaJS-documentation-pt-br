---
title: GraphQL Yoga Plugin - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: GraphQL Yoga Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Plugin para Elysia que adiciona suporte para usar o GraphQL Yoga no servidor Elysia. Comece instalando o plugin com @elysiajs/graphql-yoga".

    - - meta
      - name: 'og:description'
        content: Plugin para Elysia que adiciona suporte para usar o GraphQL Yoga no servidor Elysia. Comece instalando o plugin com @elysiajs/graphql-yoga".
---

# GraphQL Yoga Plugin
Este plugin integra o GraphQL Yoga com o Elysia

Instale com:
```bash
bun add @elysiajs/graphql-yoga
```

Em seguida, use-o:
```typescript
import { Elysia } from 'elysia'
import { yoga } from '@elysiajs/graphql-yoga'

const app = new Elysia()
    .use(
        yoga({
            typeDefs: /* GraphQL */`
                type Query {
                    hi: String
                }
            `,
            resolvers: {
                Query: {
                    hi: () => 'Hello from Elysia'
                }
            }
        })
    )
    .listen(8080)
```

Ao acessar `/swagger`, você verá um endpoint Swagger com o endpoint gerado a partir do servidor Elysia.

Opcional: você também pode instalar versões personalizadas de dependências opcionais com:
```bash
bun add graphql graphql-yoga
```

## Resolver
O Elysia usa o [Mobius](https://github.com/saltyaom/mobius) para inferir tipos automaticamente do campo **typeDefs** field automatically, allowing permitindo que você obtenha total segurança de tipo e auto-completar ao digitar os tipos **resolver**.

## Context
Você pode adicionar um contexto personalizado à função resolver adicionando **context**
```ts
import { Elysia } from 'elysia'
import { yoga } from '@elysiajs/graphql-yoga'

const app = new Elysia()
    .use(
        yoga({
            typeDefs: /* GraphQL */`
                type Query {
                    hi: String
                }
            `,
            context: {
                name: 'Mobius'
            },
            // If context is a function on this doesn't present
            // for some reason it won't infer context type
            useContext(_) {}
            resolvers: {
                Query: {
                    hi: async (parent, args, context) => context.name
                }
            }
        })
    )
    .listen(8080)
```

## Configuração
Este plugin estende [as opções de criação do GraphQL Yoga, consulte a documentação do GraphQL Yoga](https://the-guild.dev/graphql/yoga-server/docs) com a configuração `schema` embutida na raiz.

A seguir, está uma configuração aceita pelo plugin

### path
@default `/graphql`

Endpoint para expor o manipulador GraphQL
