---
description: Busca no Google Maps negócios bem avaliados e gera a lista de leads (2 modos — Redesign ou Criar site)
argument-hint: "[nicho] [cidade] — opcional, usa os padrões do config"
---

Prospecte leads qualificados seguindo a skill `prospeccao-maps`.

## Preparação

1. Leia `prospector-config.json` na pasta conectada. Se não existir, oriente a rodar `/setup` primeiro.
2. **Escolha o MODO de prospecção via `AskUserQuestion` (popup)** — define todo o resto do fluxo:
   - **🔄 Redesign** — negócios que faturam bem mas têm um **site próprio ruim**. Oferta: refazer o site. Abordagem: **e-mail** (capa antes/depois).
   - **✨ Criar site** — negócios que faturam bem mas **não têm site** (só Instagram/WhatsApp/Linktree). Oferta: criar o 1º site. Abordagem: **WhatsApp / DM do Instagram**.
   Guarde o modo escolhido — cada lead será marcado com `modo: redesign` ou `modo: criar`, e `/redesenhar` e `/proposta` se adaptam a ele.
3. Determine nicho e cidade: use os argumentos `$ARGUMENTS` se informados; senão, pergunte qual dos nichos padrão do config usar (e confirme a cidade). O usuário SEMPRE pode trocar na hora — nunca trave nos padrões. Dica: no modo **Criar site**, nichos tradicionais e de alto fluxo (restaurantes, pousadas, clínicas, comércio consolidado) rendem mais — muitos faturam alto há décadas e vivem só de Instagram.
4. Leia `leads.md` na pasta conectada (se existir) para EXCLUIR quem já foi avaliado.

## Execução

Use as ferramentas do Claude in Chrome (carregue via ToolSearch se necessário) para abrir o Google Maps e executar o fluxo da skill `prospeccao-maps` no modo escolhido. Avalie até 25 estabelecimentos ou até bater a meta de leads do config (padrão 10).

**Filtro comum aos dois modos — potencial financeiro:** nota ≥ 4.7 e avaliações ≥ 40 (em cidade pequena, aceite ≥ 30 e avise que afrouxou). Reprovou → próximo.

**Modo 🔄 Redesign** — precisa de **site próprio ativo porém ruim + e-mail público**:
- Sem site / site fora do ar / só diretório-terceiros (Instagram, Linktree, wa.me) → **descarta** (esse é lead do outro modo).
- Site bom → descarta. Site ativo e ruim (2+ defeitos, ver skill) → candidato.
- Sem e-mail público → descarta (a proposta vai por e-mail).
- Coletar: nome, nota, avaliações, telefone, WhatsApp (55DDDnúmero), **e-mail**, URL do site, motivo do site ser ruim.

**Modo ✨ Criar site** — precisa de **NÃO ter site próprio + canal de contato (WhatsApp ou Instagram)**:
- Tem site próprio ativo → descarta (esse é lead do modo Redesign).
- Alvo = quem aparece só com Instagram, Linktree, wa.me como "site", ou nada. Quanto mais avaliações/seguidores, melhor.
- Contato: WhatsApp (55DDDnúmero) do perfil/site, ou o **@ do Instagram** (é por lá a abordagem e a fonte de fotos/conteúdo do futuro site). E-mail é opcional aqui.
- Coletar: nome, nota, avaliações, telefone/WhatsApp, **@Instagram**, e o motivo (ex.: "40 anos, 1.6k avaliações, sem site — só Instagram").

Registre SEMPRE os descartados com o motivo e siga até bater a meta.

## Saída — Google Sheets + dashboard + cópia local

1. **Google Sheets** (conector do Google Drive: `create_file` com CSV em `textContent`, `contentMimeType: text/csv`). Título: `Leads Prospector — [modo] — [nicho] [cidade]`. Colunas: #, Nome, Modo, Nota, Avaliações, Contato (e-mail ou WhatsApp/@Instagram), Site atual, Motivo, Situação (Qualificado/Descartado + motivo), Status. Inclua qualificados E descartados, ranqueados por potencial. Retorne o link.
2. **Cópia local `leads.md`**: cópia de trabalho para controle de status (`novo → redesenhado → publicado → proposta enviada`). Marque o `modo` de cada lead. Em rodadas novas, some aos antigos — nunca duplique.
3. **Dashboard**: crie/atualize `dashboard.html` pela skill `dashboard-leads` (com a tag de `modo` em cada card).

A entrega final DEVE incluir "Dashboard atualizado: [N] leads". Mostre a tabela com o link da planilha e do `dashboard.html`, e sugira o próximo passo: `/redesenhar` para os 5+ melhores leads (no modo escolhido).
