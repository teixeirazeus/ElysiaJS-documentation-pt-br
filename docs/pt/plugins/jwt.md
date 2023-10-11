---
title: JWT Plugin - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: JWT Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Plugin para Elysia que adiciona suporte para usar JWT (JSON Web Token) no servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/jwt".

    - - meta
      - name: 'og:description'
        content: Plugin para Elysia que adiciona suporte para usar JWT (JSON Web Token) no servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/jwt".
---

# JWT Plugin
Este plugin adiciona suporte para usar JWT no manipulador Elysia.

Instale com:
```bash
bun add @elysiajs/jwt
```

Em seguida, use-o:
```typescript
import { Elysia, t } from 'elysia'
import { cookie } from '@elysiajs/cookie'
import { jwt } from '@elysiajs/jwt'

const app = new Elysia()
    .use(
        jwt({
            name: 'jwt',
            secret: 'Fischl von Luftschloss Narfidort'
        })
    )
    .use(cookie())
    .get('/sign/:name', async ({ jwt, cookie, setCookie, params }) => {
        setCookie('auth', await jwt.sign(params), {
            httpOnly: true,
            maxAge: 7 * 86400,
        })

        return `Sign in as ${cookie.auth}`
    })
    .get('/profile', async ({ jwt, set, cookie: { auth } }) => {
        const profile = await jwt.verify(auth)

        if (!profile) {
            set.status = 401
            return 'Unauthorized'
        }

        return `Hello ${profile.name}`
    })
    .listen(8080)
```

