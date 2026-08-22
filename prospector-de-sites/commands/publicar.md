---
description: Publica as páginas redesenhadas no Cloudflare Pages e retorna as URLs públicas
argument-hint: "[nome do cliente ou todos]"
---

Publique páginas no Cloudflare Pages seguindo a skill `deploy-cloudflare`.

## Passos

1. Leia `prospector-config.json`. Se o bloco `cloudflare` não estiver preenchido (`repoRemoto`, `projetoPages`, `subdominio`), oriente o usuário a rodar o `/setup` — não prossiga sem eles.
2. Determine o que publicar: `$ARGUMENTS` (um cliente ou "todos"), ou liste as páginas com status `redesenhado` em `leads.md` e pergunte.
3. **Gere a página-capa de cada cliente**: preencha `references/capa-proposta-template.html` (skill `proposta-email`) com os dados do lead + assinatura do config e salve como `sites/[slug]/proposta.html`. É ela que vai no e-mail de proposta.
4. **Publique seguindo a skill `deploy-cloudflare`**: garanta o clone local do repo de sites, copie a página redesenhada para `[repoLocal]/[slug]/index.html` e a capa para `[repoLocal]/[slug]/proposta.html`, e faça `git add -A` → `commit` → `push`. O Cloudflare Pages faz o build automático em segundos. Sem FTP, sem hPanel, sem senha — o push roda direto do agente.
5. **Verificação HTTPS (bloqueante)**: aguarde o build (~30–60s) e abra cada URL com `https://` — confirme que carrega com cadeado válido. O Cloudflare serve HTTPS por padrão; se der 404, o build ainda está rodando, espere mais um pouco. Link `http://` NUNCA vai para cliente.
6. Atualize `leads.md` e o banco do dashboard: status `publicado` + URL pública nova.

## Saída

Liste, por cliente: URL da página nova (`https://[subdominio]/[slug]/`) e URL da capa (`.../proposta.html`), ambas testadas em https. Sugira o próximo passo: `/proposta` para enviar os e-mails.
