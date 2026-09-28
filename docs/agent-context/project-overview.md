# Project Overview

## CURRENT STATE

**Abnormal Order — Business Domains Reference** é um site estático que documenta, de forma visual, o domínio de negócio do Abnormal Order: identificar pedidos acima do comportamento esperado por Customer–PN, classificar permanentemente a parcela excedente (Abnormal), segregá-la (On Hold) e liberá-la por quatro mecanismos.

- **Público:** times de negócio e de sistemas que precisam de uma referência única das regras.
- **Formato:** uma página-pôster (`index.html`) com os 7 domínios em cards + páginas de detalhe por domínio (`pages/NN-*.html`).
- **Fonte de verdade do conteúdo:** `docs/business-domains.md`. Decisões posteriores da revisão de 27/09/2026 estão em `index.html#review` (itens 20.1–20.6) e prevalecem sobre o doc onde divergem.
- **Doc histórico:** `docs/01-architecture-overview.md` (04/09/2026) usa terminologia anterior (Empresa–Material, Cliente–Material). Não é a referência atual do site.

## Stack

| Item | Valor |
|---|---|
| Linguagem | HTML5 + CSS3 |
| Framework / UI lib | nenhum |
| JavaScript | nenhum |
| Package manager / build | nenhum |
| Fontes | Google Fonts CDN: Inter, JetBrains Mono |
| Backend / DB / Auth | nenhum |
| Testes | nenhum (verificação manual no browser) |
| Hospedagem | arquivo local; repo `andrecauss/abnormal-order` (privado) |

## Os 7 domínios

1. Demand & Forecast
2. Business Rules & Quota
3. Order Classification
4. On Hold Management
5. Release Management
6. Fulfillment & Back Order
7. Governance & Audit (transversal)
