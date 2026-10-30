# 🚀 Workflows n8n para Gestores de Tráfego

5 automações prontas para importar no n8n e captar, organizar e acompanhar leads de anúncios.

Feito por [@lucasmarzullo.ads](https://instagram.com/lucasmarzullo.ads)

👉 **Primeira vez? Siga o [tutorial completo passo a passo](TUTORIAL.md).**

## 📦 O que tem aqui

| # | Arquivo | O que faz |
|---|---------|-----------|
| 1 | `01-lead-ads-whatsapp.json` | Envia WhatsApp automático quando entra lead do Facebook Ads |
| 2 | `02-lead-planilha.json` | Salva o lead no Google Sheets |
| 3 | `03-alerta-novo-lead.json` | Avisa você ou o cliente no Telegram a cada lead novo |
| 4 | `04-follow-up-24h.json` | Segunda mensagem se o lead não respondeu em 24h (usa a planilha do workflow 2) |
| 5 | `05-relatorio-meta-ads.json` | Relatório diário de gasto, leads e CPL no WhatsApp |
| 📘 | `TUTORIAL.md` | Passo a passo completo de configuração |

## ⚙️ Como usar

1. No n8n, clique em **Workflows → Import from File**
2. Selecione o `.json` do workflow que quer usar
3. Em cada nó, selecione suas credenciais (Facebook, Google, Telegram)
4. Troque tudo que estiver como `TROQUE_AQUI`
5. Teste com um lead seu e depois ative o workflow

## 🔑 Você vai precisar de

- n8n (cloud ou self-hosted)
- Conta de anúncios no Meta Ads (e uma página com formulário de Lead Ads)
- Uma API de WhatsApp com endpoint de envio de texto (os exemplos usam o formato da Evolution API v2: `POST /message/sendText/{instancia}` com `number` e `text`)
- Conta Google (para o Sheets)
- Bot do Telegram (só para o workflow 3)

## 📊 Planilha (workflows 2 e 4)

Crie uma aba chamada **Leads** com estas colunas na linha 1:

`Data | Nome | Telefone | Email | Status`

Para o follow-up não ser enviado a quem já falou com você, mude o **Status** para `Respondeu`.

## 📈 Relatório Meta Ads (workflow 5)

Precisa de um token com permissão `ads_read` e do ID da conta de anúncios. Se o seu tipo de resultado não for "lead", ajuste a lista `tipos` no nó **Montar Relatório**.

## ⚠️ Importante

- Use WhatsApp de forma responsável e só com quem pediu contato.
- Não compartilhe tokens e chaves de API. Troque os `TROQUE_AQUI` só na sua instância.

## 💬 Dúvidas

Chama no Instagram: [@lucasmarzullo.ads](https://instagram.com/lucasmarzullo.ads)

Se ajudou, deixa uma ⭐ no repositório!
