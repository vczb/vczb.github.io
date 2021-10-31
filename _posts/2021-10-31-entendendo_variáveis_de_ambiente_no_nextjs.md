---
layout: post
title:  'Entendendo variáveis de ambiente no NextJS'
date:   2021-10-31 19:00:00 +0530
categories: 'NextJS'
---

Por mais simples que pareçam, as [variáveis de ambiente](https://nextjs.org/docs/basic-features/environment-variables) no [NextJS](https://nextjs.org/docs/) tem as suas peculiaridades.

Vamos começar pelo nome de prefixo `NEXT_PUBLIC_`, serve para diferenciar as variáveis que estarão disponíveis no "client-side" e no "server-site".

Por ex, vamos supor que tenhamos o arquivo `.env` e nele eu tenha as seguintes variáveis:

```
NEXT_PUBLIC_API_URL='https://meusite.com/api'
DB_PASS=password
```

No lado client, se eu usar o script `process.env.DB_PASS` o resultado será `undefined` pois a variável DB_PASS não está acessível publicamente.

Já o  script `process.env.NEXT_PUBLIC_API_URL` retornará o valor correto `https://meusite.com/api`.

Porém no lado "server-side" ambas as variáveis `NEXT_PUBLIC_API_URL` e `DB_PASS` estarão disponíveis.

Esta configuração de prefixo é muito poderosa, com ela podemos separar dados sigilosos de simples configurações.

Outra coisa muito legal das variáveis de ambiente no NextJS é a possibilidade de ter diferentes configurações para ambientes de desenvolvimento e produção.

Explicando: caso eu tenha um arquivo `.env.development` as variáveis setadas nele só serão lidas em modo desenvolvimento, ou seja, quando executo um `next dev`. Enquanto isso o arquivo `.env.production` será lido na versão de produção buildada ou quando eu execute um `next start` (lembre-se deve executar esse comando após o build).

Tá, e se eu quiser compartilhar variáveis em diferentes ambientes ?

Bem simples, basta usar um arquivo `.env.local` ou `.env`, a diferença entre as duas é apenas a nível de hierarquia. Cuide para não sobre-escrever atributos.

Entendendo a hierarquia:

O arquivo `.env.local` sempre será o primeiro a ser lido, logo em seguida será `.env.development` ou o `.env.production` dependendo do ambiente que você estiver, e por último, a `.env`, ou seja, ela que "manda mais".

Caso você tenha deployado sua aplicação Next na [Vercel](https://vercel.com/) você poderá adicionar com facilidade os atributos das variáveis, indo na guia "Project Settings / Environment Variables".

Você também pode "commitar" essas variáveis, certifique-se de não haver nenhuma informação sigilosa.

Por último mas não menos importante, é comum adicionar variáveis de [exemplo](https://github.com/vercel/next.js/tree/canary/examples/environment-variables), modelo. Geralmente se usa os nomes `.env.template`, `.env.example`. A aplicação NextJS não irá ler estes arquivos.

---

E ai! Gostou ? Espero que este artigo tenha sido útil pra você!

Para elogios, sugestões e reclamações: [abra uma issue](https://github.com/vczb/vczb.github.io)

### Valeu :)
