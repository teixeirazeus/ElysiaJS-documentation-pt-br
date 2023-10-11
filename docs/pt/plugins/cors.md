---
title: CORS Plugin - ElysiaJS
head:
  - - meta
    - property: 'og:title'
      content: CORS Plugin - ElysiaJS

  - - meta
    - name: 'description'
      content: Plugin para Elysia que adiciona suporte para personalizar o comportamento de Compartilhamento de Recursos de Origem Cruzada (Cross-Origin Resource Sharing). Comece instalando o plugin com "bun add @elysiajs/cors".

  - - meta
    - name: 'og:description'
      content: Plugin para Elysia que adiciona suporte para personalizar o comportamento de Compartilhamento de Recursos de Origem Cruzada (Cross-Origin Resource Sharing). Comece instalando o plugin com "bun add @elysiajs/cors".
---

# CORS Plugin

Este plugin adiciona suporte para personalizar o comportamento de [Compartilhamento de Recursos de Origem Cruzada](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) (CORS).

Instale com:
```bash
bun add @elysiajs/cors
```

Depois, use-o:
```typescript
import { Elysia } from 'elysia'
import { cors } from '@elysiajs/cors'

new Elysia()
    .use(cors())
    .listen(8080)
```

Isso configurará o Elysia para aceitar solicitações de qualquer origem. 

## Configuração
Abaixo está uma configuração aceita pelo plugin:

### origin
@default `true`

Indica se a resposta pode ser compartilhada com o código solicitante das origens fornecidas.

O valor pode ser um dos seguintes:
- **string** - Nome da origem que será atribuído diretamente ao cabeçalho [Access-Control-Allow-Origin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin).
- **boolean** - Se definido como verdadeiro, [Access-Control-Allow-Origin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) será definido como `*` (todas as origens)
- **RegExp** - Padrão para combinar com a URL da solicitação, permitido se corresponder.
- **Function** - Lógica personalizada para permitir o compartilhamento de recursos, permitir se `true` for retornado.
    - Esperado ter o tipo de:
    ```typescript
    cors(context: Context) => boolean | void
    ```
- **Array<string | RegExp | Function>** - itera por todos os casos acima em ordem, permitido se qualquer um dos valores for `true`.

---
### methods
@default `*`

Métodos permitidos para solicitações de origens cruzadas.

Atribui o cabeçalho [Access-Control-Allow-Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Methods).

O valor pode ser um dos seguintes:
- **undefined | null | ''** - Ignora todos os métodos.
- **\*** - Permite todos os métodos.
- **string** - Espera um único método ou uma string delimitada por vírgulas 
    - (ex: `'GET, PUT, POST'`)
- **string[]** - Permite vários métodos HTTP.
    - ex: `['GET', 'PUT', 'POST']`

---
### allowedHeaders
@default `*`

Cabeçalhos permitidos para uma solicitação recebida.

Atribui o cabeçalho [Access-Control-Allow-Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Headers).

O valor pode ser um dos seguintes::
- **string** - Espera um único cabeçalho ou uma string delimitada por vírgulas
    - ex: `'Content-Type, Authorization'`.
- **string[]** - Permite vários cabeçalhos HTTP.
    - ex: `['Content-Type', 'Authorization']`

---
### exposedHeaders
@default `*`

Responda CORS com os cabeçalhos especificados.

Atribui o cabeçalho [Access-Control-Expose-Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Expose-Headers).

O valor pode ser um dos seguintes:
- **string** - Espera um único cabeçalho ou uma string delimitada por vírgulas.
    - ex: `'Content-Type, X-Powered-By'`.
- **string[]** - Permite vários cabeçalhos HTTP.
    - ex: `['Content-Type', 'X-Powered-By']`

---
### credentials
@default `true`

O cabeçalho de resposta Access-Control-Allow-Credentials informa aos navegadores se deve expor a resposta ao código JavaScript de frontend quando o modo de credenciais da solicitação [Request.credentials](https://developer.mozilla.org/en-US/docs/Web/API/Request/credentials) é `include`.

Quando o modo de credenciais da solicitação [Request.credentials](https://developer.mozilla.org/en-US/docs/Web/API/Request/credentials) é `include`, navegadores só exporão a resposta ao código JavaScript de frontend se o valor de Access-Control-Allow-Credentials for verdadeiro.

Credenciais são cookies, cabeçalhos de autorização ou certificados de cliente TLS.

Atribui o cabeçalho [Access-Control-Allow-Credentials](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Credentials).

---
### maxAge
@default `5`

Indica quanto tempo os resultados de uma [preflight](https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request) (ou seja, as informações contidas nos cabeçalhos [Access-Control-Allow-Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Methods) e [Access-Control-Allow-Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)) podem ser armazenados em cache.

Atribui o cabeçalho [Access-Control-Max-Age](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Max-Age).

---
### preflight
A solicitação de pré-voo (preflight) é uma solicitação enviada para verificar se o protocolo CORS é compreendido e se um servidor está ciente de usar métodos e cabeçalhos específicos.

Responda com a solicitação **OPTIONS** com 3 cabeçalhos de solicitação HTTP:
- **Access-Control-Request-Method**
- **Access-Control-Request-Headers**
- **Origin**

Esta configuração indica se o servidor deve responder às solicitações de pré-voo.

---
## Padrão
Abaixo, você pode encontrar os padrões comuns para usar o plugin.

## Permitir CORS por domínio de nível superior

```typescript
import { Elysia } from 'elysia'
import { cors } from '@elysiajs/cors'

const app = new Elysia()
    .use(cors({
        origin: /\*.saltyaom.com$/
    }))
    .get('/', () => 'Hi')
    .listen(8080)
```

Isso permitirá solicitações de domínios de nível superior com `saltyaom.com'
