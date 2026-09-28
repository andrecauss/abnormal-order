# Features — status por domínio

Confronto entre `docs/business-domains.md` (source of truth de negócio) e o site (`index.html`, `pages/`, `css/`). Atualizar a cada rodada do time multiagente; não recriar do zero.

Escopo: site é referência estática (HTML/CSS, sem backend). "IMPLEMENTED" = conteúdo representado corretamente na página, não comportamento de sistema real.

Taxonomia: `IMPLEMENTED` · `PARTIALLY_IMPLEMENTED` · `IMPLEMENTED_DIFFERENTLY` (divergência intencional do doc, registrada na revisão) · `PLACEHOLDER` · `BROKEN_OR_INCOMPLETE`.

## CURRENT STATE (27/09/2026, fim da rodada 1)

O índice é uma visão geral: cada card traz pergunta, input, resumo de 1–2 frases, output e link "Ver detalhes →" para a página do domínio. Todo o detalhe vive nas páginas.

| # | Domínio | Página | Status | Observação |
|---|---|---|---|---|
| 1 | Demand & Forecast | `pages/01-demand-forecast.html` | IMPLEMENTED | §3.1–3.5. |
| 2 | Business Rules & Quota | `pages/02-business-rules-quota.html` | IMPLEMENTED | §4.1–4.4, 3 exemplos numéricos, tabela de flags, revisão 20.5. |
| 3 | Order Classification | `pages/03-order-classification.html` | IMPLEMENTED | §5.1, 6, 7, 7.1, 8, 9 (Regular × Urgente, cancelamentos, "atingir o limite não é anormal"). |
| 4 | On Hold Management | `pages/04-on-hold-management.html` | IMPLEMENTED_DIFFERENTLY | FIFO = Nº da Ordem de Venda + Linha (revisão 20.6), não timestamp como no doc §10.2. |
| 5 | Release Management | `pages/05-release-management.html` | IMPLEMENTED_DIFFERENTLY | 4 mecanismos + exemplos §15.1/§15.2. Checagem do Planning LT é mensal (revisão 20.3), não diária como no doc §15.2. Revisões 20.2–20.4. |
| 6 | Fulfillment & Back Order | `pages/06-fulfillment-back-order.html` | IMPLEMENTED | §16 + comparação On Hold × Back Order. |
| 7 | Governance & Audit | `pages/07-governance-audit.html` | IMPLEMENTED | §17.1–17.3, revisões 20.1 e 20.4. |
| — | Navegação índice → páginas | `index.html` (cards e `.gov-panel` são `<a>`) | IMPLEMENTED | Sequência prev/next `index → 01 → … → 07 → index`. |
| — | Estados conceituais (§18, com CANCELLED) | `index.html#lifecycle` | PARTIALLY_IMPLEMENTED | Ribbon mostra só o caminho feliz; sem ON HOLD → CANCELLED. Aguarda decisão do usuário. |
| — | Princípios centrais (§19, 16 itens) | `index.html#principles` + chips nas páginas | PARTIALLY_IMPLEMENTED | Índice mostra 7 de 16; os demais aparecem distribuídos em "Princípios relacionados" das páginas. |
| — | Esclarecimentos da revisão 20.1–20.6 | `index.html#review` | IMPLEMENTED | Lista canônica; páginas 02, 04, 05, 07 linkam para cá. |
| — | Indicadores | `index.html#metrics` | IMPLEMENTED | Exemplos, sem fonte no doc de negócio. |

## Divergências doc × site (intencionais)

1. **FIFO key** — doc §10.2 (timestamp) vs. site/revisão 20.6 (Nº Ordem de Venda + Linha).
2. **Cadência do Planning LT** — doc §15.2 (diária) vs. site/revisão 20.3 (mensal).
