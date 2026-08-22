---
name: deploy-cloudflare
description: Esta skill deve ser usada ao publicar páginas no Cloudflare Pages via GitHub — commit + push num repo de sites que o Cloudflare observa, build automático, URL pública .pages.dev e HTTPS. Acione quando o usuário disser "publicar", "subir o site", "colocar no ar", "deploy", "cloudflare", "pages" ou rodar /publicar ou o teste de conexão do /setup.
---

# Deploy no Cloudflare Pages (via GitHub)

Publicar páginas em `[repoLocal]/[slug]/` (index.html + proposta.html), dar `git push` no repo de sites e garantir a URL pública `https://[subdominio]/[slug]/` no ar com HTTPS.

## Como funciona

Um repositório do GitHub (só pros clientes) está conectado a um projeto do **Cloudflare Pages**. A cada `git push` na branch de produção, o Cloudflare faz o build (site estático, sem build command — a raiz do repo já é o site) e publica em segundos. **Não há FTP, hPanel, senha nem script na máquina do usuário — o deploy roda direto do agente por HTTPS.**

## Config

Tudo vem de `prospector-config.json` (bloco `cloudflare`):
- `repoRemoto`: URL do repo de sites (ex.: `https://github.com/usuario/prospector-sites.git`).
- `repoLocal`: pasta do clone local dentro da pasta conectada (padrão `sites-publicados`).
- `projetoPages`: nome do projeto no Cloudflare Pages.
- `subdominio`: host público (ex.: `prospector-sites.pages.dev`).
- `branch`: branch de produção (padrão `main`).

**Nenhum segredo mora aqui.** O `git push` usa as credenciais já configuradas na máquina (credential manager / PAT). Se faltar autenticação, oriente o usuário a autenticar o git uma vez (`git push` pedirá login pelo helper do sistema) — nunca peça token nem senha pelo chat.

## Método — commit + push (único, roda no agente)

1. **Garanta o clone local**: se `[repoLocal]` não existir na pasta conectada, clone: `git clone [repoRemoto] [repoLocal]`. Se já existir, atualize antes: `git -C [repoLocal] pull --ff-only`.
2. **Copie as páginas de cada cliente** para o clone, uma pasta por slug:
   - página redesenhada → `[repoLocal]/[slug]/index.html`
   - capa da proposta → `[repoLocal]/[slug]/proposta.html`
3. **Publique**:
   ```
   git -C [repoLocal] add -A
   git -C [repoLocal] commit -m "publica [slug1], [slug2]..."
   git -C [repoLocal] push origin [branch]
   ```
   Se `push` falhar por autenticação, pare e oriente o usuário a rodar o push uma vez na máquina dele para o git salvar as credenciais; jamais exponha token no chat. Se falhar por `non-fast-forward`, rode `git -C [repoLocal] pull --rebase` e repita o push.
4. **Aguarde o build** (~20–60s): o Cloudflare Pages detecta o push e publica sozinho. Não precisa acionar nada.

## Verificação (obrigatória, após o push)

1. Abra `https://[subdominio]/[slug]/` e a capa `.../proposta.html` — confirme que carregam com o conteúdo certo. Se der 404, aguarde mais ~30s (build ainda rodando) e tente de novo.
2. **HTTPS**: o Cloudflare já serve tudo com HTTPS válido por padrão (cadeado). Link `http://` nunca vai para cliente — sempre `https://`.
3. Atualize `leads.md` + dashboard com status `publicado` e a URL pública.

## Teste de conexão do /setup

Crie `[repoLocal]/teste/index.html` simples ("Funcionou!"), faça commit + push e confirme `https://[subdominio]/teste/` no ar. Se o clone/push falhar, diagnostique (repo criado? Pages conectado ao repo? git autenticado?) antes de concluir o setup.
