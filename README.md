# Corporal Class — Design System

Design system da **Corporal Class** (assinatura de estética corporal da Corporal Academy). Marca alinhada ao brandbook Corporal Academy (§3.6); cor predominante **bordô `#D6515C`** (pantone 186 U); tipografia **Silka** (embutida em woff2).

Desenvolvido por **Edegar Junior**.

## Entregas

- **index.html** — design system reutilizável (showcase navegável): paleta (institucional + derivada), temas **dark/light**, tipografia (Silka), **gradientes**, **ícones** (Phosphor Thin, copiar SVG), **logos** (copiar/baixar SVG) e sistema de **botões** (variantes/tamanhos/estados). Click-to-copy em cores, valores e código; download PNG dos gradientes.
  - 🌐 **Online (para compartilhar):** https://edegardesignacademy-hash.github.io/corporal-class-design-system/ — GitHub Pages (repo público `corporal-class-design-system`).

## Design System portátil (`design-system/`)

Pacote para aplicar a marca em **qualquer projeto/ferramenta** (web, React, Framer, agentes de IA).

- **silka.css** — fonte **Silka** (pesos 300–700) embutida em woff2/base64, self-contained; linke antes do CSS principal.
- **corporal-design-system.css** — drop-in (tokens dark/light + reset + foco + motion + tipografia + botões + chips/badges/status).
- **corporal-design-tokens.json** — tokens legíveis por máquina (Style Dictionary, Framer, IA).
- **Button.tsx** — Code Component Framer/React com Property Controls.
- **DESIGN-SYSTEM.md** — spec completa, 3 formas de aplicar e **prompt pronto para IA**.
- **THEME.md** — como o claro/escuro é configurado e ativado pelo tema do sistema do visitante (web + Framer).

## Notas técnicas

- **Cores:** derivadas das **5 cores institucionais** da Corporal Academy (brandbook §3.6): bordô `#D6515C`, vermelho claro `#FFB1BD`, amarelado `#FFE4A4`, branco `#FFFFFF`, preto `#000000`. Acento derivado coral `#E88A92`. Detalhe no `index.html`.
- **Tipografia:** Silka (institucional), embutida em base64/woff2; Poppins como fallback (quando a Silka não estiver disponível), depois system-ui.
- **Ícones:** biblioteca **Phosphor**, peso **Thin** (stroke 1pt na grade 24), `currentColor`.
- **Tema:** dark por padrão; light via `data-theme="light"`; sem atributo segue `prefers-color-scheme`. Toggle persiste em `cc-theme`.

## Publicação

Repo público `corporal-class-design-system` (conta `edegardesignacademy-hash`), branch `main`, `index.html` na raiz, GitHub Pages. `.git` fora do OneDrive (`AppData\Local\gitdirs\`); line-endings LF (`.gitattributes`). Deploy: editar → `git add/commit/push` (credencial no Cofre do Windows, sem token). Ver `HANDOFF.md`.
