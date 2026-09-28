# AGENTS.md — Abnormal Order

Instruções para qualquer agente de código (Codex = Coder) que trabalhe neste repositório.

## O que é o projeto

Site estático de referência de negócio: HTML + CSS puros, sem JavaScript, sem build, sem package manager, sem testes automatizados. Abre direto do disco no browser.

Antes de qualquer mudança, leia `docs/agent-context/` (principalmente `architecture.md` e `design-system.md`). O conteúdo de negócio vem de `docs/business-domains.md` (source of truth), com os ajustes da revisão de 27/09 listados em `index.html#review`.

## Regras obrigatórias

1. **Existing project first.** O código atual é o baseline. Nada de redesign, nova arquitetura, framework, JS ou build.
2. **REUSE > EXTEND > CREATE > REPLACE.** Reuse classes de `css/detail.css` / `css/poster.css`. Se faltar algo, estenda o CSS existente. Crie só quando não existir. Substitua só com justificativa explícita.
3. **Escopo fechado.** Mexa apenas nos arquivos da tarefa. Nada de "while I'm here". Se precisar tocar outro arquivo, pare e explique o porquê.
4. **Não quebre o que existe.** Antes de editar uma área, entenda o comportamento atual e quem depende dela (links, classes compartilhadas entre páginas).
5. **Não mexa** em `templates/template_visual.html` nem em `docs/business-domains.md` (exceto notas de implementação explicitamente pedidas).
6. **Git:** não faça commit, push, reset, checkout ou clean. O Orchestrator cuida do Git.
7. Conteúdo em **pt-BR** com acentuação correta. Termos de domínio (Customer–PN, On Hold, Abnormal, Back Order…) ficam em inglês, como no doc.

## Padrão de página de detalhe (`pages/NN-*.html`)

- Siga o "Padrão de página didática" em `docs/agent-context/design-system.md` (Entrada e saída, regra do doc em `.rule`, `.plain`, exemplos, `.flow-steps`).
- Base estrutural: `pages/02-business-rules-quota.html` (head, `.wrap`, `.back-link`, `.dom-eyebrow`, `.dom-head` + `.dom-badge`, `.dom-question`, `.sec`, bloco "Esclarecido na revisão", "Princípios relacionados", `.nav-footer`).
- Por página muda: `--accent`/`--accent-tint` no `<style>` do head, SVG do badge (o mesmo do card correspondente em `index.html`), `Domínio NN / 07 · Mês X`, título, pergunta, seções, prev/next.
- Meses: N-1 = 01, 02 · N0 = 03, 04 · N+ = 05, 06 · 07 = todos os meses.
- Todo texto novo em UTF-8 com acentos (é, ã, ç, º, –, →). Texto sem acento é defeito.
- Links relativos: `../index.html`, `../css/...`, páginas vizinhas sem prefixo.
- Sequência de navegação: `index → 01 → 02 → 03 → 04 → 05 → 06 → 07 → index`.

Conteúdo de cada página: seções correspondentes de `docs/business-domains.md` + decisões da revisão (`index.html#review`) + notas de implementação no próprio doc. `docs/BRIEFING_claude_code.md` é histórico — não seguir.

## Índice × páginas

- **`index.html` é sempre resumido:** por domínio, só pergunta, step-tag, resumo de 1–2 frases e "Ver detalhes →". Nada de fórmulas, exemplos, listas de regras, input/output.
- **Página dedicada é sempre detalhada:** regra do doc, explicação simples, exemplos, fluxos, entrada e saída, esclarecimentos da revisão.
- Conteúdo novo vai para a página do domínio; o índice só ganha um resumo se o resumo atual deixar de ser verdadeiro.

## Como verificar

Sem suíte de testes. Verificação = todos os `href`/`src` locais resolvem para arquivos existentes + a página renderiza sem quebra em ~375px e ~1280px de largura.
