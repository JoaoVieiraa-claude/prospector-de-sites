---
description: Configura o plugin — assinatura, preferências e conexão com o Cloudflare Pages (roda uma vez)
---

Configure o ambiente do Prospector de Sites. Siga esta ordem:

## 1. Pasta de trabalho

Verifique se há uma pasta do usuário conectada. Se não houver, peça para conectar uma pasta (ex.: "Clientes") usando a ferramenta de solicitação de pasta — tudo (config, leads e sites criados) será salvo nela para persistir entre sessões.

## 2. Verificar config existente

Procure `prospector-config.json` na pasta conectada. Se existir, mostre um resumo (sem exibir a senha) e pergunte o que o usuário quer atualizar. Se não existir, colete os dados abaixo.

## 3. Dados do usuário (perguntar via AskUserQuestion / formulário)

Colete:

- **Assinatura da proposta**: nome completo, como quer se apresentar (ex.: "Designer de páginas de alta conversão") e WhatsApp/telefone de contato.
- **Nichos padrão de prospecção**: sugira nutricionistas, psicólogos, advogados e psiquiatras como ponto de partida, mas deixe o usuário editar livremente.
- **Cidade/região padrão**.
- **Leads qualificados por busca**: padrão 10.
- **Modo de envio da proposta**: padrão "criar rascunho no Gmail para revisão" (recomendado). Alternativa: enviar direto.

## 4. Deploy no Cloudflare Pages (via GitHub)

A publicação é por **git push** num repositório de sites que o **Cloudflare Pages** observa — o build é automático e roda direto do agente, sem FTP, sem senha e sem script na máquina. Pergunte se o usuário já tem conta no GitHub e no Cloudflare (as duas gratuitas).

- **Se ainda não tem**: oriente a criar as contas em github.com e dash.cloudflare.com (grátis) e voltar a rodar `/setup`. Salve o config parcial e encerre.
- **Se já tem**, guie o setup único (o usuário faz nas contas dele; você nunca pede token nem senha no chat):
  1. **Criar o repo de sites**: no GitHub, um repositório novo só pros clientes (ex.: `prospector-sites`), vazio. Anote a URL `.git`.
  2. **Conectar o Cloudflare Pages**: dash.cloudflare.com → **Workers & Pages** → **Create** → **Pages** → **Connect to Git** → escolher o repo. Sem build command, output directory `/` (site estático). Isso gera o subdomínio `https://[projeto].pages.dev`.
  3. **Autenticar o git na máquina** (uma vez): peça pro usuário fazer um `git push` qualquer nesse repo (ou logar pelo credential manager) pra que o push do plugin funcione sem pedir senha. Nunca peça o token pelo chat.
  4. Ele preenche os campos na aba **Configurações → Conexão Cloudflare** do dashboard (repo remoto, projeto Pages, subdomínio) → salva direto no `prospector-config.json`, sem passar pelo chat. Ou edita o arquivo na mão.

## 5. Salvar e testar

Salve tudo em `prospector-config.json` na pasta conectada, neste formato:

```json
{
  "assinatura": { "nome": "", "apresentacao": "", "whatsapp": "" },
  "prospeccao": { "nichos": ["nutricionistas", "psicologos", "advogados", "psiquiatras"], "cidade": "", "leadsPorBusca": 10 },
  "envio": { "modo": "rascunho" },
  "cloudflare": { "repoRemoto": "", "repoLocal": "sites-publicados", "projetoPages": "", "subdominio": "", "branch": "main" }
}
```

Se os dados do Cloudflare foram informados, teste a conexão seguindo a skill `deploy-cloudflare`: clone o repo, publique uma página `teste/index.html` simples e confirme `https://[subdominio]/teste/` no ar. Se o teste falhar, diagnostique (repo criado? Pages conectado ao repo? git autenticado?) antes de concluir.

## 6. Dashboard inicial

Siga a seção "Setup" da skill `dashboard-leads`: copie `dashboard-server.py` e `iniciar-dashboard.bat` para a raiz da pasta conectada, crie o banco `prospector.db` (schema da skill) e gere o `dashboard.html` do template. Explique ao usuário: duplo clique em `iniciar-dashboard.bat` abre o painel completo em http://localhost:8765 com edição/exclusão salvando no banco (requer Python no Windows; sem ele, o dashboard.html abre no modo leitura).

## 7B. Entregar o manual e o dashboard

Copie da pasta do plugin para a pasta conectada (sobrescrevendo versões antigas): `manual.html` (manual do usuário) e o iniciador do dashboard certo para o sistema do usuário (`iniciar-dashboard.bat` no Windows ou `iniciar-dashboard.command` no Mac). O deploy não precisa de nenhum script local — roda por git push direto do agente. Apresente o `manual.html` ao usuário com a frase: "Esse é o seu manual — guarda ele que responde 90% das dúvidas."

## 7. Encerrar

Confirme o que foi salvo e explique o ciclo (guiando SEMPRE o próximo passo ao fim de cada comando): `/prospectar` → `/redesenhar` → `/publicar` → `/proposta`, com `/editor` opcional para ajustes manuais e o `dashboard.html` como painel de controle de tudo.
