---
description: Escreve a proposta do lead — e-mail (modo Redesign) ou texto de WhatsApp/DM (modo Criar site)
argument-hint: "[nome do cliente ou todos]"
---

Escreva as propostas dos leads com página publicada. O canal depende do `modo` de cada lead (em `leads.md`).

## Passos comuns

1. Leia `prospector-config.json` (assinatura e modo de envio) e `leads.md`.
2. Determine os destinatários: `$ARGUMENTS`, ou todos os leads com status `publicado` que ainda não receberam proposta.
3. Garanta a **página-capa** publicada de cada cliente (`https://[subdominio]/[slug]/proposta.html`). Se não existir, gere e publique agora (template na skill `proposta-email`, deploy pela skill `deploy-cloudflare`). NUNCA mencione preço, em nenhum modo.

## Modo 🔄 Redesign → e-mail (skill `proposta-email`)

Só para leads com **e-mail confirmado**. Para cada um, escreva o e-mail seguindo a skill `proposta-email` na íntegra: elogio baseado nas avaliações do Google, o defeito específico do site apontado na prospecção, e — como ÚNICO link — a capa publicada.

- **Checklist anti-spam (bloqueante):** 1 link, sem palavras-gatilho, sem anexo, assunto-pergunta ≤ 60 caracteres, primeira linha personalizada. Reescreva até passar.
- **Envio** conforme o config: `rascunho` (padrão) = criar rascunho no Gmail para revisão; `enviar direto` = enviar pelo Gmail (ou criar rascunho e avisar).

## Modo ✨ Criar site → texto de WhatsApp / DM do Instagram

Aqui não há e-mail nem checklist anti-spam — a abordagem é 1 a 1, humana, pelo WhatsApp ou DM do @Instagram do lead. Para cada cliente, escreva um **texto curto e pessoal** seguindo a skill `proposta-email` (seção "Mensagem de WhatsApp/DM"):

- Primeira linha 100% personalizada (elogio real citando as avaliações do Google ou algo do Instagram dele).
- O gancho: notou que ele fatura/é referência mas **não tem site** — e que já montou uma prévia.
- **Um único link**: a capa/página publicada (`https://[subdominio]/[slug]/`).
- Tom de gente, sem juridiquês, sem preço. Curto (cabe numa tela de celular).
- **Não envie automaticamente**: entregue o texto pronto + o link, para o usuário colar no WhatsApp/DM (ou, se pedir, use o Claude in Chrome no WhatsApp Web / Instagram — sempre com o usuário confirmando o envio).

## Fechamento (os dois modos)

Atualize `leads.md` e o dashboard: status `proposta` + data. Resuma: quantas propostas e por qual canal, com o link da capa de cada uma. Lembre: `/respostas` verifica quem respondeu e `/followup` cuida de quem está 3+ dias sem responder.
