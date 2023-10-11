---
title: Plugin Overview - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: Swagger Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Elysia é projetada para ser modular e leve. É por isso que Elysia está criando um plugin de padrão comum pré-construído para uso conveniente dos desenvolvedores, e graças aos plugins da comunidade para personalizar ainda mais o Elysia.

    - - meta
      - name: 'og:description'
        content: Elysia é projetada para ser modular e leve. É por isso que Elysia está criando um plugin de padrão comum pré-construído para uso conveniente dos desenvolvedores, e graças aos plugins da comunidade para personalizar ainda mais o Elysia.
---

# Visão Geral
Elysia é projetada para ser modular e leve.

Seguindo a mesma ideia do Arch Linux (aliás, eu uso Arch):

> As decisões de design são tomadas caso a caso através do consenso dos desenvolvedores

Para garantir que os desenvolvedores acabem com um servidor web de alto desempenho que pretendem criar.

É por isso que Elysia está criando um plugin de padrão comum pré-construído para uso conveniente dos desenvolvedores:

## Official plugins:
- [Bearer](/plugins/bearer) - recuperação automática do token Bearer
- [CORS](/plugins/cors) - configuração de solicitação de requisição de origem cruzada
- [Cron](/plugins/cron) - configuração de cronjob
- [Eden](/plugins/eden/overview) - cliente de ponta a ponta seguro para Elysia
- [GraphQL Apollo](/plugins/graphql-apollo) - execute GraphQL Apollo no Elysia
- [GraphQL Yoga](/plugins/graphql-yoga) - execute GraphQL Yoga no Elysia
- [HTML](/plugins/html) - plugin conveniente para lidar com resposta HTML
- [JWT](/plugins/jwt) - autenticação com JWT
- [Static](/plugins/static) - servir arquivo/pastas estáticas
- [Swagger](/plugins/swagger) - gerar documentação Swagger em 1 linha
- [tRPC](/plugins/trpc) - adicionar suporte tRPC
- [WebSocket](/patterns/websocket) - suporte a websocket

## Community plugins:
- [Lucia Auth](https://github.com/pilcrowOnPaper/lucia) - Autenticação, simples e limpa
- [Elysia Clerk](https://github.com/wobsoriano/elysia-clerk) - Plugin Clerk não oficial para ElysiaJS
- [Elysia Polyfills](https://github.com/bogeychan/elysia-polyfills) - execute o ecossistema Elysia no Node e Deno
- [Vite](https://github.com/timnghg/elysia-vite) - Plugin Elysia simples que ajuda você a usar o Vite. Ele serve seu arquivo html de entrada com scripts Vite injetados
- [Elysia Helmet](https://github.com/DevTobias/elysia-helmet) - apps Elysia seguros com vários cabeçalhos HTTP
- [Vite Plugin SSR](https://github.com/timnghg/elysia-vite-plugin-ssr) - Vite Plugin SSR usando servidor Elysia
- [OAuth2](https://github.com/bogeychan/elysia-oauth2) - lidar com o fluxo de código de autorização OAuth 2.0
- [Rate Limit](https://github.com/rayriffy/elysia-rate-limit) - limitador de taxa simples e leve
- [Logysia](https://github.com/tristanisham/logysia) - middleware de log clássico elysia
- [Logger](https://github.com/bogeychan/elysia-logger) - middleware de log pino elysia
- [Elysia Lambda](https://github.com/TotalTechGeek/elysia-lambda) - deploy Elysia na AWS Lambda
- [Decorators](https://github.com/gaurishhs/elysia-decorators) -use decoradores typescript com elysia
- [Autoroutes](https://github.com/wobsoriano/elysia-autoroutes) - rotas de sistema de arquivos para Elysia
- [Group Router](https://github.com/itsyoboieltr/elysia-group-router) - roteador baseado em sistema de arquivos e pastas para grupos.
- [Basic Auth](https://github.com/itsyoboieltr/elysia-basic-auth) - autenticação http básica para Elysia
- [ETag](https://github.com/bogeychan/elysia-etag) - geração automática de ETags HTTP para Elysia
- [Basic Auth](https://github.com/eelkevdbos/elysia-basic-auth) - Autenticação http básica para Elysia (usando o evento 'request').
- [i18n](https://github.com/eelkevdbos/elysia-i18next) - wrapper i18n para Elysia baseado em i18next
- [Elysia Request ID](https://github.com/gtramontina/elysia-requestid) - Adiciona/Encaminha IDs de solicitação (`X-Request-ID` or personalizado).
- [Elysia HTMX](https://github.com/gtramontina/elysia-htmx) - Auxiliares de contexto para [HTMX](https://htmx.org/).
- [Elysia HMR HTML](https://github.com/gtrabanco/elysia-hmr-html) - Recarregue arquivos html quando mudar qualquer arquivo em um diretório
- [Elysia Inject HTML](https://github.com/gtrabanco/elysia-inject-html) - Plugin tão simples para injetar código HTML em arquivos HTML
- [Elysia HTTP Error](https://github.com/yfrans/elysia-http-error) - maneira mais fácil de retornar erros HTTP dos manipuladores Elysia
- [Elysia Http Status Code](https://github.com/sylvain12/elysia-http-status-code) - Plugin de código de status http simples para Elysia.js
- [NoCache](https://github.com/gaurishhs/elysia-nocache) - Um plugin para Elysia para desativar o cache

---
Se você tiver um plugin escrito para Elysia, sinta-se à vontade para compartilhar seu plugin criando um PR para o [repositório de documentação](https://github.com/elysiajs/documentation).