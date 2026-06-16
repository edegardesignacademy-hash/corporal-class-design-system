# Changelog — Corporal Class Design System

Todas as mudanças relevantes deste design system são registradas aqui.
O formato segue [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/) e o
versionamento segue [SemVer](https://semver.org/lang/pt-BR/):

- **MAJOR** — muda ou remove um token/API público (quebra compatibilidade).
- **MINOR** — adiciona de forma retrocompatível (novo componente/token/variante).
- **PATCH** — correções que não mudam a API (bug, contraste, ajuste fino).

---

## [Não lançado]

_Nada pendente no momento._

## [1.1.0] — 2026-06-16
### Adicionado
- Navegação por categorias (Marca · Componentes · Sistema) com menus suspensos, scrollspy e logo da marca.
### Alterado
- Conteúdo reordenado: parte visual primeiro, tokens no fim.
- Dark mode: elevação por superfície (card mais claro que o fundo); removido o brilho colorido das sombras.
- Cantos arredondados corrigidos no card com mídia.
- Copy revisada (menos estrangeirismos desnecessários).

## [1.0.0] — 2026-06-16

Primeira versão consolidada e documentada do sistema. Reúne fundações, camada
de produto e camada de maturidade/processo.

### Fundações
- Arquitetura de tokens em 3 camadas (`primitive → semantic/intent → component`).
- Cores, gradientes e tipografia (Silka, com fallback `system-ui`) derivadas do brandbook.
- Theming **dark/light** com paridade total e contraste **WCAG AA** em todos os textos.
- Numerais com `tabular-nums` global.
- Tokens de fundação: opacidade, border-width, blur, breakpoints/grid, elevação
  semântica (surface → modal), sizing/touch ≥ 44px e aspect-ratio.

### Produto
- **Forms:** input, textarea, select, checkbox/radio/toggle, helper, erro e sucesso
  inline + matriz canônica de estados (default/focus/filled/disabled/error/success/read-only).
- **Feedback:** alert/banner inline, toast, spinner, skeleton e empty state.
- **Overlays:** modal/dialog, tooltip e popover.
- **Estrutura:** tabs, accordion, avatar (+ grupo e status), breadcrumb, paginação
  e variantes de card (básico, interativo, mídia, horizontal).

### Maturidade & processo
- Seção "Princípios & processo": arquitetura de tokens, princípios de motion e do/don't.
- Este `CHANGELOG.md` e o `CONTRIBUTING.md` (governança, regra das 3 equipes, SemVer).
- Menu de navegação fixo com scrollspy no showcase + respiro de layout revisado.

[Não lançado]: #não-lançado
[1.0.0]: #100--2026-06-16
