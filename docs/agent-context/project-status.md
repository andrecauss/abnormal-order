# Project Status

## CURRENT STATE (28/09/2026)

Rodada 2 concluída: índice agrupado por mês (N-1 / N0 / N+ + Governance transversal), input/output movidos para as páginas, as 7 páginas reescritas no padrão didático (regra do doc + "Em poucas palavras" + exemplos + fluxo), e "Fulfillment & Back Order" renomeado para Fulfillment (estado final Released). Branch `20260927` publicada como `main`.

### Log da rodada 2

| Item | Quem implementou | Review |
|---|---|---|
| Spec: grupos por mês + componentes didáticos | Designer | — |
| Rename Fulfillment (arquivo, links, nota no doc §16) | Orchestrator | Revisão do índice |
| Componentes `.rule`, `.plain`, `.flow-steps`, `.io` em `detail.css` | Orchestrator (CSS do Designer) | — |
| Índice por mês; CSS de input/output movido para `template-draft.css` | Codex | Aprovado |
| Página 01 | Codex | Aprovado (lista do §3.4 restaurada literal) |
| Página 02 | Codex | Reprovado 1× (lista do §4.4 achatada) → corrigido |
| Página 03 | Codex | Reprovado 1× (gíria, fluxo inventado no §9) → corrigido |
| Página 04 | Codex | Aprovado (3 ajustes menores de texto) |
| Página 05 | Codex | Reprovado 1× (timestamp e "diariamente" como regra atual) → corrigido; "§" trocado por "parágrafo" pelo Codex → normalizado |
| Página 06 Fulfillment | Codex | Aprovado; resumo da 05 que citava Back Order como estado ajustado |
| Página 07 | Codex | Aprovado na revisão final (3 ajustes: lista literal do §17.2, uploads, "antes do upload") |
| `.rule code` sem margem inferior colava no parágrafo seguinte | Orchestrator | Verificado no browser |
| Revisão final de consistência (meses, cadeia entrada/saída, prev/next, 20.3/20.6, Back Order) | Reviewer | Aprovado |

Padrão de reprovação recorrente do Codex: achatar listas do doc em prosa, inventar sequência em fluxos, texto coloquial, manter regra superada pela revisão como se valesse. Os prompts passaram a listar essas lições explicitamente.

### Log da rodada 1

| Item | Quem implementou | Review |
|---|---|---|
| Infra: `AGENTS.md`, `CLAUDE.md`, `.claude/agents/`, `docs/agent-context/` | Orchestrator | — |
| `pages/03-order-classification.html` | Codex (~2,5 min) | Reprovado 1× (exemplo fora do doc) → corrigido pelo Orchestrator; política "Ilustrativo" criada |
| `pages/04-on-hold-management.html` | Codex | Aprovado |
| `.mech-*` portado para `detail.css` | Orchestrator | Aprovado junto da 05 |
| `pages/05-release-management.html` | Codex | Aprovado |
| `pages/06-fulfillment-back-order.html` | Codex | Aprovado (ajuste menor: "O fluxo pode incluir") |
| `pages/07-governance-audit.html` | Codex | Aprovado (ajuste menor: "matriz" removido) |
| Cards do índice → `<a href>` | Orchestrator | Revisão final |
| Índice resumido (pedido do usuário) | Designer (spec) → Codex | Codex gravou resumos sem acento; corrigido pelo Orchestrator |
| Tabela da 02 estourando 12px em 375px (pré-existente) | Orchestrator | Revisão final |
| Revisão final reprovou: CSS apagado de `poster.css` ainda era usado por `templates/template_visual.html` → regras movidas para `templates/template-draft.css` | Orchestrator | Verificado no browser |

Pipeline Architect/Designer → Codex → Reviewer validado: cada job Codex de uma página levou ~2,5 min sem travar. O Architect não foi acionado (nenhuma mudança estrutural).

### Verificação

Sem suíte de testes. Rodar `python -m http.server 8765` na raiz (ou a config `site` de `.claude/launch.json`) e conferir no browser. Na rodada 1: todos os `href` locais e âncoras resolvem nas 8 páginas; sem overflow horizontal em 375px.

## Backlog

- [ ] Decidir com o usuário: `#lifecycle` deve mostrar a saída ON HOLD → CANCELLED (§18)?
- [ ] Decidir com o usuário: `#principles` deve listar os 16 princípios de §19 ou manter os 7?
- [ ] Atualizar `docs/BRIEFING_claude_code.md` ou marcá-lo como histórico (nomes de arquivo antigos).
