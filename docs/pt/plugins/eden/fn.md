---
title: Eden Fn - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: Eden Fn - ElysiaJS

  - - meta
    - name: 'description'
      content: Eden Fn ou Elysia Fn permitem que você exponha funções de backend para serem executadas no frontend com segurança de tipo de ponta a ponta, auto-completar, comentário JsDoc original e "clique para definição", permitindo que você acelere seu ciclo de desenvolvimento.

  - - meta
    - name: 'og:description'
      content: Eden Fn ou Elysia Fn permitem que você exponha funções de backend para serem executadas no frontend com segurança de tipo de ponta a ponta, auto-completar, comentário JsDoc original e "clique para definição", permitindo que você acelere seu ciclo de desenvolvimento.
---

# Eden Fn
Eden Fn permite que você exponha funções de backend para serem executadas no frontend com segurança de tipo de ponta a ponta, auto-completar, comentários JsDoc originais e "clique para definição", permitindo que você acelere sua velocidade de desenvolvimento.

Para usar o Eden Fn, crie funções expostas usando `fn`:
```typescript
// server.ts
import { Elysia } from 'elysia'

const app = new Elysia()
    .fn({
        sum: (a: number, b: number) => a + b,
    })
    .listen(8080)
```

Então, no cliente, importe `edenFn`:
```typescript
// client.ts
import { edenFn } from '@elysiajs/eden'
import type { App } from './server'

const fn = edenFn<App>('http://localhost:8080')

const data = await fn.sum(6, 9)
```

Elysia Fn usa o Proxy do JavaScript para capturar propriedades de objetos e parâmetros, para criar solicitações em lote para o servidor processar e retorna o valor pela rede.

