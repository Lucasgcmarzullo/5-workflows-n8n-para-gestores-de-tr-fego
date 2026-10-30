# 📘 Tutorial completo: como usar os workflows

Este passo a passo leva você do zero até os workflows rodando. Não precisa saber programar. Leva em média de 1 a 2 horas na primeira vez, e a parte mais demorada é conectar o Facebook.

> 💡 Dica: faça um workflow por vez. Comece pelo **01** ou pelo **02**, teste, e só depois siga para os outros.

---

## 1. Ter o n8n funcionando

O n8n é a ferramenta onde as automações rodam. Você tem duas opções:

- **n8n Cloud** (mais fácil): crie a conta em n8n.io. Já vem pronto, e tem período de teste.
- **Self-hosted** (mais barato em escala): instale no seu servidor ou VPS. Exige um pouco mais de conhecimento técnico.

Para quem está começando, o Cloud é o caminho mais rápido.

---

## 2. Importar um workflow

1. No n8n, clique em **Workflows** e depois em **Create Workflow**
2. Clique nos **três pontinhos (⋯)** no canto superior direito
3. Escolha **Import from File**
4. Selecione o arquivo `.json` (por exemplo `01-lead-ads-whatsapp.json`)

O desenho do fluxo aparece na tela. Alguns nós vão estar com um aviso vermelho: é normal, ainda faltam as credenciais.

---

## 3. Criar as credenciais

Credenciais são as "chaves" que permitem o n8n acessar suas contas. Para criar uma, abra o nó que está com aviso, clique em **Credential to connect with** e depois em **Create new credential**.

### 3.1 Facebook Lead Ads (workflows 1 a 4)

É a parte mais trabalhosa. Em resumo:

1. Acesse **developers.facebook.com** e crie um **App** do tipo Empresa (Business)
2. Adicione o produto **Facebook Login** e copie o **App ID** e o **App Secret**
3. No n8n, crie a credencial **Facebook Lead Ads OAuth2 API** e cole o App ID e o App Secret
4. O n8n mostra uma **OAuth Redirect URL**. Cole essa URL nas configurações do seu App no Facebook (campo de URIs de redirecionamento OAuth válidos)
5. Clique em **Connect** no n8n e autorize com a conta que administra a página

Pontos de atenção:
- Você precisa ser administrador da **Página** e da **conta de anúncios**
- O App precisa ter as permissões de leads liberadas. Se o Facebook pedir revisão do app, siga as instruções dele
- A documentação oficial do n8n tem esse passo detalhado: procure por "Facebook Lead Ads credentials"

Depois de conectar, abra o nó **Novo Lead (Facebook)** e escolha:
- **Page**: a página que roda os anúncios
- **Form**: o formulário de cadastro

### 3.2 Google Sheets (workflows 2 e 4)

1. Crie a credencial **Google Sheets OAuth2 API**
2. No n8n Cloud basta clicar em **Sign in with Google** e autorizar
3. No self-hosted, é preciso criar um projeto no Google Cloud Console, ativar a Google Sheets API e gerar o Client ID e Client Secret

### 3.3 Telegram (workflow 3)

1. No Telegram, procure por **@BotFather** e envie `/newbot`
2. Escolha um nome e um usuário para o bot
3. O BotFather devolve um **token**. Copie
4. No n8n, crie a credencial **Telegram API** e cole o token
5. Para descobrir o **Chat ID**: mande uma mensagem para o seu bot e procure por **@userinfobot** (para o seu ID pessoal). Para um grupo, adicione o bot ao grupo e use o ID do grupo (começa com `-`)
6. Cole o Chat ID no nó **Avisar no Telegram**, no lugar de `TROQUE_AQUI_CHAT_ID`

### 3.4 API de WhatsApp (workflows 1, 4 e 5)

Os nós de envio são do tipo **HTTP Request** e seguem o formato da **Evolution API v2**. Abra o nó (**Enviar WhatsApp**, **Enviar Follow-up** ou **Enviar Relatório**) e troque:

| Campo | O que colocar |
|-------|---------------|
| URL | `https://seu-servidor/message/sendText/nome-da-instancia` |
| Header `apikey` | A chave da sua API |