## Configuração
Este plugin estende a configuração de [jose](https://github.com/panva/jose).

A seguir, está uma configuração aceita pelo plugin.

### name
Nome para registrar a função `jwt`.

Por exemplo, a função `jwt` registrada com um nome personalizado.
```typescript
app
    .use(
        jwt({
            name: 'myJWTNamespace',
            secret: process.env.JWT_SECRETS
        })
    )
    .get('/sign/:name', ({ myJWTNamespace, params }) => {
        return myJWTNamespace.sign(params)
    })
```

Como alguns podem precisar usar múltiplos `jwt` com diferentes configurações em um único servidor, registrar explicitamente a função JWT com um nome diferente é necessário.

### secret
A chave privada para assinar o payload JWT.

### schema
Validação estrita de tipo para o payload JWT.

---
A seguir, está uma configuração que estende de [cookie](https://npmjs.com/package/cookie)

### alg
@default `HS256`

Algoritmo de assinatura para assinar o payload JWT.

As propriedades possíveis para jose são:
HS256
HS384
HS512
PS256
PS384
PS512
RS256
RS384
RS512
ES256
ES256K
ES384
ES512
EdDSA

### iss
A reivindicação do emissor identifica o principal que emitiu o JWT conforme [RFC7519](https://www.rfc-editor.org/rfc/rfc7519#section-4.1.1)

Em resumo; geralmente é (o domínio) nome do signatário.

### sub
A reivindicação do assunto identifica o principal que é o assunto do JWT.

As reivindicações em um JWT são normalmente declarações sobre o assunto conforme [RFC7519](https://www.rfc-editor.org/rfc/rfc7519#section-4.1.2)

### aud
A reivindicação de público identifica os destinatários para os quais o JWT se destina.

Cada principal destinado a processar o JWT DEVE identificar-se com um valor na reivindicação de público conforme [RFC7519](https://www.rfc-editor.org/rfc/rfc7519#section-4.1.3)

### jtit
A reivindicação de ID do JWT fornece um identificador único para o JWT conforme [RFC7519](https://www.rfc-editor.org/rfc/rfc7519#section-4.1.7)

### nbf
A reivindicação "não antes" identifica o tempo antes do qual o JWT não deve ser aceito para processamento conforme [RFC7519](https://www.rfc-editor.org/rfc/rfc7519#section-4.1.5)

### exp
A reivindicação de tempo de expiração identifica o tempo de expiração em ou após o qual o JWT NÃO DEVE ser aceito para processamento conforme [RFC7519](https://www.rfc-editor.org/rfc/rfc7519#section-4.1.4)

### iat
A reivindicação "emitida em"(issued at) identifica o momento em que o JWT foi emitido.

Esta reivindicação pode ser usada para determinar a idade do JWT conforme[RFC7519](https://www.rfc-editor.org/rfc/rfc7519#section-4.1.6)
 
### b64
Este parâmetro de cabeçalho de extensão JWS modifica a representação do payload JWS e o cálculo da entrada de assinatura JWS conforme [RFC7797](https://www.rfc-editor.org/rfc/rfc7797).

### kid
Uma dica indicando qual chave foi usada para proteger o JWS.

Este parâmetro permite que os originadores sinalizem explicitamente uma mudança de chave para os destinatários conforme [RFC7515](https://www.rfc-editor.org/rfc/rfc7515#section-4.1.4)

### x5t
O parâmetro de cabeçalho (impressão digital SHA-1 do certificado X.509) é um digest SHA-1 codificado em base64url da codificação DER do certificado X.509 [RFC5280](https://www.rfc-editor.org/rfc/rfc5280) correspondente à chave usada para assinar digitalmente o JWS conforme [RFC7515](https://www.rfc-editor.org/rfc/rfc7515#section-4.1.7)

### x5c
O parâmetro de cabeçalho (cadeia de certificados X.509) contém o certificado de chave pública X.509 ou a cadeia de certificados [RFC5280](https://www.rfc-editor.org/rfc/rfc5280) correspondente à chave usada para assinar digitalmente o JWS conforme [RFC7515](https://www.rfc-editor.org/rfc/rfc7515#section-4.1.6)

### x5u
O parâmetro de cabeçalho (URL X.509) é um URI [RFC3986](https://www.rfc-editor.org/rfc/rfc3986) que se refere a um recurso para o certificado de chave pública X.509 ou cadeia de certificados [RFC5280] correspondente à chave usada para assinar digitalmente o JWS conforme [RFC7515](https://www.rfc-editor.org/rfc/rfc7515#section-4.1.5)

### jwk
O parâmetro de cabeçalho "jku" (URL do conjunto JWK) é um URI [RFC3986] que se refere a um recurso para um conjunto de chaves públicas codificadas em JSON, uma das quais corresponde à chave usada para assinar digitalmente o JWS.

As chaves DEVEM ser codificadas como um conjunto JWK [JWK] conforme [ RFC7515](https://www.rfc-editor.org/rfc/rfc7515#section-4.1.2)

### typ
O parâmetro de cabeçalho `typ` (tipo) é usado por aplicativos JWS para declarar o tipo de mídia [IANA.MediaTypes] deste JWS completo.

Isso se destina a ser usado pelo aplicativo quando mais de um tipo de objeto pode estar presente em uma estrutura de dados de aplicativo que pode conter um JWS conforme [RFC7515](https://www.rfc-editor.org/rfc/rfc7515#section-4.1.9)

### ctr
O parâmetro Content-Type é usado por aplicativos JWS para declarar o tipo de mídia [IANA.MediaTypes] do conteúdo protegido (o payload).

Isso se destina a ser usado pelo aplicativo quando mais de um tipo de objeto pode estar presente no payload JWS conforme [RFC7515](https://www.rfc-editor.org/rfc/rfc7515#section-4.1.9)

## Manipulador
A seguir estão os valores adicionados ao manipulador.

### jwt.sign
Um objeto dinâmico de coleção relacionado ao uso com JWT registrado pelo plugin JWT.

Digite:
```typescript
sign: (payload: JWTPayloadSpec): Promise<string>
```

`JWTPayloadSpec` aceita o mesmo valor que [JWT config](#Configuração)

### jwt.verify
Verifique o payload com a configuração JWT fornecida.

Digite:
```typescript
verify(payload: string) => Promise<JWTPayloadSpec | false>
```

`JWTPayloadSpec` aceita o mesmo valor que [JWT config](#Configuração)

## Padrão
A seguir, você pode encontrar os padrões comuns para usar o plugin.

## Definir data de expiração do JWT
Por padrão, a configuração é passada para `setCookie` e herda seu valor.

```typescript
const app = new Elysia()
    .use(
        jwt({
            name: 'jwt',
            secret: 'kunikuzushi',
            exp: '7d'
        })
    )
    .get('/sign/:name', async ({ jwt, params }) => jwt.sign(params))
```

Isso assinará o JWT com uma data de expiração dos próximos 7 dias.
