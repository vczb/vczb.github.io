---
layout: post
title:  "Deploy automatizado de pull request com Heroku e Github"
date:   2021-07-12 19:00:00 +0530
categories: deploy
---

Já pensou o quanto pode agilizar o processo de desenvolvimento e code review se para cada pull request no repositório houvesse um ambiente deployado ? 

Com Heroku isso é possível e bem simples graças ao [Review Apps](https://devcenter.heroku.com/articles/github-integration-review-apps).

Antes de mais nada é necessário ter uma conta no Heroku, se você ainda não tem [crie a sua](https://id.heroku.com/login)

Você também precisará de uma aplicação funcional em um repositório do [Github](https://github.com/login), afinal, temos que deployar algo. Para ilustrar esta postagem usaremos o [Gamou](https://github.com/vczb/gamou), projeto open-source que venho desenvolvendo.

Acesse o [dashboard](https://dashboard.heroku.com/apps) do Heroku e vá em 'New' -> 'Create new app'

Irá aparecer uma tela semelhante a esta, preencha as informações do seu projeto

![Create new app](/assets/images/20210712/create_new_app.png)

Em seguida em Deployment Method selecione Github e procure o repositório

![Deployment Method](/assets/images/20210712/connect_to_github.png)

Clique em 'Connect'

Escolha a branch de sua preferência e clique em 'Enable Automatic Deploys'

![Enable Automatic Deploys](/assets/images/20210712/enable_auto_deploys.png)

E clique em 'Deploy Branch'

Aguarde o deploy... e pronto! Temos o nosso projeto no ar, clique em 'Open app' e abrirá um link semelhante a este [https://gamou.herokuapp.com/](https://gamou.herokuapp.com/)
 
Agora iremos configurar os deploys de pull request

Primeiro você precisará criar um arquivo chamado `app.json` na raiz do seu repositório

Configure conforme os dados do seu projeto
```
{
  "name": "Gamou",
  "description": "Gamou its a gamification project",
  "website": "https://gamou.herokuapp.com/",
  "success_url": "/",
  "addons": ["heroku-postgresql:hobby-dev"],
  "env": {
    "RAILS_ENV": "production",
    "COOKIE_SECRET": {
            "description": "This gets generated",
            "generator": "secret"
    },
    "SETUP_BY": {
            "description": "Who initiated this setup",
            "value": "",
            "required": false
    }
  },
  "scripts": {
    "postdeploy": "bundle exec rake db:migrate"
  }
}
```

_OBS: em scripts você pode configurar comandos personalizados para executar antes ou depois do deploy. No nosso caso estamos rodando uma migration_

Atualize o seu App e aguarde o deploy.

Feito isto vá em 'Pipeline' -> 'Settings' -> 'Enable Review Apps'

![Enable Review Apps](/assets/images/20210712/enable_review_app.png)

Marque a opção 'Automatically create review apps for new PRs'

![Automatically create review apps for new PRs](/assets/images/20210712/enable_auto_create_review.png)

Você também pode adicionar outras configurações conforme a sua preferência

Se tudo deu certo você já está com as Review Apps funcionais, a cada pull request o heroku criará um ambiente novo e no seu Github você verá algo como isto:

![View Deployment](/assets/images/20210712/view_deployment.png)


Se você quer fazer um comentário ou teve alguma dificuldade, abra uma issue no [repositório do blog](https://github.com/vczb/vczb.github.io/issues)

---

_Obrigado_