---
title: Cron Plugin - ElysiaJS
head:
    - - meta
      - property: 'og:title'
        content: Cron Plugin - ElysiaJS

    - - meta
      - name: 'description'
        content: Plugin para Elysia que adiciona suporte para executar cronjobs no servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/cron".

    - - meta
      - name: 'og:description'
        content: Plugin para Elysia que adiciona suporte para executar cronjobs no servidor Elysia. Comece instalando o plugin com "bun add @elysiajs/cron".
---

# Cron Plugin

Este plugin adiciona suporte para executar cronjobs no servidor Elysia.

Instale com:

```bash
bun add @elysiajs/cron
```

Depois, use-o:

```typescript
import { Elysia } from 'elysia'
import { cron } from '@elysiajs/cron'

new Elysia()
    .use(
        cron({
            name: 'heartbeat',
            pattern: '*/10 * * * * *',
            run() {
                console.log('Heartbeat')
            }
        })
    )
    .listen(8080)
```

O código acima registrará `heartbeat` a cada 10 segundos

## cron

Crie um cronjob para o servidor Elysia.

```
cron(config: CronConfig, callback: (Instance['store']) => void): this
```

`CronConfig` aceita os parâmetros especificados abaixo:

### name

Nome do trabalho para registrar em  `store`.

Isso registrará a instância cron em `store` com um nome especificado, que pode ser usado como referência em processos posteriores, por exemplo, parar o trabalho.

### pattern

Hora de executar o trabalho conforme especificado pela [sintaxe cron](https://en.wikipedia.org/wiki/Cron) especificada como abaixo:

```
┌────────────── segundo (opcional)
│ ┌──────────── minuto
│ │ ┌────────── hora
│ │ │ ┌──────── dia do mês
│ │ │ │ ┌────── mês
│ │ │ │ │ ┌──── dia da semana
│ │ │ │ │ │
* * * * * *
```

Isso pode ser gerado por ferramentas como [Crontab Guru](https://crontab.guru/)

---

Este plugin estende o método cron para Elysia usando [cronner](https://github.com/hexagon/croner).

Abaixo estão as configurações aceitas pelo cronner.

### timezone

Fuso horário no formato Europe/Stockholm.

### startAt

Hora de início agendada para o trabalho.

### stopAt

Hora de parada agendada para o trabalho.

### maxRuns

Número máximo de execuções.

### catch

Continue a execução mesmo se um erro não tratado for lançado por uma função acionada.

### interval

O intervalo mínimo entre execuções, em segundos.

## Pattern

Abaixo, você pode encontrar os padrões comuns para usar o plugin.

## Stop cronjob

Você pode parar o cronjob manualmente acessando o nome do cronjob registrado em `store`.

```typescript
import { Elysia } from 'elysia'
import { cron } from '@elysiajs/cron'

const app = new Elysia()
    use(
        cron({
            name: 'heartbeat',
            pattern: '*/1 * * * * *',
            run() {
                console.log("Heartbeat")
            }
        }
    )
    .get('/stop', ({ store: { cron: { heartbeat } } }) => {
        heartbeat.stop()

        return 'Stop heartbeat'
    })
    .listen(8080)
```
