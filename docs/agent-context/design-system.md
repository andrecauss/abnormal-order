# Design System (extraído do código)

## CURRENT STATE

### Tokens globais (`css/base.css`)

| Token | Valor | Uso |
|---|---|---|
| `--bg` | `#EEF1F6` | fundo da página |
| `--surface` | `#FFFFFF` | cards |
| `--ink` | `#0F1729` | texto principal |
| `--muted` | `#5B6478` | texto secundário, eyebrows |
| `--line` | `#E2E6EE` | bordas, divisores |
| `--ink-panel` | `#12172B` | painéis escuros (governance, chips de princípio, qbar) |

### Paleta por domínio (`css/poster.css`; nas páginas vira `--accent`/`--accent-tint`)

| Domínio | Cor | Tint | Classe do card |
|---|---|---|---|
| 01 Demand & Forecast | `#2456E0` blue | `#EBF0FE` | `.t-blue` |
| 02 Business Rules & Quota | `#0C8A5E` green | `#E7F7F0` | `.t-green` |
| 03 Order Classification | `#7A3FE0` purple | `#F2ECFD` | `.t-purple` |
| 04 On Hold Management | `#5E2FC9` purple-2 | `#EFE8FC` | `.t-purple2` |
| 05 Release Management | `#DD5E14` orange | `#FDEEE3` | `.t-orange` |
| 06 Fulfillment & Back Order | `#0E7C86` teal | `#E3F4F5` | `.t-teal` |
| 07 Governance & Audit | `#3B4258` slate (badge em `--ink-panel`) | `#EEF0F4` | `.gov-panel` |
| alerta | `#C4321E` red | `#FBEAE7` | `.s-red` |

### Tipografia

- **Inter** 400–900: corpo e títulos. `h1` da página de detalhe 900, `clamp(24px,3.4vw,38px)`.
- **JetBrains Mono** 400–700: eyebrows, `h2` de seção (uppercase, letter-spacing .13em), números, fórmulas (`code`).
- Pergunta-guia (`.dom-question`, `.question`): itálico, `--muted`, borda esquerda 3px.

### Forma

- Raios: 18px (cards, painéis), 14–15px (`.box`, `.dom-badge`), 12px (`.ex`, `.mech`, `.scenario`), 100px (chips, botões, pills).
- Sombra única: `0 1px 2px rgba(16,24,40,.04), 0 10px 24px -12px rgba(16,24,40,.10)` (cards). Hover de botão: `0 8px 18px -8px rgba(16,24,40,.18)` + `translateY(-1px)`.
- Responsivo por `flex-wrap` + `clamp()`; breakpoints explícitos só em `poster.css` (640px, 900px).

### Componentes — páginas de detalhe (`detail.css`)

`.wrap` · `.back-link` · `.dom-eyebrow` · `.dom-head` + `.dom-badge` (56px, SVG 30px) · `.dom-question` · `.sec` (`h2`, `h3`, `p`, `ul` com marcador quadrado em `--accent`) · `.box` + `.lbl` (fórmula/destaque em `--accent-tint`) · `.ex-row` / `.ex` / `.ex-lbl` / `.ex-res` (exemplos numéricos lado a lado) · `table` (header em `--accent-tint`) · `.principle-row` / `.principle-chip` · `.nav-footer` / `.nav-btn` / `.nav-btn.next`.

### Componentes — pôster (`poster.css`)

`.card` + `.card-head` / `.card-body` · `.domain-badge` · `.question` · `.step-tag` · `.in-tag` · `ul.activities` · `.tinted-box` · `.chips` / `.chip` · `.output-badge` (`.unconstrained` / `.constraint-badge`) · `.connector` · `.mech-grid` / `.mech` / `.mech-anytime` · `.balance-banner` · `.scenario-row` / `.scenario` (`.s-green`, `.s-red`) · `.gov-panel` / `.gov-col` · `.review-card` / `.review-status` · `.metric-panel` · `.principle-chip` · `.legend-row`.

### Ícones

SVG inline 24×24, um por domínio, idêntico entre o card do índice e o `.dom-badge` da página de detalhe.

## RECOMMENDATIONS

- Não criar novos tokens de cor; usar os da tabela.
- Componentes do pôster reaproveitados em páginas de detalhe devem ser portados para `detail.css` usando `var(--accent)` no lugar da cor fixa.
