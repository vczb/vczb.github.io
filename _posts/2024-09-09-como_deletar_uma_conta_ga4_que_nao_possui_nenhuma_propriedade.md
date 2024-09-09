---
layout: post
title: "Configurando notificações de bateria no Ubuntu 20.04"
date: 2022-01-21 19:00:00 +0530
categories: seo ga4
---

Se voce está tentando deletar uma conta no Google Analytics 4 que não possui nenhuma propriedade e não está conseguindo por causa do erro "Missing permissions / Request Access" vou te ensiar um jeito não tão complicado de fazer isso com NodeJS

![Error](/assets/images/20240909/error.png)

Siga os seguintes passos:

1. Abra o Google Cloud e crie um novo projeto:

https://console.cloud.google.com/welcome

![Google Cloud](/assets/images/20240909/gcloud.png)

2. Acesse o seu projeto e vá na aba credentials

https://console.cloud.google.com/apis/credentials

3. Clique em "Add Credentials" e crie uma nova do tipo OAuth ID

![OAuth](/assets/images/20240909/oauth.png)

Você precisa criar uma OAuth key

- tipo OAuth client ID
- Application Type: Web application
- Authorized redirect URI: http://localhost:3000/oauth2callback

4. Nesta mesma página em "Client Secrets" fazer o download do JSON e salvar na raiz do seu projeto.

5. Vá até Enabled APIs & services

- Clique em Google Analytics Admin API
- Enable API

6. Crie um novo projeto NodeJS

```bash
npm init -y
```

7. Instale as bibliotecas:

```bash
npm install express googleapis open
```

8. Adicione estes scrips ao package.json

```json
  "scripts": {
    "start": "node index.js",
    "delete": "node delete_ga_account.js",
  },
```

9. Crie um arquivo chamado `index.js` e adicione o seguinte código:

```javascript
import express from "express";
import fs from "fs";
import path from "path";
import { google } from "googleapis";

// Initialize Express server
const app = express();
const port = 3000;

// Load client credentials
const CLIENT_SECRETS_PATH = path.join(
  "./client_secret.json" // Aquele arquivo que voce fez download antes
);
const credentials = JSON.parse(fs.readFileSync(CLIENT_SECRETS_PATH, "utf-8"));
const { client_id, client_secret } = credentials.web;

// Set up OAuth 2.0 client
const oAuth2Client = new google.auth.OAuth2(
  client_id,
  client_secret,
  `http://localhost:${port}/oauth2callback`
);

function generateAuthUrl() {
  const authUrl = oAuth2Client.generateAuthUrl({
    access_type: "offline",
    scope: ["https://www.googleapis.com/auth/analytics.edit"], // Include analytics.edit scope
  });
  console.log("Authorize this app by visiting this URL:", authUrl);
}

app.get("/oauth2callback", async (req, res) => {
  const code = req.query.code;
  if (code) {
    try {
      // Exchange the authorization code for an access token
      const { tokens } = await oAuth2Client.getToken(code);
      oAuth2Client.setCredentials(tokens);
      console.log("OAuth 2.0 flow complete. Tokens obtained.");

      // Store the tokens in a temporary file for use by the other script
      fs.writeFileSync("tokens.json", JSON.stringify(tokens));
      res.send("Authentication successful! You can now run the other script.");
    } catch (err) {
      console.error("Error retrieving access token", err);
      res.send("Error during the OAuth process.");
    }
  } else {
    res.send("No authorization code found.");
  }
});

app.listen(port, () => {
  console.log(`Server is listening on http://localhost:${port}`);

  // Generate the OAuth URL when the server starts
  generateAuthUrl();
});
```

10. Não se esqueça de renomear o client_secret.json para o mesmo nome do arquivo que você baixou antes

11. Crie um arquivo chamado `delete_ga_account.js` e adicione o seguinte código:

```javascript
import { google } from "googleapis";
import fs from "fs";
import path from "path";
import { fileURLToPath } from "url";

// Fix __dirname for ES modules
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// Load client credentials and tokens
const CLIENT_SECRETS_PATH = path.join(__dirname, "client_secret.json");

const credentials = JSON.parse(fs.readFileSync(CLIENT_SECRETS_PATH, "utf-8"));
const { client_id, client_secret } = credentials.web;

const tokens = JSON.parse(fs.readFileSync("tokens.json", "utf-8"));

// Set up OAuth 2.0 client with saved tokens
const oAuth2Client = new google.auth.OAuth2(
  client_id,
  client_secret,
  "http://localhost:3000/oauth2callback"
);

oAuth2Client.setCredentials(tokens);

// Function to delete the Google Analytics account
async function deleteAccount() {
  try {
    const analyticsAdmin = google.analyticsadmin({
      version: "v1beta",
      auth: oAuth2Client,
    });
    const accountId = "accounts/251892362"; // Adicione o ID da conta que você quer deletar

    // Delete the account
    await analyticsAdmin.accounts.delete({ name: accountId });
    console.log(`Account ${accountId} has been successfully deleted.`);
  } catch (err) {
    console.error("Error deleting account:", err);
  }
}

// Execute the deletion
deleteAccount();
```

12. Não se esqueça de alterar o `client_secret.json` e o ID da conta que você deseja excluir.

13. Execute o comando:

```bash
npm run start
```

Abra o link, autorize. Se tudo deu certo um arquivo token.json deverá ser criado na raiz do seu repositório.

```bash
npm run delete
```

Este comando irá deletar a conta.

---

Espero que tenha ocorrido tudo bem, se não voce pode ler os logs de erro e tentar resolver sozinho. Usar o GPT também pode ajudar.

Obrigado pelo tempo!

viniciuszucatti@gmail.com
