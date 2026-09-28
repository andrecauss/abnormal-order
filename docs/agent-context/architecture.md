# Architecture

## CURRENT STATE

```
abnormal_order/
├── index.html                  pôster: header + lifecycle, 6 cards (#flow), #governance, #review, #metrics, #principles, #footer
├── pages/
│   └── NN-<slug>.html          uma página de detalhe por domínio (01–07)
├── css/
│   ├── base.css                reset + tokens globais (carregado por todas as páginas)
│   ├── poster.css              só index.html (cards, conectores, .mech, .scenario, governance, review…)
│   └── detail.css              só pages/*.html (.wrap, .dom-*, .sec, .box, .ex, tabela, .nav-footer)
├── templates/
│   ├── template_visual.html    referência visual original — NÃO editar
│   └── template-draft.css
├── docs/
│   ├── business-domains.md     fonte de verdade do negócio
│   ├── BRIEFING_claude_code.md especificação de conteúdo das páginas 02–07
│   ├── 01-architecture-overview.md  doc histórico (terminologia antiga)
│   └── agent-context/          memória compartilhada dos agentes
├── AGENTS.md                   regras para o Coder (Codex)
├── CLAUDE.md                   regras do Orchestrator (importa AGENTS.md)
└── .claude/agents/             subagentes: designer, architect, reviewer
```

### Convenções

- **Carregamento de CSS:** `index.html` → `base.css` + `poster.css`. `pages/*.html` → `../css/base.css` + `../css/detail.css`. Nenhuma página carrega os dois CSS específicos.
- **Tema por página de detalhe:** `<style>:root{ --accent:…; --accent-tint:…; }</style>` no `<head>`, sobrescrevendo o default azul de `detail.css`. A página 01 usa o default.
- **Nomes de arquivo:** `pages/NN-kebab-case.html`, NN = número do domínio.
- **Navegação:** `index → 01 → … → 07 → index` via `.nav-footer`; `.back-link` sempre para `../index.html`.
- **Âncoras do índice usadas por páginas:** `index.html#review`.
- Sem JS: todo comportamento é HTML/CSS (links, hover, responsividade via `flex-wrap` e `clamp()`).

## RECOMMENDATIONS

- Manter a divisão `poster.css` / `detail.css`. Componente de pôster necessário numa página de detalhe deve ser adicionado a `detail.css` com cor via `var(--accent)`, não importando `poster.css`.
