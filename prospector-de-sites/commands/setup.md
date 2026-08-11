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

## 4. Conexão com o Cloudflare Pages (via GitHub)

A publicação usa um repositório GitHub de clientes conectado ao Cloudflare Pages: cada cliente vira uma pasta `/[slug]/` na raiz do repo e **cada `git push` publica sozinho** (HTTPS grátis). Não há FTP nem senha.

Pergunte se o usuário já tem o repositório de clientes e o projeto no Cloudflare Pages.

- **Se ainda não tem**: ajude a montar:
  1. Crie (ou reutilize) um repositório GitHub privado para os clientes (ex.: `clientes-prospector`) e clone-o localmente numa pasta `repoDir`. Se o `gh` estiver disponível, dá para criar e já subir com `gh repo create <nome> --private --source=. --push`.
  2. Peça ao usuário para conectar esse repositório no Cloudflare Pages: painel → **Workers & Pages** → **Pages** → **Connect to Git** → branch `main`, **framework preset** None, **build command** vazio, **build output** `/` → Save and Deploy. O Cloudflare gera uma URL `pages.dev`.
- Preencha o bloco `cloudflare` do config (abaixo) com `repoDir`, `repoUrl`, `pagesUrl` e `branch`. O `pagesUrl` pode ser colado depois de conectar o Cloudflare — enquanto isso, os pushes já funcionam. A autenticação do git é cuidada pelo `gh` (sem senha no config).

O bloco pode ser preenchido na aba **Configurações → Conexão Cloudflare** do dashboard, ou direto no `prospector-config.json`.

## 5. Salvar e testar

Salve tudo em `prospector-config.json` na pasta conectada, neste formato:

```json
{
  "assinatura": { "nome": "", "apresentacao": "", "whatsapp": "" },
  "prospeccao": { "nichos": ["nutricionistas", "psicologos", "advogados", "psiquiatras"], "cidade": "", "leadsPorBusca": 10 },
  "envio": { "modo": "rascunho" },
  "cloudflare": { "repoDir": "", "repoUrl": "", "pagesUrl": "", "branch": "main" }
}
```

Se `repoDir` e `repoUrl` já estão configurados, teste a conexão seguindo a skill `deploy-cloudflare`: crie uma página `_teste/index.html` simples, faça commit e push, e (se `pagesUrl` já estiver preenchida) confirme que `https://<projeto>.pages.dev/_teste/` abre; depois remova a pasta `_teste` e push de novo. Se o push falhar por autenticação, oriente a rodar `gh auth login` uma vez.

## 6. Dashboard inicial

Siga a seção "Setup" da skill `dashboard-leads`: copie `dashboard-server.py` e `iniciar-dashboard.bat` para a raiz da pasta conectada, crie o banco `prospector.db` (schema da skill) e gere o `dashboard.html` do template. Explique ao usuário: duplo clique em `iniciar-dashboard.bat` abre o painel completo em http://localhost:8765 com edição/exclusão salvando no banco (requer Python no Windows; sem ele, o dashboard.html abre no modo leitura).

## 7B. Entregar o manual e o dashboard

Copie da pasta do plugin para a pasta conectada (sobrescrevendo versões antigas): `manual.html` (manual do usuário) e o iniciador do dashboard certo para o sistema (`iniciar-dashboard.bat` no Windows ou `iniciar-dashboard.command` no Mac). No Cloudflare **não há publicador local** — quem publica é o `git push` durante o `/publicar`. Apresente o `manual.html` ao usuário com a frase: "Esse é o seu manual — guarda ele que responde 90% das dúvidas."

## 7. Encerrar

Confirme o que foi salvo e explique o ciclo (guiando SEMPRE o próximo passo ao fim de cada comando): `/prospectar` → `/redesenhar` → `/publicar` → `/proposta`, com `/editor` opcional para ajustes manuais e o `dashboard.html` como painel de controle de tudo.
