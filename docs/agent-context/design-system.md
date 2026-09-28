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

- Raios: 18px (cards, painéis), 14–15px (`.box`, `.dom-badge`), 12px (`.ex`, `.mech`), 100px (chips, botões, pills).
- Sombra única: `0 1px 2px rgba(16,24,40,.04), 0 10px 24px -12px rgba(16,24,40,.10)` (cards). Hover de botão: `0 8px 18px -8px rgba(16,24,40,.18)` + `translateY(-1px)`.
- Responsivo por `flex-wrap` + `clamp()`; breakpoints explícitos só em `poster.css` (640px, 900px).

### Componentes — páginas de detalhe (`detail.css`)

`.wrap` · `.back-link` · `.dom-eyebrow` · `.dom-head` + `.dom-badge` (56px, SVG 30px) · `.dom-question` · `.sec` (`h2`, `h3`, `p`, `ul` com marcador quadrado em `--accent`) · `.box` + `.lbl` (fórmula/destaque em `--accent-tint`) · `.ex-row` / `.ex` / `.ex-lbl` / `.ex-res` (exemplos numéricos lado a lado) · `table` (header em `--accent-tint`) · `.principle-row` / `.principle-chip` · `.mech-grid` / `.mech` / `.mech-badge` / `.mech-num` / `.mech-anytime` / `.mech-title` (mecanismos em sequência, cor via `--accent`) · `.ex-row.io` + `.io-note` (Entrada e saída) · `.plain` + `.lbl` ("Em poucas palavras") · `ol.flow-steps` + `.fs-num` / `.fs-ico` (fluxo em passos; vira vertical ≤640px) · `.bars` / `.bar-row` / `.bar` / `.seg` (`.is-soft`, `.is-hatched`) / `.marker` / `.key` (visuais numéricos) · `.queue` / `.q-exits` / `.q-exit` (fila) · `.ex.audit` (registro de auditoria) · `.nav-footer` / `.nav-btn` / `.nav-btn.next`.

### Padrão de página didática (desde 28/09/2026; sem citações desde a rodada 3)

Público: pessoas que não conhecem o domínio. Pouco texto, muito visual. A fonte continua sendo `docs/business-domains.md`, mas a página **não cita** o doc.

1. `.back-link` → `.dom-eyebrow` com o mês (`Domínio 02 / 07 · Mês N-1`) → `.dom-head` → `.dom-question`.
2. Seção **Entrada e saída** (`<div class="sec" id="entrada-saida">`) logo depois da pergunta:
```html
<div class="sec" id="entrada-saida">
  <h2>Entrada e saída</h2>
  <div class="ex-row io">
    <div class="ex"><div class="ex-lbl">← Entrada · 01 Demand &amp; Forecast · Mês N-1</div><div class="ex-res">Forecast Customer–PN</div><div class="io-note">Forecast do PN × representatividade do cliente.</div></div>
    <div class="ex"><div class="ex-lbl">Saída → 03 Order Classification · Mês N0</div><div class="ex-res">Customer–PN Final Limit</div><div class="io-note">Limite do mês usado na avaliação de cada pedido.</div></div>
  </div>
</div>
```
3. Cada seção de regra tem **id estável** para links vindos do exemplo de ponta a ponta: `id="s4-2"` para §4.2, `id="s6"` para §6 (ponto vira hífen). Ordem: `h2` "§ · título" → `.plain` (2–4 frases curtas, palavras simples; decisão da revisão citada como "(revisão 20.x)") → tabela ou `.mech-grid` se houver → `.ex-row` com exemplos (do doc ou "Ilustrativo"); **quando o exemplo tem números, um visual substitui o `<code>` dentro do `.ex`**, mantendo `.ex-lbl` e `.ex-res` → `ol.flow-steps` quando houver sequência (`.fs-ico` com SVG no lugar de `.fs-num`, opcional).
4. Visuais: CSS puro ou SVG inline, sem JS; cores só de `--accent`, `--accent-tint`, `--bg`, `--line`, `--ink`; larguras por `style="--w:…"` e posições por `style="--x:…"`, em % da escala do próprio exemplo. Sólido = resultado, `.is-soft` = base/já consumido, `.is-hatched` = excedente (Abnormal). Para dividir um total entre partes iguais em status (ex.: clientes A e B), sólido e `.is-soft` só alternam as partes. Todo `.bar`/`.bars` tem `role="img"` + `aria-label` com os números; ícones e `.key` têm `aria-hidden="true"`.
5. "Esclarecido na revisão" (`.box`), "Princípios relacionados", `.nav-footer` — sem mudança.

Exemplo de seção (§7.1, barra de capacidade):
```html
<div class="sec" id="s7-1">
  <h2>7.1 · Exemplo</h2>
  <div class="plain">
    <div class="lbl">Em poucas palavras</div>
    <p>O que cabe no limite é Normal. O que passa é Abnormal.</p>
    <p>As duas partes continuam ligadas à linha original.</p>
  </div>
  <div class="ex-row">
    <div class="ex">
      <div class="ex-lbl">Exemplo do doc · excede o limite</div>
      <div class="bar" role="img" aria-label="Limite 20. Já no mês: 15. Pedido de 10: 5 Normal até o limite, 5 Abnormal acima.">
        <span class="seg is-soft" style="--w:60%">15</span>
        <span class="seg" style="--w:20%">5</span>
        <span class="seg is-hatched" style="--w:20%">5</span>
        <i class="marker to-left" style="--x:80%"><span>limite 20</span></i>
      </div>
      <div class="ex-res">5 Normal · 5 Abnormal</div>
    </div>
  </div>
  <div class="key" aria-hidden="true"><span><i class="is-soft"></i>Já no mês</span><span><i></i>Normal</span><span><i class="is-hatched"></i>Abnormal</span></div>
</div>
```

### Exemplo de ponta a ponta (`pages/exemplo-ponta-a-ponta.html`)

Um caso ilustrativo com números fixos. Cada passo é um `.sec id="passo-N"` com a cor do domínio via `style="--accent:…; --accent-tint:…"`, um `.plain` curto, um visual e uma linha `.e2e-links` com links para as seções das páginas (`01-demand-forecast.html#s3-1`…). **Se uma seção de página mudar de id, atualizar os links do exemplo.**

### Componentes — pôster (`poster.css`)

`#months` / `.month-group` / `.month-head` / `.month-tag` / `.month-sub` / `.month-row` / `.month-link` (grupos por mês N-1 · N0 · N+) · `.card` + `.card-head` / `.card-body` · `.domain-badge` · `.question` · `.step-tag` · `.card-sum` · `.card-more` · `.chips` / `.chip` · `.connector` (só setas) · `.gov-panel` · `.review-card` / `.review-status` · `.metric-panel` · `.principle-chip` · `.legend-row`. `.in-tag`, `.output-badge`, `.clabel` e afins só existem em `templates/template-draft.css` (usados pelo template visual).

### Ícones

SVG inline 24×24, um por domínio, idêntico entre o card do índice e o `.dom-badge` da página de detalhe.

## RECOMMENDATIONS

- Não criar novos tokens de cor; usar os da tabela.
- Componentes do pôster reaproveitados em páginas de detalhe devem ser portados para `detail.css` usando `var(--accent)` no lugar da cor fixa.