Se você usa outra API de WhatsApp, o que muda é a URL, o header e o formato do corpo da mensagem. Consulte a documentação da sua API.

> ⚠️ Evite colar a apikey direto em workflows que você vai compartilhar. Antes de publicar qualquer arquivo, confira que não sobrou nenhuma chave real.

---

## 4. Preparar a planilha (workflows 2 e 4)

1. Crie uma planilha no Google Sheets
2. Renomeie a aba para **Leads**
3. Na linha 1, escreva exatamente estes títulos:

`Data | Nome | Telefone | Email | Status`

4. Copie o **ID da planilha** (está na URL, entre `/d/` e `/edit`)
5. Nos nós do Google Sheets, cole esse ID no lugar de `TROQUE_AQUI_ID_DA_PLANILHA`

Como o follow-up funciona: o workflow 2 salva o lead com Status **Novo**. Depois de 24h, o workflow 4 consulta a planilha. Se o Status não for **Respondeu**, ele manda a mensagem de follow-up. Então, quando o lead falar com você, mude o Status para **Respondeu**.

---

## 5. Configurar o relatório do Meta Ads (workflow 5)

1. Pegue o **ID da conta de anúncios** (Gerenciador de Anúncios → configurações da conta). No nó **Buscar Dados Meta Ads**, ele entra depois de `act_`
2. Gere um **token de acesso** com a permissão `ads_read`. O mais indicado é criar um **Usuário do Sistema** no Business Manager e gerar o token por ele, porque tokens gerados no Graph API Explorer expiram em poucas horas
3. Cole o token no parâmetro `access_token`
4. No nó **Enviar Relatório**, troque `55TROQUE_AQUI_NUMERO_DO_CLIENTE` pelo número com DDI e DDD (exemplo: `5521999999999`)
5. Se o seu resultado não for "lead" (por exemplo, conversas no WhatsApp), ajuste a lista `tipos` no nó **Montar Relatório**

O relatório roda todo dia às 8h. Para mudar o horário, abra o nó **Todo dia 8h**.

---

## 6. Testar antes de ativar

**Testar a captação de leads:**
1. Acesse a ferramenta de testes de Lead Ads do Facebook (busque por "Lead Ads Testing Tool")
2. Escolha sua página e formulário e crie um lead de teste com o **seu** número
3. No n8n, abra o workflow e confira a execução em **Executions**

**Testar o envio:** clique em **Execute Workflow** com o nó de teste do trigger, ou use o lead de teste acima. Se a mensagem chegar no seu WhatsApp, está funcionando.

**Testar o relatório:** clique em **Execute Workflow** manualmente, sem esperar às 8h.

---

## 7. Ativar

Quando estiver tudo certo, ligue a chave **Active** no canto superior direito do workflow. A partir daí ele roda sozinho.

---

## 🛠️ Problemas comuns

| Problema | O que verificar |
|----------|-----------------|
| O workflow não dispara com lead novo | O workflow está **Active**? A página e o formulário estão selecionados? O app do Facebook está com as permissões liberadas? |
| Erro na credencial do Facebook | Confira se a URL de redirecionamento do n8n foi colada no app do Facebook |
| A mensagem de WhatsApp não chega | Teste a API separadamente. Confira URL, instância, apikey e se o número está com DDI (55) |
| O Google Sheets dá erro | Confira se a aba se chama **Leads** e se os títulos da linha 1 estão iguais aos do tutorial |
| O relatório vem zerado | Verifique o ID da conta, o token e se houve gasto no dia anterior |
| O follow-up foi enviado para quem já respondeu | Mude o Status para **Respondeu** na planilha antes das 24h |

---

## ✅ Boas práticas

- Use WhatsApp só com quem pediu contato. Lead que preencheu o formulário já demonstrou interesse.
- Adapte as mensagens para o seu nicho e o seu jeito de falar.
- Antes de colocar em um cliente, rode uma semana com seus próprios leads de teste.
- Guarde uma cópia dos workflows depois de configurar.

---

Ficou com dúvida? Chama no Instagram: [@lucasmarzullo.ads](https://instagram.com/lucasmarzullo.ads)

Se esse conteúdo ajudou, deixa uma ⭐ no repositório!
