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

- Copie a estrutura de `pages/02-business-rules-quota.html` (head, `.wrap`, `.back-link`, `.dom-eyebrow`, `.dom-head` + `.dom-badge`, `.dom-question`, `.sec`, bloco "Esclarecido na revisão", "Princípios relacionados", `.nav-footer`).
- Troque só: `--accent`/`--accent-tint` no `<style>` do head, SVG do badge (o mesmo do card correspondente em `index.html`), `Domínio NN / 07`, título, pergunta, seções, prev/next.
- Links relativos: `../index.html`, `../css/...`, páginas vizinhas sem prefixo.
- Sequência de navegação: `index → 01 → 02 → 03 → 04 → 05 → 06 → 07 → index`.

Especificação de conteúdo por página: `docs/BRIEFING_claude_code.md` (os nomes de arquivo antigos nele — `abnormal_order_poster.html`, `abnormal_order_business_domains.md` — hoje são `index.html` e `docs/business-domains.md`).

## Como verificar

Sem suíte de testes. Verificação = todos os `href`/`src` locais resolvem para arquivos existentes + a página renderiza sem quebra em ~375px e ~1280px de largura.
