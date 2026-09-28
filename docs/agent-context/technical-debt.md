# Technical Debt & Observações

## CURRENT STATE

1. **Home do usuário também é repo Git.** `C:\Users\andre` tem um `.git` próprio (outro projeto) que enxerga o sistema inteiro como untracked. Este projeto tem repo próprio em `abnormal_order/` desde 27/09/2026. Rodar Git sempre de dentro de `abnormal_order/`.
2. **Memória da máquina.** ~16 GB total, ~2 GB livres em uso normal. Um job Codex em lote (páginas 02–07) ficou 34+ min preso e foi morto por pressão de memória. Regra: um job Codex por arquivo (na rodada 1, cada página levou ~2,5 min).
2a. **Codex pode perder acentos.** Ao editar um arquivo existente (`index.html`), o Codex gravou os textos novos sem acento ("media movel", "No da Ordem"). Nas páginas criadas do zero isso não aconteceu. O Reviewer deve procurar palavras sem acento em todo texto novo.
3. **`docs/BRIEFING_claude_code.md` usa nomes antigos**: `abnormal_order_poster.html` = `index.html`; `abnormal_order_business_domains.md` = `docs/business-domains.md`; arquivos citados na raiz hoje vivem em `pages/`, `templates/`, `docs/`.
4. **Divergências doc × site (intencionais):** FIFO key (doc §10.2 timestamp → site Nº Ordem de Venda + Linha, revisão 20.6) e cadência do Planning LT (doc §15.2 diária → site mensal, revisão 20.3). O doc tem nota apontando para isso.
5. **`#principles` do índice** mostra 7 dos 16 princípios de §19; **`#lifecycle`** mostra só o caminho feliz (sem ON HOLD → CANCELLED). Pendente decisão do usuário se é resumo intencional.
6. **Sem verificação automatizada.** Links e layout conferidos manualmente no browser via servidor local (`.claude/launch.json`). Abrir por `file://` no painel de preview do app não carrega o CSS.
7. **`docs/01-architecture-overview.md`** usa terminologia anterior (Empresa–Material). Pode confundir agentes; não é referência do site.

## RECOMMENDATIONS

- Item 5: decidir com o usuário antes de expandir.