Elysia Fn estende [superjson](https://github.com/blitz-js/superjson), permitindo tipo nativo em JavaScript como `Error`, `Map`, `Set`, e `undefined` para serem analisados através de dados JSON.

Elysia Fn suporta vários casos de uso, por exemplo **acessando Prisma no aplicativo do lado do cliente**, teoricamente, é possível usar Redis, Sequelize, RabbitMQ e mais, desde que seja uma função.

Como Elysia está rodando no Bun, Elysia Fn pode executar mais de 1,2 milhão de operações/segundo, simultaneamente (testado no M1 Max).

### Convenção de Nomenclatura
Quanto à convenção de nomenclatura, nos referiremos a Elysia Fn e Eden Fn como:

- Elysia Fn: Funções expostas no servidor Elysia
- Eden Fn: Um cliente para usar Elysia Fn

## Segurança
Você pode limitar ou negar escopos da função, verificar o cabeçalho de autorização e outros campos de cabeçalhos, validar parâmetros ou limitar o acesso a chaves programaticamente usando a função `permission`.

`permission` aceita um objeto composto por:
- value (obrigatório): uma função ou classes a serem expostas
- allow: array de métodos a serem permitidos
- deny: array de métodos a serem negados
- check: defina programaticamente uma permissão para a função com acesso a métodos e parâmetros da função e acesso à solicitação HTTP.

## Limitando o escopo
A abordagem mais simples para preocupações de segurança é definir o escopo **allow/deny** para Elysia Fn se o valor exposto for um objeto ou classe.

Isso pode ser alcançado usando:
- allow: array de métodos a serem permitidos
- deny: array de métodos a serem negados

```typescript
// server.ts
import { Elysia } from 'elysia'
import { Redis } from 'ioredis'

const app = new Elysia()
    .fn(({ permission }) => {
        sum: (a: number, b: number) => a + b,
        redis: permission({
            value: new Redis(),
            allow: ['set']
        })
    })
    .listen(8080)
```

Por padrão, se `allows` estiver definido, quaisquer outros métodos que não estiverem definidos em uma função de permissão serão considerados como `denied` e vice-versa.

::: tip Dica
Elysia Fn tem auto-completar e segurança de tipo para string literal de uma verificação de método.
:::

## Função "check"
Você pode definir programaticamente permissões para a função acessando métodos e parâmetros da função e a solicitação HTTP.

```typescript
import { Elysia } from 'elysia'
import { Redis } from 'ioredis'

const app = new Elysia()
    .fn(({ permission }) => {
        sum: (a: number, b: number) => a + b,
        redis: permission({
            value: new Redis(),
            allow: ['set'],
            check: ({ key, params, request, match }) => {
                if(!request.headers.get('Authorized'))
                    // This value will be sent to client
                    throw new Error('Invalid')
            }
        })
    })
    .listen(8080)
```

Por padrão, se a função **check** lançar um erro, o acesso à função é negado e a solicitação será rejeitada.

::: tip Dica
Se um método estiver definido em um escopo de permissão, o método também será re-verificado na função **check**.
:::

### Parâmetros disponíveis
**check** é composto por:
- key: Um nome de método de um objeto/classe, unido com `.` para método aninhado. (Se o valor for uma função, este valor é `never`)
- params: Uma matriz de parâmetros de função
- request: Uma solicitação HTTP, composta por várias informações sobre a solicitação, por exemplo, Cabeçalho
- match: um método `switch-case` com suporte de estreitamento de tipo de nível

::: tip Dica
A verificação de chaves suporta segurança de tipo e auto-completar de todas as funções possíveis, então você não está perdendo alguma função ou digitando acidentalmente o nome errado.
:::

### Função "match"
Por padrão, params é um tipo de união de todos os parâmetros possíveis de todos os métodos combinados em um único valor.

Como o TypeScript estreita é complicado, Elysia Fn forneceu a você um método `match` para estreitar o tipo do valor para classe/objeto.

Você não precisa usar `match` se o valor for uma função.

```typescript
import { Elysia } from 'elysia'
import { Redis } from 'ioredis'

const app = new Elysia()
    .fn(({ permission }) => {
        sum: (a: number, b: number) => a + b,
        redis: permission({
            value: new Redis(),
            check: ({ key, params, request, match }) => {
                if(!headers.get('Authorized'))
                    throw new Error('Invalid')

                return match({
                    set([value]) {
                        if(value === 'Mutsuki')
                            throw new Error('Correction need')
                    },
                    delete() {
                        if(!isAdmin(request.headers.get('Authorized')))
                            throw new Error('Invalid')
                    }
                    default() {}
                })
            }
        })
    })
    .listen(8080)
```

Cada método aceita o tipo estreitado de sua matriz de parâmetros.

## Agrupando
Por padrão, Eden Fn irá **agrupar solicitações em um intervalo de 33ms** em uma única solicitação, então você não precisa se preocupar se acidentalmente sobrecarregar seu servidor se você chamar várias funções frequentemente.

Se uma solicitação nas solicitações agrupadas falhar, ela não afetará outras solicitações dentro do lote, Eden Fn lidará com o erro nos bastidores.

## Config
Como Elysia Fn pode lidar com validação de solicitação HTTP usando Cabeçalhos, Eden Fn também pode definir o valor padrão dos cabeçalhos e comportamento de busca com um segundo argumento do `edenFn`.

```typescript
export const fn = edenFn<Server>('http://localhost:8080', {
    // Endpoint of Elysia Fn, default to `/~fn`
    fn: '/~fn',
    // Set default fetch behavior, default to {}
    fetch: {
        headers: {
            Authorized: something
        }
    }
})
```

### $set
Você pode alterar o comportamento da busca após uma inicialização da função usando `$set` que aceita o mesmo valor que o construtor.
```typescript
fn.$set({
    fn: '/~fn',
    fetch: {
        headers: {
            Authorized: something
        }
    }
})
```

**$set** irá mutar a configuração, **não mesclá-la**, então tenha cuidado com efeitos colaterais ou leia abaixo.

### $clone
Uma implementação sem efeitos colaterais de **$set**, retornará uma nova instância de Elysia Fn.

```typescript
fn.$clone({
    fn: '/~fn',
    fetch: {
        headers: {
            Authorized: something
        }
    }
})
```

## Limitação
Elysia Fn não suporta encadeamento de métodos e uma função de retorno de chamada como parâmetro.

Em termos de desempenho, Elysia Fn é construído em cima de REST com um pequeno overhead ~2,5% mais lento em comparação
