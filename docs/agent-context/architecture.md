# Architecture

## CURRENT STATE

```
abnormal_order/
├── index.html                  pôster: header + lifecycle, 6 cards em 3 grupos por mês (#months: N-1, N0, N+), #governance, #e2e (link para o exemplo), #review, #metrics, #principles, #footer
├── pages/
│   ├── NN-<slug>.html          uma página didática por domínio (01–07; 06 = 06-fulfillment.html), seções com id estável (s4-2…)
│   └── exemplo-ponta-a-ponta.html  um caso ilustrativo passando pelos 7 domínios, com links para as seções
├── css/
│   ├── base.css                reset + tokens globais (carregado por todas as páginas)
│   ├── poster.css              index.html e template (cards-resumo, conectores, governance, review…)
│   └── detail.css              só pages/*.html (.wrap, .dom-*, .sec, .box, .ex, .plain, .flow-steps, visuais .bar/.queue/.audit, .e2e-links, tabela, .nav-footer)
├── templates/
│   ├── template_visual.html    referência visual original — NÃO editar
│   └── template-draft.css      regras só do template (inclui as que saíram de poster.css)
├── docs/
│   ├── business-domains.md     fonte de verdade do negócio
│   ├── BRIEFING_claude_code.md histórico (briefing da rodada 0) — não usar como instrução
│   └── agent-context/          memória compartilhada dos agentes
├── AGENTS.md                   regras para o Coder (Codex)
├── CLAUDE.md                   regras do Orchestrator (importa AGENTS.md)
└── .claude/agents/             subagentes: designer, architect, reviewer
```

### Convenções

- **Carregamento de CSS:** `index.html` → `base.css` + `poster.css`. `templates/template_visual.html` → `base.css` + `poster.css` + `template-draft.css` (apagar regra de `poster.css` exige checar o template). `pages/*.html` → `../css/base.css` + `../css/detail.css`. Nenhuma página carrega os dois CSS específicos.
- **Tema por página de detalhe:** `<style>:root{ --accent:…; --accent-tint:…; }</style>` no `<head>`, sobrescrevendo o default azul de `detail.css`. A página 01 usa o default.
- **Nomes de arquivo:** `pages/NN-kebab-case.html`, NN = número do domínio.
- **Navegação:** `index → 01 → … → 07 → index` via `.nav-footer`; `.back-link` sempre para `../index.html`.
- **Âncoras do índice usadas por páginas:** `index.html#review`.
- Sem JS: todo comportamento é HTML/CSS (links, hover, responsividade via `flex-wrap` e `clamp()`).

## RECOMMENDATIONS

- Manter a divisão `poster.css` / `detail.css`. Componente de pôster necessário numa página de detalhe deve ser adicionado a `detail.css` com cor via `var(--accent)`, não importando `poster.css`.
