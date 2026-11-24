---
name: eapbuild
description: Use quando o usuário falar de orçamentos de obra no eapbuild — projetos, EAP (estrutura analítica do projeto), itens, quantidades, preços, pendências da IA, link ou PDF/planilha para o cliente. Ensina a ler antes de alterar, a pedir aprovação antes de confirmar mudanças e a nunca mandar custo ou BDI ao cliente.
when_to_use: Pedidos como eapbuild, meu orçamento, EAP do projeto, pendências do projeto, mude a quantidade do item, mande o orçamento para o cliente, link do cliente, PDF do orçamento.
---

# eapbuild

O eapbuild monta o orçamento de uma obra (a EAP) a partir das pranchas que o usuário sobe. A IA do eapbuild lê as pranchas, cria os itens com quantidade e preço e levanta **pendências** quando falta informação. As ferramentas `eapbuild` (servidor MCP) agem **como o próprio usuário**, com as mesmas regras do app.

## Ferramentas

| Ferramenta | Para quê |
|---|---|
| `listar_projetos` | Descobrir o `project_id` pelo nome; ver status e pendências abertas. |
| `ver_projeto` | Panorama: dados, status, contagem de itens, totais (custo e preço ao cliente). |
| `ver_eap` | Itens agrupados por fase: código, descrição, unidade, quantidade, preços, tipo (M.O./Mat), pendente. Filtre com `fase`. |
| `listar_pendencias` | Dúvidas da IA sobre as pranchas. Use `status=open` para as abertas. |
| `responder_pendencia` | Registrar a resposta do usuário a uma pendência. |
| `editar_eap_com_ia` | Pedir uma edição da EAP em linguagem natural. |
| `confirmar_alteracao` | Aplicar (`aplicar=true`) ou descartar (`aplicar=false`) uma alteração pendente. |
| `link_do_cliente` | Link público do cliente, com PDF e planilha, só com o preço ao cliente. |
| `novo_orcamento` | Link da página onde o usuário envia as pranchas de um orçamento novo. |

## Regras

1. **Leia antes de alterar.** Antes de qualquer edição, chame `ver_projeto` e `ver_eap` (com `fase` se a EAP for grande) e cite os itens pelo **código** (ex.: `3.1.02`) no pedido a `editar_eap_com_ia`. Nunca invente IDs nem códigos.
2. **Sempre pergunte antes de confirmar.** Quando `editar_eap_com_ia` devolver `confirmacao_pendente`, mostre ao usuário o `resumo` e cada operação e **pergunte se pode aplicar**. Só chame `confirmar_alteracao` com `aplicar=true` depois de um "sim" explícito. Se o usuário recusar, chame com `aplicar=false`. O `prompt_token` expira em 15 minutos e vale uma vez só.
3. **Custo e BDI nunca vão para o cliente.** `ver_eap` e `ver_projeto` trazem valores internos (custo unitário, custo total, BDI embutido). Não copie esses números em e-mails, mensagens ou documentos para o cliente. Para o cliente, use `link_do_cliente` e envie `url_cliente`, `pdf_url` ou `xlsx_url`: eles mostram só o preço ao cliente.
4. **Pendências: só com informação do usuário.** Em `responder_pendencia`, escreva o que o usuário disse (ex.: "a altura do muro é 2,10 m"). Não chute medidas. Se não souber, pergunte ao usuário.
5. **Projeto aguardando pagamento.** Se `ver_projeto` mostrar `ia_bloqueada: true`, `editar_eap_com_ia` não funciona até o usuário liberar o projeto no app. Explique isso em vez de tentar de novo.
6. **Erros.** As mensagens de erro já vêm em português e dizem o que fazer (ex.: "aguarde alguns segundos" entre mensagens para a IA). Siga a orientação; não repita a mesma chamada em sequência.
7. **Sem conexão ou sem token.** Se as ferramentas `eapbuild` não aparecerem, ou falharem por login/token (401, "não autorizado", servidor desconectado no `/mcp`), não tente de novo. Diga ao usuário, com o link:
   1. Abra https://eapbuild.com.br/user/integrations?ferramenta=claude-code&nome=Claude%20Code (entre na conta se pedir).
   2. Clique em **Criar token** e copie o token (começa com `eapb_` e aparece uma vez só).
   3. Rode `/plugin configure eapbuild@mcp`, cole o token e depois `/reload-plugins` (ou reinicie o Claude Code).

## Fluxo típico

1. `listar_projetos` com `busca` = parte do nome → pegue o `project_id`.
2. `ver_projeto` → panorama; `listar_pendencias` com `status=open` se houver pendências abertas.
3. `ver_eap` (com `fase`, se precisar) → localize os itens pelo código.
4. `editar_eap_com_ia` com um pedido claro, citando códigos.
5. Se vier `confirmacao_pendente`: mostre, pergunte, e só então `confirmar_alteracao`.
6. Para enviar ao cliente: `link_do_cliente`.

Orçamento novo: `novo_orcamento` → mande o `url_upload` ao usuário (as pranchas são enviadas no site, não pelo chat) → acompanhe com `listar_projetos` e `ver_projeto` até o `status` ficar pronto.
