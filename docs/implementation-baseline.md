# Implementation Baseline — Abnormal Order

Confronto entre `business-domains.md` (source of truth de negócio) e a implementação atual do site (`index.html`, `pages/`, `css/`). Atualizar este arquivo a cada rodada do time multiagente, não recriar do zero.

Escopo: site é referência estática (HTML/CSS, sem backend). "IMPLEMENTED" = conteúdo representado corretamente na página, não comportamento de sistema real.

## Status por domínio

| # | Domínio | Onde vive hoje | Status | Observação |
|---|---|---|---|---|
| 1 | Demand & Forecast | `index.html` card1 (resumo) + `pages/01-demand-forecast.html` (completo) | IMPLEMENTED | Página 01 cobre doc §3.1–3.5 quase verbatim. Referência de padrão para as demais. |
| 2 | Business Rules & Quota | `index.html` card2 (resumo) | PARTIALLY_IMPLEMENTED | Fórmula + flags OK. Faltam os 3 exemplos numéricos (§4.2) e a tabela de estados (§4.3) em HTML. `pages/02-business-rules-quota.html` não existe. |
| 3 | Order Classification | `index.html` card3 (resumo) | PARTIALLY_IMPLEMENTED | Fórmula/exemplo OK. Faltam: distinção Regular×Urgente (§5.1), regra de Cancelamentos (§9), nuance "atingir o limite exato não é anormalidade" (§7). `pages/03-order-classification.html` não existe. |
| 4 | On Hold Management | `index.html` card4 (resumo) | IMPLEMENTED_DIFFERENTLY | Doc §10.2 diz FIFO por data/hora original. Site já mudou para Nº da Ordem de Venda + Linha — decisão registrada em `index.html#review` item 20.6. Divergência intencional; doc está desatualizado nesse ponto (nota já adicionada em `business-domains.md`). `pages/04-on-hold-management.html` não existe. |
| 5 | Release Management | `index.html` card5 (resumo) | IMPLEMENTED_DIFFERENTLY | Doc §15.2 diz verificação do Planning LT é diária. Site mudou para mensal — registrado em `index.html#review` item 20.3. Mesma situação: proposital, documentada. `pages/05-release-management.html` não existe (faltam os 4 mecanismos detalhados + exemplos §15.1/§15.2). |
| 6 | Fulfillment & Back Order | `index.html` card6 (resumo) | IMPLEMENTED (nível resumo) | Bate com doc §16. `pages/06-fulfillment-back-order.html` (comparação On Hold × Back Order) não existe. |
| 7 | Governance & Audit | `index.html` `#governance` | PARTIALLY_IMPLEMENTED | §17.1 (Permissões) e §17.2 (Auditoria) cobertos. §17.3 (reaproveitamento do modelo de upload) não aparece. `pages/07-governance-audit.html` não existe. |
| — | Estados conceituais (§18, com CANCELLED) | `index.html` `#lifecycle` (ribbon simplificado) | PARTIALLY_IMPLEMENTED / UNCLEAR | Ribbon mostra só 4 passos do caminho feliz; não representa a saída paralela ON HOLD → CANCELLED. Confirmar se é resumo intencional. |
| — | Princípios centrais (§19, 16 itens) | `index.html` `#principles` (7 chips) | PARTIALLY_IMPLEMENTED | Só 7 dos 16 princípios do doc aparecem como chip. |
| — | Navegação clicável poster → páginas de detalhe | `index.html` cards / `.gov-panel` | NOT_IMPLEMENTED | `docs/BRIEFING_claude_code.md` assume que os cards já são `<a href>`. São `<div>` estáticos hoje. Fazer junto da criação das páginas 02–07. |

## Divergências reais registradas (doc desatualizado, não a implementação)

1. **FIFO key** — doc §10.2 (timestamp) vs. site/review 20.6 (Nº Ordem + Linha).
2. **Cadência de checagem do Planning LT** — doc §15.2 (diária) vs. site/review 20.3 (mensal).

## Backlog de build (ordem)

- [x] `pages/02-business-rules-quota.html` — escrita direto por Claude (Orchestrator), não pelo Codex: o job do Codex ficou preso 34min+ e foi morto por pressão de memória do sistema antes de escrever o arquivo. Pipeline Architect→Codex→Reviewer não foi validado nesta rodada.
- [ ] `pages/03-order-classification.html`
- [ ] `pages/04-on-hold-management.html`
- [ ] `pages/05-release-management.html`
- [ ] `pages/06-fulfillment-back-order.html`
- [ ] `pages/07-governance-audit.html`
- [ ] Cards + `.gov-panel` de `index.html` virarem `<a href>` pras páginas acima
- [ ] Revisar `#principles`/`#lifecycle` quanto à cobertura de §18/§19 (decidir com o usuário se expande)

Mapeamento arquivo → seções do doc → cor: ver `docs/BRIEFING_claude_code.md`.
