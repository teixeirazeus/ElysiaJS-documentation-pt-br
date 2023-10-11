---
title: Apollo GraphQL Plugin - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: Apollo GraphQL Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Plugin para Elysia que adiciona suporte para usar GraphQL Apollo no servidor Elysia. Comece instalando o plugin com @elysiajs/apollo @apollo/server".

    - - meta
      - name: 'og:description'
        content: Plugin para Elysia que adiciona suporte para usar GraphQL Apollo no servidor Elysia. Comece instalando o plugin com @elysiajs/apollo @apollo/server".
---

# GraphQL Apollo Plugin
Plugin para [elysia](https://github.com/elysiajs/elysia) para usar o GraphQL Apollo.

Instale com:
```bash
bun add graphql @elysiajs/apollo @apollo/server
```

Depois, use-o:
```typescript
import { Elysia } from 'elysia'
import { apollo, gql } from '@elysiajs/apollo'

const app = new Elysia()
    .use(
        apollo({
            typeDefs: gql`
                type Book {
                    title: String
                    author: String
                }

                type Query {
                    books: [Book]
                }
            `,
            resolvers: {
                Query: {
                    books: () => {
                        return [
                            {
                                title: 'Elysia',
                                author: 'saltyAom'
                            }
                        ]
                    }
                }
            }
        })
    )
    .listen(8080)
```

Acessando `/graphql` você deverá ver o playground do Apollo GraphQL funcionando.

## Context
Como o Elysia é baseado no padrão Web Standard Request e Response, que é diferente do `HttpRequest` e `HttpResponse` do Node que o Express usa, resulta em `req, res` sendo indefinidos no contexto.

Por causa disso, o Elysia substitui ambos por `context`, como parâmetro de rota.
```typescript
const app = new Elysia()
    .use(
        apollo({
            typeDefs,
            resolvers,
            context: async ({ request }) => {
                const authorization = request.headers.get('Authorization')

                return {
                    authorization
                }
            }
        })
    )
    .listen(8080)
```


## Configuração
Este plugin estende o [ServerRegistration](https://www.apollographql.com/docs/apollo-server/api/apollo-server/#options) do Apollo (que é o parâmetro do construtor do ApolloServer).

A seguir estão os parâmetros estendidos para configurar o Apollo Server com o Elysia.
### path
@default "/graphql"

Caminho para expor o Apollo Server.

### enablePlayground
@default "process.env.ENV !== 'production'

Determina se o Apollo deve fornecer o Playground do Apollo.