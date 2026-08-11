---
name: deploy-cloudflare
description: Esta skill deve ser usada ao publicar páginas de clientes no Cloudflare Pages via GitHub — copiar a página para o repositório de clientes, commit e push (o Cloudflare publica sozinho), montar a URL pública e verificar o HTTPS. Acione quando o usuário disser "publicar", "subir o site", "colocar no ar", "deploy", "cloudflare" ou rodar /publicar ou o teste de conexão do /setup.
---

# Deploy no Cloudflare Pages (via GitHub)

Publicar a página redesenhada de cada cliente em `[repoDir]/[slug]/index.html`, dar `git push` e deixar o Cloudflare Pages publicar sozinho. URL pública: `https://<projeto>.pages.dev/[slug]/`.

## Modelo (importante)

Não há FTP, senha nem publicador local. Um único repositório GitHub (`clientes-prospector`) guarda **todos** os clientes, um por pasta na raiz (`/[slug]/index.html`). Esse repositório está conectado a **um** projeto do Cloudflare Pages: **todo `git push` na branch de produção dispara o deploy automático** (HTTPS grátis, ~1 minuto).

## Config

Tudo vem de `prospector-config.json` (bloco `cloudflare`):

- `repoDir` — caminho local do clone do repositório de clientes (ex.: `C:\Users\...\clientes-prospector`).
- `repoUrl` — URL do repositório no GitHub.
- `pagesUrl` — URL pública do projeto Cloudflare Pages (ex.: `https://clientes-prospector.pages.dev`), preenchida depois de conectar o repositório no painel do Cloudflare.
- `branch` — branch de produção (padrão `main`).

Se `repoDir` não existir ou não for um repositório git, oriente o usuário a rodar `/setup` (que clona o repo e configura). Se `pagesUrl` estiver vazio, a publicação no git funciona, mas ainda não dá para montar/verificar a URL pública — peça ao usuário para conectar o repo no Cloudflare (dashboard → Workers & Pages → Pages → Connect to Git) e colar a URL `pages.dev` nas Configurações do dashboard.

## Publicação (para cada cliente)

1. Garanta que `repoDir` está atualizado: `git -C "[repoDir]" pull --ff-only` (silencioso; ignore se não houver upstream novo).
2. Copie a página final para o repositório: `sites/[slug]/[slug].html` → `[repoDir]/[slug]/index.html`. Se existir a capa da proposta, copie também `sites/[slug]/proposta.html` → `[repoDir]/[slug]/proposta.html`.
3. Commit + push (é isto que publica):
   ```bash
   git -C "[repoDir]" add -A
   git -C "[repoDir]" commit -m "Publica/atualiza [slug]"
   git -C "[repoDir]" push
   ```
   O credential helper do `gh` cuida da autenticação. Se o push falhar por autenticação, peça ao usuário para rodar `gh auth login` uma vez.
4. **Aguarde ~60–90s** (o build do Cloudflare) antes de verificar.

## Verificação (obrigatória)

1. Abra `https://<projeto>.pages.dev/[slug]/` (monte a partir de `pagesUrl` + `/[slug]/`) e a capa `.../[slug]/proposta.html`, se houver — confirme que carregam com o conteúdo certo.
2. **HTTPS**: o Cloudflare já serve tudo em HTTPS com cadeado válido, automaticamente — não há passo manual de SSL. Se a URL ainda der 404 logo após o push, o deploy pode não ter terminado: espere mais ~60s e tente de novo (o primeiro deploy do projeto costuma demorar um pouco mais).
3. Atualize `leads.md` + dashboard (skill `dashboard-leads`): `status='publicado'` e `urlNova = https://<projeto>.pages.dev/[slug]/`.

## Teste de conexão do /setup

Se `repoDir` e `repoUrl` estão configurados: crie uma página `_teste/index.html` simples ("Funcionou!"), faça commit e push, aguarde ~90s e confirme que `https://<projeto>.pages.dev/_teste/` abre (se `pagesUrl` já estiver preenchida). Depois remova a pasta `_teste`, commit e push de novo. Se `pagesUrl` ainda não existir, faça só o push de teste e explique que a URL pública aparece após conectar o repo no Cloudflare.

## Domínio próprio (opcional)

Para um link mais bonito na proposta, o usuário pode adicionar um domínio custom no projeto Cloudflare Pages (painel → projeto → Custom domains). Depois, atualize `pagesUrl` no dashboard para o domínio próprio — as URLs dos clientes passam a usá-lo.
