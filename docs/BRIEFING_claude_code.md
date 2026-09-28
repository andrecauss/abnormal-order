# Briefing — Páginas de detalhe do Abnormal Order

Cole este arquivo inteiro como prompt no Claude Code, dentro da pasta `abnormal_order/`.

## Contexto

Já existem nesta pasta:
- `template_visual.html` — template visual original de referência (não mexer)
- `abnormal_order_poster.html` — página índice (poster), já com os 6 cards de domínio + o painel de Governance & Audit virando links clicáveis (classe `.card`/`.gov-panel` como `<a href="...">`), apontando para os 7 arquivos abaixo
- `01-demand-forecast.html` — primeira página de detalhe, **já pronta e aprovada**. Use-a como o template/padrão exato de estrutura, CSS e tom para as próximas 6.
- `abnormal_order_business_domains.md` — o doc de origem com todo o conteúdo de negócio (se não estiver na pasta, cole o conteúdo aqui)

## Tarefa

Criar as 6 páginas de detalhe que faltam, seguindo **exatamente** o padrão estrutural, CSS e tom de `01-demand-forecast.html` (mesmo `:root`, mesma tipografia Inter + JetBrains Mono, mesmos componentes: `.back-link`, `.dom-eyebrow`, `.dom-head`/`.dom-badge`, `.dom-question`, `.sec` com `h2`/`h3`/`ul`/`.box` de fórmula, `.ex-row`/`.ex` para exemplos numéricos, `.principle-row`/`.principle-chip`, `.nav-footer` com prev/next).

Cada página troca apenas: `--accent` / `--accent-tint` (cor do domínio, igual ao card correspondente no poster), o ícone SVG do `.dom-badge` (mesmo SVG usado no card daquele domínio dentro de `abnormal_order_poster.html`), o número do domínio (`X / 7`), o título, a pergunta-guia, o conteúdo das seções, e os links prev/next no rodapé.

## Mapeamento arquivo → seção do doc → cor

| Arquivo | Domínio | Seções do .md | `--accent` | `--accent-tint` |
|---|---|---|---|---|
| `02-business-rules-quota.html` | Business Rules & Quota | 4.1, 4.2, 4.3, 4.4 | `#0C8A5E` (green) | `#E7F7F0` |
| `03-order-classification.html` | Order Classification | 5.1, 6, 7, 7.1, 8, 9 | `#7A3FE0` (purple) | `#F2ECFD` |
| `04-on-hold-management.html` | On Hold Management | 10.1, 10.2 | `#5E2FC9` (purple-2) | `#EFE8FC` |
| `05-release-management.html` | Release Management | 11, 12, 12.1, 12.2, 12.3, 13, 14, 15, 15.1, 15.2, 15.3 | `#DD5E14` (orange) | `#FDEEE3` |
| `06-fulfillment-back-order.html` | Fulfillment & Back Order | 16 | `#0E7C86` (teal) | `#E3F4F5` |
| `07-governance-audit.html` | Governance & Audit | 17.1, 17.2, 17.3 | `#3B4258` (slate) — usar `--ink-panel:#12172B` como fundo do `.dom-badge`, igual ao card | `#EEF0F4` |

## Regras de conteúdo por página

- **02 — Business Rules & Quota**: incluir a fórmula do Final Limit (`MAX(Forecast × (1+Upper Limit%), Min Qty)`) com os **3 exemplos numéricos do doc** (seção 4.2) em `.ex-row`/`.ex` — igual ao padrão de exemplo que você usaria em `01`. Incluir a tabela de estados Classification × Segregation (seção 4.3) como uma tabela HTML simples (reaproveitar paleta: header com `--accent-tint`). Fechar com um bloco "Esclarecido na revisão" referenciando o item **20.5** (alteração de regra durante a competência não tem trava automática — link `index.html#review`).
- **03 — Order Classification**: é a mais densa. Cobrir: tipos de pedido (Regular/Urgente) e por que Urgente consome mas nunca é Abnormal; a chave conceitual `Customer + PN + Competência`; o exemplo numérico da seção 7.1 (limite 20, acumulado 15, pedido 10 → 5/5) em `.ex`; a lista de eventos que **não** removem a classificação Abnormal (seção 8); e a seção de Cancelamentos (seção 9) como subseção própria (linha mantida integralmente ou cancelada integralmente, sem cancelamento parcial).
- **04 — On Hold Management**: fila global por PN (todos clientes/segmentos/canais juntos) e a chave FIFO = Nº da Ordem de Venda + Linha. Fechar com "Esclarecido na revisão" referenciando **20.6** (decisão da chave FIFO).
- **05 — Release Management**: a mais longa. Estruturar como 4 subseções dentro da página (uma por mecanismo: Monthly Reconciliation, Inventory Release, Manual Release, Planning Lead Time Release), cada uma com sua sequência (1/2/3/"a qualquer momento") destacada visualmente como no `.mech` do poster — pode reaproveitar essa classe. Incluir os exemplos da seção 15.1 (início da contagem) e 15.2 (LT vigente muda de 120→60 dias). Fechar com "Esclarecido na revisão" referenciando **20.2**, **20.3** e **20.4**.
- **06 — Fulfillment & Back Order**: mais curta — fluxo alocação→separação→expedição→entrega, e a distinção conceitual On Hold vs. Back Order (usar `.ex-row` com 2 `.ex` lado a lado comparando as duas definições).
- **07 — Governance & Audit**: Permissões (17.1) e Auditoria (17.2, com o exemplo de campos rastreados: override manual, usuário, data/hora, valor original→alterado) e Uploads (17.3). Fechar com "Esclarecido na revisão" referenciando **20.1** (concorrência — fechado como fato, sem lock necessário) e **20.4** (autoridade do Manual Release é absoluta, governança só via auditoria).

## Padrão do bloco "Esclarecido na revisão"

Onde relevante, adicionar ao final da página (antes do `.nav-footer`) uma seção assim, reaproveitando o estilo `.box` já definido:

```html
<div class="sec">
  <h2>Esclarecido na revisão de 27/09</h2>
  <div class="box">
    <div class="lbl">Seção 20.X do doc</div>
    <p><b>Pergunta:</b> [pergunta resumida]</p>
    <p><b>Decisão:</b> [decisão resumida]</p>
  </div>
  <p><a href="index.html#review" style="color:var(--accent); font-weight:700;">Ver todos os esclarecimentos da revisão →</a></p>
</div>
```

## Navegação (rodapé)

Sequência fixa para os links prev/next: `index → 01 → 02 → 03 → 04 → 05 → 06 → 07 → index`.
Na página `07`, o botão "next" deve voltar para `index.html` (texto "Voltar à Visão Geral") em vez de apontar para uma página 08 inexistente.

## Depois de criar as 6 páginas

1. Conferir que todos os `href` do `abnormal_order_poster.html` resolvem para arquivos existentes.
2. Abrir cada página num browser local rápido pra checar quebras de layout (os `.mech`/`.ex-row` em telas estreitas).
3. Não mexer em `template_visual.html`.
