---
layout: post
title:  'VSCode snippets personalizados por workspace'
date:   2021-07-20 19:00:00 +0530
categories: VSCode
---

Sabe aqueles projetos que são cheios de custom hooks, funções ou até estruturas para documentação que você precisa estar escrevendo toda hora ?

Vou apresentar uma solução legal para agilizar estes processos, os [snippets](https://code.visualstudio.com/docs/editor/userdefinedsnippets).

![Snippet](/assets/images/20210720/ajax-snippet.gif)


Agora imagine se você pudesse compartilhar eles com o resto da equipe tornando ainda mais dinâmica a experiência de desenvolvimento, isto é possível desde o [realese v1_28](https://code.visualstudio.com/updates/v1_28#_project-level-snippets) que adiciona as configurações de snipptes dentro do diretório `.vscode`

Então vamos por a mão na massa:

1) Se você ainda não tem, crie uma pasta chamada `.vscode` na raiz do seu projeto

2) Agora vamos criar nosso arquivo de snippet, devemos criar um arquivo para cada linguagem de programação que queremos adicionar métodos, para exemplificar este post, usaremos a linguagem `javascript`, mas você pode usar a que quiser.

Crie um novo arquivo chamado `javascript.code-snippets` dentro da pasta `.vscode`

3) Vamos fazer nossos métodos personalizados, os arquivos tem a sintaxe de `JSON` e seguem esta estrutura:

```json
{
  "Escreve console": {
		"prefix": "log",
		"body": [
			"console.log('$1');",
			"$2"
		],
		"description": "Escreve o método console.log"
	},
  "Outro snippet":{
    ...
  }
}
```

Explicando:

 - `"Escreve console"` É o nome do snippet, deve ser único
 - `"prefix"` É com esse "log" que você irá chamar o gatilho que resultará no método
 - `"body"` Aqui fica o corpo do método
 - `"description"` Essa descrição é opcional, ela aparece no IntelliSense

4) Para compartilhar com a equipe basta commitar esse arquivo e subir para o repositório da sua aplicação, todos que usarem VSCode como editor de texto terão acesso a estes snippets. Lembre-se de verificar se a pasta `.vscode` não está adicionada no arquivo `.gitignore`

É isso aí, espero que tenham gostado!
---

Para elogios, sugestões e reclamações abra uma issue no [repositório do blog](https://github.com/vczb/vczb.github.io/issues)
