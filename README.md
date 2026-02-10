# WhatsApp Expenses Bot (Node.js) — Deploy no Railway

Este repositório contém um bot de WhatsApp (Cloud API do Meta) para registrar gastos via mensagem e salvar no SQLite. Inclui endpoints de webhook, comandos (`saldo`, `por categoria`, `exportar`) e Dockerfile para deploy no Railway.

## 1) Pré-requisitos
- Meta for Developers → App (Business) com produto **WhatsApp** habilitado (Phone Number ID, WABA ID, Access Token).
- **Assinar o WABA** na app (`/{WABA_ID}/subscribed_apps`).
- Token de longa duração (System User) para `CLOUD_API_TOKEN`.
- Conta no **Railway**.

## 2) Variáveis de ambiente (.env)
Copie `.env.example` para `.env` e preencha:

```
PORT=3000
VERIFY_TOKEN=um_token_que_somente_voce_sabe
WA_PHONE_NUMBER_ID=xxxxxxxxxxxxxxx
CLOUD_API_TOKEN=EAAG...
APP_SECRET=SEU_APP_SECRET
PUBLIC_BASE_URL=https://SEU-SUBDOMINIO.up.railway.app
GRAPH_API_VERSION=v19.0
```

> Em produção, **não** faça commit do `.env`. No Railway, configure em **Variables**.

## 3) Rodar localmente
```bash
npm install
npm run dev
```
Exponha o servidor com ngrok e configure o **Callback URL** no painel do Meta: `https://SEU_NGROK/webhook` (Verify Token = `VERIFY_TOKEN`).

## 4) Deploy no Railway
1. Suba este repositório para seu GitHub.
2. No Railway: **New Project → Deploy from GitHub Repo**.
3. Em **Variables**, informe as variáveis do `.env` (acima).
4. A URL pública será algo como `https://seuapp-production.up.railway.app`.
5. No Meta for Developers → WhatsApp → **Configuration**:
   - **Callback URL**: `https://seuapp-production.up.railway.app/webhook`
   - **Verify Token**: o mesmo do `.env`
6. Assine o WABA:
```bash
curl -X POST   "https://graph.facebook.com/v21.0/WHATSAPP_BUSINESS_ACCOUNT_ID/subscribed_apps"   -H "Authorization: Bearer SEU_ACCESS_TOKEN"
```

## 5) Endpoints
- `GET /` → Health check
- `GET /webhook` → Verificação (hub.challenge)
- `POST /webhook` → Receber mensagens do WhatsApp
- `GET /export?phone=5511999999999&month=YYYY-MM` → Exporta CSV

## 6) Comandos úteis pelo WhatsApp
- `gastei 45,90 mercado`
- `saldo` ou `total mês`
- `por categoria`
- `exportar`

## 7) Segurança
- Verificação da assinatura `x-hub-signature-256` via `APP_SECRET`.
- Resposta 200 imediata ao webhook para evitar reentregas.

## 8) Licença
MIT
