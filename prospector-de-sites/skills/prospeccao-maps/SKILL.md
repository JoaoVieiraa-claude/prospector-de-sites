---
name: prospeccao-maps
description: Esta skill deve ser usada ao prospectar clientes no Google Maps — buscar negócios bem avaliados nos dois modos (Redesign = com site ruim; Criar site = sem site), qualificar leads, avaliar sites e montar a planilha. Acione quando o usuário disser "prospectar", "buscar clientes", "achar leads", "clientes com site ruim", "clientes sem site" ou rodar /prospectar.
---

# Prospecção no Google Maps

Encontrar o cliente ouro: negócio que JÁ fatura bem (nota alta, muitas avaliações) mas perde clientes por causa do site. Não se cria demanda — conserta-se onde o dinheiro escapa. **Dois modos**, escolhidos no `/prospectar`:

- **🔄 Redesign** — o site existe mas é ruim → oferta = refazer.
- **✨ Criar site** — não há site (só Instagram/WhatsApp) → oferta = criar o primeiro.

## Fluxo (via Claude in Chrome)

1. Abrir `https://www.google.com/maps` e buscar `[nicho] em [cidade]`.
2. Percorrer os resultados. Para cada um, ler nota, nº de avaliações e o link de "site".
3. **Filtro 1 — potencial financeiro (os DOIS modos):** nota ≥ 4.7 E avaliações ≥ 40. Em cidade pequena, aceite ≥ 30 e avise que afrouxou. Reprovou → próximo.
4. **Filtro 2 — depende do modo** (ver abaixo).
5. Parar ao bater a meta de leads (config, padrão 10) ou após avaliar 25.
6. Pular quem já está em `leads.md`.

### Dica de leitura do Maps (importante)
- O painel de detalhe às vezes não abre por clique. Alternativa confiável: navegar para `https://www.google.com/maps/search/[Nome do lugar]+[cidade]` (resultado único abre o perfil) e ler com `get_page_text`.
- Para achar @Instagram e WhatsApp de quem não tem site, uma busca web `https://www.google.com/search?q=[nome]+[cidade]+instagram+whatsapp` + `get_page_text` costuma trazer o @ e o número direto dos posts/bio.
- O "Website" no Maps pode ser um `wa.me/`, `linktr.ee` ou `instagram.com` disfarçado — isso NÃO conta como site próprio (é lead do modo Criar site).

## Modo 🔄 Redesign — Filtro 2 (tem site próprio ativo porém ruim)

- Sem site, site fora do ar, ou "site" que é diretório de terceiros (Instagram/Linktree/wa.me) → **descarta** (é lead do modo Criar site).
- Abrir o site em nova aba (ou `curl` do HTML) e avaliar. **Site bom → descarta.** Site ativo com **2 ou mais** problemas abaixo → candidato.
- **E-mail público é OBRIGATÓRIO** (a proposta vai por e-mail). Procure no site (rodapé, contato, `mailto:`) e no Google. Sem e-mail → descarta.

**Problemas que caracterizam site ruim (guardar o motivo específico e verificável):**
- Layout datado (template de 10+ anos, fontes de sistema, imagens esticadas/pixeladas)
- Sem CTA claro de agendamento/contato na primeira dobra (nenhum botão de WhatsApp/agenda)
- Domínio gratuito ou plataforma alheia (Google Sites, Wix grátis, subdomínio com marca da plataforma)
- Não responsivo (quebra no mobile — checar meta viewport)
- Conteúdo desorganizado, serviços escondidos, texto corrido sem seções
- Sem prova social (nenhum depoimento, apesar da nota alta no Google)

## Modo ✨ Criar site — Filtro 2 (NÃO tem site próprio)

- **Tem site próprio ativo → descarta** (é lead do modo Redesign).
- **Alvo = quem só tem Instagram, Linktree, wa.me como "site", ou nada.** Quanto mais avaliações no Google e seguidores no Instagram, melhor (sinal de faturamento). Negócios tradicionais (décadas de casa) sem site são ouro.
- **Contato = WhatsApp ou @Instagram** (é por lá a abordagem e a fonte de fotos/conteúdo do futuro site). E-mail é opcional.
- Motivo objetivo, ex.: "40 anos, 1.665 avaliações, 7,6k seguidores, sem site — só @sambura.angra".

## Coleta por lead

Comum: nome, nicho, cidade, nota, nº de avaliações, telefone, **WhatsApp em `55DDDnúmero`**, `modo`.
- **Redesign**: + **e-mail** (obrigatório) + URL do site + motivo do site ser ruim.
- **Criar site**: + **@Instagram** (fonte de conteúdo) + motivo (sem site + prova de faturamento).

**WHATSAPP: capture sempre, separado do telefone.** Fontes: botão/link de WhatsApp no site ou bio (`wa.me/`, `api.whatsapp.com` — extraia o número); celular do perfil do Maps (9º dígito = celular = assuma WhatsApp); posts do Instagram. Formato `55 + DDD + número`.

## Saída — Google Sheets + leads.md local

Planilha do Google (conector do Drive: `create_file` com CSV em `textContent`, `contentMimeType: text/csv`). Título `Leads Prospector — [modo] — [nicho] [cidade]`; incluir qualificados e descartados, ranqueados por potencial. Entregar o link.

Cópia de trabalho `leads.md` (com a coluna `modo` e o contato certo por modo):

```markdown
| # | Nome | Modo | Nota | Aval. | Contato (e-mail ou WhatsApp/@IG) | Site atual | Motivo | Status |
```

Status: `novo`, `redesenhado`, `publicado`, `proposta enviada`. Ao mudar status, regenerar a planilha e o `dashboard.html` (skill `dashboard-leads`). Nunca sobrescrever leads antigos — só acrescentar/atualizar.

## Boas práticas

- Trabalhar por região dá vantagem: menos concorrência e conhecimento local.
- Enquanto o navegador trabalha, não interromper o fluxo com perguntas — só reportar a tabela final.
- Se o Google pedir login/captcha, pausar e avisar o usuário.
