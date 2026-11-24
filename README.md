# eapbuild no seu assistente de IA

[eapbuild](https://eapbuild.com.br) monta o orçamento de obra (EAP) a partir das pranchas do projeto. Este repositório tem o **plugin do Claude Code** e a configuração do **servidor MCP** do eapbuild, para usar o produto direto do assistente: listar e abrir projetos, ler a EAP e as pendências, responder pendências, editar a EAP com a IA do eapbuild (mudanças grandes pedem sua confirmação), gerar o link do cliente com PDF e planilha, e começar um orçamento novo.

Aqui só há configuração do cliente, sem segredo: o servidor MCP roda em `https://eapbuild.com.br/api/v1/mcp` e age como você, com as mesmas regras do app.

## Instalar no Claude Code

```
/plugin marketplace add eapbuild/mcp
/plugin install eapbuild@mcp
```

Sem git, pelo site: `/plugin marketplace add https://eapbuild.com.br/claude/marketplace.json`.

No primeiro uso, rode `/mcp` → eapbuild → **Autenticar**: o navegador abre, você entra no eapbuild e clica em **Autorizar**. Não precisa copiar token. Versões novas: `/plugin marketplace update mcp`.

**Sem o plugin**, só o servidor MCP (mesmo login no navegador):

```
claude mcp add --transport http eapbuild https://eapbuild.com.br/api/v1/mcp
```

**Prefere token?** Crie em [Minha conta › Integrações com IA](https://eapbuild.com.br/user/integrations?ferramenta=claude-code&nome=Claude%20Code) e conecte com `claude mcp add --transport http eapbuild https://eapbuild.com.br/api/v1/mcp --header "Authorization: Bearer eapb_..."`.

## Outras ferramentas

claude.ai e Claude Desktop, ChatGPT e Codex, Cursor, VS Code / Copilot e OMP: passo a passo em **[eapbuild.com.br/integracoes](https://eapbuild.com.br/integracoes)**.

## Ferramentas

| Ferramenta | O que faz |
|---|---|
| `listar_projetos` | Lista seus projetos (filtros: status, busca). |
| `ver_projeto` | Dados, status, disciplinas, contagem de itens e totais. |
| `ver_eap` | Itens da EAP por fase, com quantidades, preços e pendentes. |
| `listar_pendencias` | Pendências do projeto. |
| `responder_pendencia` | Registra a sua resposta a uma pendência. |
| `editar_eap_com_ia` | Edita a EAP com a IA do eapbuild a partir de um pedido em texto. |
| `confirmar_alteracao` | Aplica ou descarta uma alteração que pediu confirmação. |
| `link_do_cliente` | Link do cliente com PDF e planilha, só com o preço ao cliente. |
| `novo_orcamento` | Link para enviar as pranchas de um orçamento novo. |

A skill em `skills/eapbuild/SKILL.md` ensina o assistente a ler antes de alterar, a perguntar antes de confirmar mudanças e a nunca mandar custo ou BDI ao cliente.

## Segurança

- O login no navegador (ou o token) age como você: o assistente só vê os seus projetos.
- Nenhum segredo fica neste repositório: o acesso é emitido pelo eapbuild no login e guardado pelo Claude Code.
- Desconecte quando quiser em Minha conta › Integrações com IA.

## English

Claude Code plugin and MCP configuration for [eapbuild](https://eapbuild.com.br), a Brazilian construction budgeting app (EAP/WBS from project drawings). Install with `/plugin marketplace add eapbuild/mcp` and `/plugin install eapbuild@mcp`; on first use, run `/mcp` → eapbuild → Authenticate to sign in with OAuth in the browser (no token to paste). A personal API token still works with `claude mcp add --header`. The server acts as the signed-in user with the app's own permissions.

## Licença

[MIT](LICENSE)
