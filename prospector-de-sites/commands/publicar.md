---
description: Publica as páginas redesenhadas no Cloudflare Pages (via GitHub) e retorna as URLs públicas
argument-hint: "[nome do cliente ou todos]"
---

Publique páginas no Cloudflare Pages seguindo a skill `deploy-cloudflare`.

## Passos

1. Leia `prospector-config.json` (bloco `cloudflare`: `repoDir`, `repoUrl`, `pagesUrl`, `branch`). Se `repoDir` não existir ou não for um repositório git, oriente a rodar `/setup` primeiro — não prossiga sem ele.
2. Determine o que publicar: `$ARGUMENTS` (um cliente ou "todos"), ou liste as páginas com status `redesenhado` em `leads.md` e pergunte.
3. **Gere a página-capa de cada cliente**: preencha `references/capa-proposta-template.html` (skill `proposta-email`) com os dados do lead + assinatura do config e salve como `sites/[slug]/proposta.html`. É ela que vai no e-mail de proposta.
4. **Publique seguindo a skill `deploy-cloudflare`**: para cada cliente, copie `sites/[slug]/[slug].html` → `[repoDir]/[slug]/index.html` e (se existir) `sites/[slug]/proposta.html` → `[repoDir]/[slug]/proposta.html`; depois `git add -A`, `git commit` e `git push` no `repoDir`. O Cloudflare Pages publica sozinho em ~1 minuto. Um único push pode levar vários clientes de uma vez.
5. **Verificação (bloqueante)**: aguarde ~60–90s e abra `https://<projeto>.pages.dev/[slug]/` (monte a partir de `pagesUrl`) e a capa `.../[slug]/proposta.html`. O Cloudflare já entrega HTTPS com cadeado válido automaticamente. Se der 404 logo após o push, o deploy ainda não terminou — espere mais ~60s e tente de novo. Só considere publicado quando a URL abrir.
6. Atualize `leads.md` e o banco do dashboard: status `publicado` + `urlNova = https://<projeto>.pages.dev/[slug]/`.

## Saída

Liste, por cliente: URL da página nova e URL da capa (`.../proposta.html`), ambas testadas em https. Sugira o próximo passo: `/proposta` para enviar os e-mails.
