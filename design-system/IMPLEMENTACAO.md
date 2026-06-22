# Implementação — Corporal Class Design System

Documentação técnica de **como cada item é implementado**, para subir em qualquer infra (HTML/CSS puro, qualquer stack via tokens, ou Framer). Tudo aqui reflete o que está no showcase publicado (`index.html`) e no pacote `design-system/`. Em caso de divergência, **o showcase + `corporal-design-tokens.json` são a fonte da verdade**; o `corporal-design-system.css` (drop-in) espelha esses tokens.

- **Sem build step.** O showcase é um único `index.html` self-contained (CSS em `<style>`, SVGs em `<defs><symbol>`, JS em `<script>`, fonte Silka embutida em base64). Abre direto no navegador, sem bundler, sem dependências de rede.
- **Marca irmã:** Facial Class. Mesma arquitetura; só mudam cores, prefixo de classe (`fc-`), chave de tema (`fc-theme`) e nomes de arquivo. Ver a seção **13. Diferenças por marca**.

---

## 1. Arquitetura e arquivos

```
repo (corporal-class-design-system)
├── index.html                       # Showcase self-contained (a referência viva)
├── README.md · HANDOFF.md           # Visão geral e entrega
└── design-system/                   # PACOTE consumível
    ├── silka.css                    # @font-face Silka 300–700 (woff2 base64, self-contained)
    ├── corporal-design-system.css   # DROP-IN: tokens + reset + foco + motion + tipografia + botões + chips/status
    ├── corporal-design-tokens.json  # Tokens legíveis por máquina (Framer, Style Dictionary-like, IA)
    ├── Button.tsx                   # Code Component Framer/React (Property Controls)
    ├── DESIGN-SYSTEM.md             # Spec + como aplicar + prompt p/ IA
    ├── THEME.md                     # Mecânica claro/escuro
    ├── CONTRIBUTING.md              # Governança (SemVer, regra das 3 equipes, depreciação)
    ├── CHANGELOG.md                 # Histórico (Keep a Changelog + SemVer)
    ├── IMPLEMENTACAO.md             # Este documento
    ├── voz-e-tom.md                 # Guia de copy generalista
    ├── glossario-marca.md           # Termos de domínio da marca
    └── copy-deck.corporal.json      # Strings de produto do showcase
```

**Duas camadas de classe (importante):**
- **Pacote drop-in** usa classes prefixadas **`cc-*`** (`.cc-btn`, `.cc-fill`, `.cc-h1`, `.cc-status`…). É o que você consome num projeto.
- **Showcase** (`index.html`) usa classes **curtas próprias** (`.b`, `.alert`, `.dtbl`, `.cmdk`…) no `<style>` inline. São as **implementações de referência** dos componentes que ainda não estão no drop-in (data table, command palette, app shell, date picker, modal, etc.). Para portar esses para sua infra, copie o markup + o CSS da seção correspondente.

Os snippets de CSS abaixo são os do **showcase** (classes curtas), por serem a referência completa. Onde o drop-in difere no nome, está indicado.

---

## 2. Tokens e theming

### 2.1 Mecânica de tema (claro/escuro)

Três camadas, nesta ordem de precedência:
1. **Default = dark.** O bloco `:root` define o tema escuro.
2. **Override claro** via `[data-theme="light"]` no `<html>` **e** `@media (prefers-color-scheme: light)` (para quem nunca tocou no toggle).
3. **Escolha do usuário** persistida em `localStorage['cc-theme']` (`'light'`|`'dark'`), que vence o sistema.

`color-scheme: dark` é declarado no `:root` (form controls nativos seguem o tema).

**Init anti-flash** (IIFE no início do `<body>`, roda antes da pintura):
```js
var t = localStorage.getItem('cc-theme');
if (t !== 'light' && t !== 'dark')
  t = matchMedia('(prefers-color-scheme: light)').matches ? 'light' : 'dark';
document.documentElement.setAttribute('data-theme', t);
```
O toggle (`#themeToggle`, `.theme-toggle`, com `aria-pressed`) alterna `data-theme`, grava no `localStorage` e sincroniza o `aria-pressed`. Um listener de `matchMedia('(prefers-color-scheme: light)')` só reage se o usuário **não** fixou preferência.

> **Numa infra sua:** para evitar flash de tema, replique o IIFE de init no `<head>` (inline, antes do CSS). Sem JS, o site ainda segue `prefers-color-scheme`.

### 2.2 Cores institucionais (brand) — base imutável

Cores do brandbook Corporal Academy. Nada deve sair daqui.

| Token | Hex | Papel |
|---|---|---|
| `--brand-bordo` | `#D6515C` | predominante |
| `--brand-coral` | `#E88A92` | accent |
| `--brand-amarelo` | `#FFE4A4` | dourado |
| `--brand-vermelho` | `#FFB1BD` | rosa |
| `--brand-amarelado` | `#FFCA9B` | pêssego |
| `--brand-branco` | `#FFFFFF` | — |
| `--brand-preto` | `#000000` | — |

Os `--brand-*` **não** mudam entre temas; os tokens de tema abaixo é que derivam deles.

### 2.3 Tokens de tema (valores do showcase)

| Token | Dark | Light | Uso |
|---|---|---|---|
| `--bg` | `#0E0708` | `#FAFAFA` | fundo da página |
| `--bg2` | `#1A1012` | `#F6F4F2` | fundo alt / app shell |
| `--card` | `#241619` | `#FCFAF8` | superfície de cartão/controle |
| `--card2` | `#2E1B1F` | `#F2EFEB` | superfície elevada/hover |
| `--line` | `rgba(232,138,146,.14)` | `rgba(214,81,92,.16)` | bordas/divisores |
| `--txt` | `#FAF7F8` | `#2A1517` | texto principal |
| `--mut` | `#CBBDBF` | `#835D63` | texto secundário |
| `--legal-mut` | `#A07B82` | `#8B656B` | texto legal/rodapé |
| `--bordo` | `#5A2730` | `#5A2730` | bordô profundo (gradiente) |
| `--bordo2` | `#D6515C` | `#D6515C` | primária (fill/seleção) |
| `--bordo-bright` | `#E0727A` | `#E0727A` | hover sólido |
| `--coral` | `#E88A92` | `#C2434E` | **accent interativo** (links, ativo, foco) |
| `--coral-soft` | `#F0A6AD` | `#B05059` | accent hover |
| `--logo` | `#FFFFFF` | `#D6515C` | cor do lockup SVG |
| `--gold` / `--gold-ink` | `#FFE4A4` / `#FFE4A4` | `#FFE4A4` / `#7A5A0E` | dourado fill / texto |
| `--rose` / `--rose-ink` | `#FFB1BD` / `#FFB1BD` | `#FFB1BD` / `#B04A5E` | rosa fill / texto |

**Semânticas** (texto sempre com ícone/label junto, nunca cor sozinha):

| Token | Dark | Light |
|---|---|---|
| `--success` / `--success-bg` | `#45C08A` / `rgba(69,192,138,.14)` | `#147A45` / `rgba(31,138,91,.10)` |
| `--warning` / `--warning-bg` | `#E8B53D` / `rgba(232,181,61,.14)` | `#8A5A00` / `rgba(138,90,0,.10)` |
| `--danger` / `--danger-bg` | `#FF7D93` / `rgba(255,125,147,.14)` | `#BE2C45` / `rgba(199,47,73,.10)` |
| `--info` / `--info-bg` | `#74C0D8` / `rgba(116,192,216,.14)` | `#2A7286` / `rgba(42,114,134,.10)` |

> `--info` é **teal** (não coral): como o accent da Corporal é coral/avermelhado, o info usa teal para não se confundir com danger/warning. `--danger` light = `#BE2C45` (aprofundado para passar AA 4.5:1 no badge de erro; ver CHANGELOG 1.3.1).

### 2.4 Escalas e foundations (iguais nos dois temas, salvo sombra)

```
/* Raio */            --radius-sm:8px  --radius-md:14px  --radius-lg:18px  --radius-pill:30px
/* Espaçamento (4px)*/--space-1:4  -2:8  -3:12  -4:16  -6:24  -8:32  -12:48 (px)
/* Motion */          --motion-fast:.15s  --motion:.2s  --motion-slow:.4s  --ease:cubic-bezier(.2,.8,.2,1)
/* Foco */            --focus:0 0 0 3px rgba(232,138,146,.55)
/* z-index */         --z-base:0  --z-raised:10  --z-sticky:40  --z-overlay:100  --z-toast:1000
/* Opacidade */       --opacity-disabled:.45  --opacity-muted:.66  --opacity-hover:.08  --opacity-overlay:.58
/* Borda */           --bw-hair:1px  --bw-1:1px  --bw-2:2px
/* Blur */            --blur-sm:6px  --blur-md:12px  --blur-lg:20px
/* Breakpoints */     --bp-phone:390px  --bp-tablet:810px  --bp-desktop:1200px
/* Tamanhos */        --size-icon-sm:16  --size-icon:20  --size-icon-lg:24  --control-h:44  --touch-min:44 (px)
/* Aspect ratio */    --ar-square:1/1  --ar-photo:4/3  --ar-wide:16/9
/* Componentes */     --row-h:46px  --row-h-compact:38px  --side-w:248px  --cal-cell:38px  --row-sel:(accent .16 dark / .10 light; Corporal light usa rgba(194,67,78,.10))
```

**Elevação (sombra em camadas; dark preto, light com matiz da marca):**
```
--elev-1 … --elev-4                            (cada nível mais difuso)
--elev-3 dark: 0 4px 12px rgba(0,0,0,.45), 0 16px 36px rgba(0,0,0,.55)
--elev-raised:var(--elev-1)  --elev-overlay:var(--elev-3)  --elev-modal:var(--elev-4)
--sh / --sh-strong: rgba(214,81,92,.40/.50) dark · .18/.26 light  (glow dos botões fill, matiz bordô)
```
**Regra:** use o papel (`--elev-raised/overlay/modal`), não o número.

### 2.5 Formato do `corporal-design-tokens.json`

JSON customizado (não Style Dictionary; sem `$value/$type`), com temas separados dentro de cada categoria:
```
{ $meta, breakpoints, color:{brand,dark,light}, elevation:{roles,dark,light},
  radius, foundations:{opacity,borderWidth,blur,breakpoint,size,aspectRatio},
  space, zIndex, motion, focus, typography:{fontFamily,weights,styles{...}},
  components:{button,statusColors}, icons, gradients, accessibility }
```
Consuma gerando suas variáveis (CSS vars, JS, Tailwind theme). **Regra de ouro:** componente lê token, nunca hex solto.

---

## 3. Fundamentos (reset, foco, motion, tipografia, ícones)

### 3.1 Reset / base
```css
*{margin:0;padding:0;box-sizing:border-box}
html{scroll-behavior:smooth}
body{font-family:var(--font-sans);background:var(--bg);color:var(--txt);line-height:1.5;
     -webkit-font-smoothing:antialiased;font-variant-numeric:tabular-nums}
img,svg,video{display:block;max-width:100%}
a{color:var(--coral);text-decoration:none}
```
`font-variant-numeric:tabular-nums` é **global** (dígitos alinham em tabelas/listas).

### 3.2 Foco visível (a11y, obrigatório)
```css
a:focus-visible,button:focus-visible,.b:focus-visible,[tabindex]:focus-visible{
  outline:2px solid var(--coral);outline-offset:2px;box-shadow:var(--focus);border-radius:6px}
@media (forced-colors:active){
  a:focus-visible,button:focus-visible{outline:2px solid Highlight;outline-offset:2px}}
```
Componentes que removem `outline` sempre repõem com `box-shadow:var(--focus)`.

### 3.3 Movimento reduzido
```css
@media (prefers-reduced-motion:reduce){
  html{scroll-behavior:auto}
  *,*::before,*::after{transition-duration:.01ms!important;animation-duration:.01ms!important}}
```
Todas as transições/animações viram instantâneas. (Ao auditar contraste por script, desative transições antes de medir, senão pega valores intermediários da animação de tema.)

### 3.4 Tipografia
- Stack: `'Silka','Poppins',system-ui,-apple-system,'Segoe UI',Roboto,sans-serif`. Silka embutida em `silka.css` (5 `@font-face`, pesos 300/400/500/600/700, woff2 base64, `font-display:swap`). Sem Silka → Poppins → system-ui (sem quebrar).
- Escala fluida com `clamp()` por papel (Display/H1…H6, Body, Legal); valores por breakpoint (Desktop 1200 / Tablet 810 / Phone 390). Ex.: `H1 ≈ clamp(34px,6vw,60px)`, peso 500, `letter-spacing:-.025em`, `line-height:1.03`. Body mínimo **16px** no mobile (evita zoom no iOS).
- No Framer: cada papel vira um **Text Style** com override por breakpoint (L/M/S).

### 3.5 Ícones
- Biblioteca **Phosphor**, peso **Thin** (traço 1 na grade 24). Definidos uma vez em `<svg><defs><symbol id="ph-…">` e referenciados por `<use href="#ph-…"/>`.
- Cor por `currentColor` (herda `color`/accent, segue o tema). Ícone decorativo leva `aria-hidden="true"`; ícone que informa estado vem **com texto**.
- IDs de logo: `logo-hor`, `logo-vert`, `logo-tipo`, `logo-icon` (lockups oficiais, `fill=currentColor`, cor via `--logo`). IDs de UI: `i-arrow/i-check/i-sun/i-moon/i-copy/i-download` + ~60 `ph-*`.

### 3.6 Gradientes
Montados só com cores do brand. Tipos: linear primário (`bordo2→bordo`), espectral (bordô→coral→rosa→pêssego), **malha** (multi radial-gradient sobre `--bg`) e **spot** (radial topo). Sem cônico, sem blob, sem halo. Ver seção 03 do showcase.

---

## 4. Botões (componente central) ⭐

Classe base **`.b`** (drop-in: `.cc-btn`). Composição: `.b` + tamanho (`.sm`/`.md`/`.lg`) + variante (`.fill`/`.solid`/`.outline`/`.ghost`/`.gold`/`.gold-o`). Ícone interno: `.ico` (drop-in `.cc-ico`).

```css
.b{display:inline-flex;align-items:center;justify-content:center;gap:9px;font-family:inherit;
   font-weight:600;border:none;cursor:pointer;text-decoration:none;border-radius:30px;
   transition:.2s var(--ease);white-space:nowrap;min-height:44px;max-width:100%}
```

### 4.1 Tamanhos
| Classe | font-size | padding | gap | ícone |
|---|---|---|---|---|
| `.b.sm` | 13px | 10px 18px | 7px | 15px |
| `.b.md` (padrão) | 15px | 14px 26px | 9px | 18px |
| `.b.lg` | 16px | 17px 32px | 9px | 19px |

### 4.2 Variantes e estados (hover/active)
```css
/* Preenchido (gradiente + glow) */
.b.fill{background:linear-gradient(120deg,#C2434E,var(--bordo));color:#fff;box-shadow:0 10px 30px var(--sh)}
.b.fill:hover{transform:translateY(-2px);box-shadow:0 16px 38px var(--sh-strong)}   /* sobe + glow cresce */
.b.fill:active{box-shadow:0 6px 18px var(--sh)}                                      /* glow recua */

/* Sólido */
.b.solid{background:#C2434E;color:#fff}  .b.solid:hover{background:#B05059}
.b.solid:hover{background:var(--bordo-bright)}

/* Contorno */
.b.outline{background:transparent;color:var(--txt);border:1px solid var(--line)}
.b.outline:hover{border-color:var(--coral);color:var(--coral)}

/* Inline / ghost */
.b.ghost{background:transparent;color:var(--coral);padding:10px 14px;border-radius:8px}
.b.ghost:hover{color:var(--coral-soft);text-decoration:underline;text-underline-offset:3px}

/* Dourado e dourado contorno */
.b.gold{background:var(--gold);color:#140D1B;border:1px solid var(--gold-ink)}
.b.gold:hover{background:var(--gold-deep)}
.b.gold-o{background:transparent;color:var(--gold-ink);border:1px solid var(--gold-line)}
.b.gold-o:hover{border-color:var(--gold-ink)}
```

### 4.3 Estados globais (microinteração)
```css
.b:active{transform:translateY(0) scale(.985);transition-duration:var(--motion-fast)}  /* press: encolhe 1.5% em .15s */
.b:disabled,.b.is-disabled{opacity:.42;pointer-events:none;box-shadow:none;transform:none}
.b:focus-visible{outline:2px solid var(--coral);outline-offset:2px;box-shadow:var(--focus)}
@media (max-width:560px){.b{white-space:normal;text-align:center}}
```
**Resumo da microinteração do botão:** `transition:.2s var(--ease)` (transform + box-shadow + cor); `fill` levanta 2px no hover e o glow (`--sh`→`--sh-strong`) intensifica; `:active` faz `scale(.985)` em `.15s`. O **glow vive só no botão** (cartões não usam glow). Alvo de toque mínimo 44px (`min-height`).

### 4.4 Framer (`Button.tsx`)
Code Component com Property Controls:
```
label:string · variant:"fill|solid|outline|ghost|gold|gold-o"=fill ·
size:"sm|md|lg"=md · showIcon:boolean=true · iconPosition:"left|right"=right ·
link:string · newTab:boolean · disabled:boolean · style:CSSProperties
```
Mapeia 1:1 para as variantes/tamanhos acima. Ícone via Phosphor (ArrowRight padrão), `currentColor`.

---

## 5. Componentes

Para cada um: classes, estados e microinterações. (A11y consolidada na seção 8.)

### 5.1 Chips / badges / status
```css
.cc-chip{background:var(--card);border:1px solid var(--line);color:var(--txt);
         padding:9px 16px;border-radius:30px}                 /* neutro, cor via token */
.cc-badge{color:var(--gold-ink);border:1px solid var(--gold-line);font-size:10.5px;
          padding:4px 10px;border-radius:20px}                /* destaque dourado */
.cc-status{padding:9px 15px;border-radius:30px;border:1px solid currentColor}
.cc-status.is-success{color:var(--success);background:var(--success-bg)}
.cc-status.is-warning{color:var(--warning);background:var(--warning-bg)}
.cc-status.is-danger {color:var(--danger);background:var(--danger-bg)}
.cc-status.is-info   {color:var(--info);background:var(--info-bg)}
```
`border:1px solid currentColor` herda a cor da variante. No showcase, o status compacto da tabela é `.st` com um ponto `.st i` (`.st.ok/.warn/.off`).

### 5.2 Formulários
```css
.input,.textarea,.select{font-size:15px;color:var(--txt);background:var(--card);
  border:1px solid var(--line);border-radius:14px;padding:0 14px;min-height:44px;width:100%;outline:none;
  transition:border-color var(--motion) var(--ease),box-shadow var(--motion) var(--ease)}
.input:focus{border-color:var(--coral);box-shadow:var(--focus)}
.input.is-error{border-color:var(--danger)}  .input.is-success{border-color:var(--success)}
.input:disabled{opacity:var(--opacity-disabled);cursor:not-allowed}
.input[readonly]{background:var(--card2);color:var(--mut)}
```
- **Checkbox/radio** (`.check`): input `appearance:none`, 20px, `border-radius:6px` (radio 50%); `:checked` pinta `var(--coral)` + checkmark via `::after` (rotate 45°); radio usa `::after` circular. `min-height:44px` no label.
- **Toggle** (`.toggle`): trilho 42×24 `border-radius:999px`, thumb 20px `::after` que desliza `left 2px→20px` em `.2s`; `:checked` pinta o trilho de `--coral`.
- Validação: mensagem diz **o que houve + como resolver**; estado por classe (`is-error`/`is-success`) + texto, nunca só cor.

### 5.3 Feedback
```css
.alert{display:flex;gap:11px;padding:13px 16px;border-radius:14px;border:1px solid var(--line);background:var(--card)}
.alert.ok{background:var(--success-bg);border-color:var(--success)}   /* idem info/warn/err */
.toast{border-radius:30px;background:var(--card2);box-shadow:var(--elev-overlay)}
.spinner{width:28px;height:28px;border:3px solid var(--line);border-top-color:var(--coral);
         border-radius:50%;animation:spin .7s linear infinite}
.skel{background:linear-gradient(90deg,var(--card) 25%,var(--card2) 37%,var(--card) 63%);
      background-size:400% 100%;animation:shimmer 1.4s ease infinite}
.empty{border:1px dashed var(--line);border-radius:18px;text-align:center;padding:30px 20px}
```
Alert: ícone (`#ph-check/info/warn/x`) + `<b>Título.</b> corpo`. Spinner: `role="status" aria-label="Carregando"`. Skeleton e spinner têm `@keyframes` (ver 7).

### 5.4 Sobreposições
```css
.modal-scrim{position:absolute;inset:0;background:radial-gradient(... rgba(232,138,146,.30) ...),rgba(90,39,48,.55);
             backdrop-filter:blur(var(--blur-sm))}                       /* scrim com matiz da marca */
.modal{width:min(380px,100%);background:var(--card);border:1px solid var(--line);
       border-radius:18px;box-shadow:var(--elev-modal);padding:22px}     /* .m-row alinha botões à direita */
.tip .bub{background:var(--card2);border:1px solid var(--line);border-radius:8px;box-shadow:var(--elev-overlay)}
.pop{width:240px;background:var(--card);border:1px solid var(--line);border-radius:14px;box-shadow:var(--elev-overlay)}
```
Scrim usa `backdrop-filter:blur(6px)` + matiz bordô (não cinza). Aparição/posicionamento de tip/pop é via interação (hover/JS) com `transition:.2s var(--ease)`.

### 5.5 Estrutura
```css
.tabs{display:flex;gap:4px;border-bottom:1px solid var(--line)}
.tab{color:var(--mut);border-bottom:2px solid transparent;padding:10px 14px;margin-bottom:-1px}
.tab.active{color:var(--coral);border-bottom-color:var(--coral)}
.acc summary svg{transition:transform .2s var(--ease)}                  /* caret */
.acc details[open] summary svg{transform:rotate(180deg)}               /* gira 180° ao abrir */
.av{width:40px;height:40px;border-radius:50%;background:var(--card2);border:1px solid var(--line)}
.av .dot{position:absolute;...;background:var(--success);border:2px solid var(--bg)}  /* presença */
.av-stack .av{margin-left:-12px;border:2px solid var(--bg)}            /* empilhado */
.crumb a:hover{color:var(--coral)}  .crumb .cur{color:var(--txt);font-weight:500}
.pg{min-width:40px;height:40px;border-radius:8px;border:1px solid var(--line)}
.pg.active{border-color:var(--coral);color:var(--coral);font-weight:600}  .pg:disabled{opacity:.45}
```
Accordion usa `<details>/<summary>` nativo (estado open gratuito + acessível); `::-webkit-details-marker{display:none}` esconde o triângulo padrão.

### 5.6 Cartão (com elevação no hover)
```css
.cardv{background:var(--card);border:1px solid var(--line);border-radius:18px;padding:18px;overflow:hidden}
.cardv.inter{cursor:pointer;transition:transform .2s var(--ease),box-shadow .2s var(--ease)}
.cardv.inter:hover{transform:translateY(-3px);box-shadow:var(--elev-overlay)}   /* sobe 3px + sombra (sem glow) */
.cardv-media .media{height:92px;background:linear-gradient(120deg,var(--gold),var(--rose))}
```

### 5.7 Avançados (camada de produto)

**Data table (`.dtbl`)** — em wrapper `.dtbl-x{overflow-x:auto}`:
- Cabeçalho sticky: `thead th{position:sticky;top:0;background:var(--card2)}`, uppercase 10.5px.
- Ordenação: botão `.ths` no `th`; estado em `aria-sort="ascending|descending"`; a seta SVG gira (`[aria-sort="ascending"] .ths svg{transform:rotate(180deg);opacity:1}`).
- Linha: `tbody tr{transition:background .2s}`; `tr:hover{background:var(--card2)}`; `tr.is-sel{background:var(--row-sel)}` (tint do accent).
- Seleção: `.check` na `.col-ck`; densidade `.dtbl.compact` (linha 46→38px via `--row-h`/`--row-h-compact`).
- Status: `.st` + `.st i` (ponto) com `.ok`(success)/`.warn`(warning)/`.off`(mut).

**Command palette (`.cmdk`)** — overlay com `.cmdk-scrim`:
- `.cmdk{width:min(520px,100%);box-shadow:var(--elev-modal)}`, input `.cmdk-in`, lista `.cmdk-list{max-height:262px;overflow-y:auto}`, grupos `.cmdk-grp` (uppercase).
- Item `.cmdk-item`; `:hover{background:var(--card2)}`; ativo `.is-active{background:var(--row-sel)}` + ícone em `--coral` + `.kbd` (↵).
- `.kbd`: tecla com `border-bottom-width:2px` (relevo), `tabular-nums`. id da lista: `cmdk-list-co`.

**App shell (`.appshell`)** — `grid-template-columns:var(--side-w) 1fr` (248px + conteúdo), `min-width:660px`:
- Sidebar `.appside` (brand + `.navgroup-lbl` + itens + `.side-foot`); topbar `.appbar`.
- Item `.navitem`; `:hover{background:var(--card2);color:var(--txt)}`; ativo `.is-active{background:var(--row-sel)}` + **faixa accent à esquerda** via `::before{width:3px;background:var(--coral)}` + ícone em `--coral`.

**Date picker (`.cal`)** — calendário `width:296px`, grid `.cal-grid{grid-template-columns:repeat(7,1fr)}`:
- Dia `.cal-day{height:var(--cal-cell)}`; `:hover{background:var(--card2)}`.
- Estados: `.is-out{opacity:.5}` · `.is-today{box-shadow:inset 0 0 0 1.5px var(--coral)}` · `.is-range{background:var(--row-sel)}` · `.is-sel{background:var(--bordo2);color:#fff}` (tom profundo p/ o branco passar contraste).

---

## 6. Comportamentos (JavaScript)

Tudo vanilla, sem dependências. Scripts no fim do `<body>`.

- **Tema:** init anti-flash + toggle `#themeToggle` (ver 2.1). Chave `localStorage['cc-theme']`.
- **Scrollspy:** `IntersectionObserver({rootMargin:"-78px 0px -68% 0px",threshold:0})` observando cada `<section>` com id. O índice (`byId`) e a categoria (`catOf`) são montados a partir dos links `.tn-menu a`; ao intersectar, marca `.active` no link e no `.tn-cat` do grupo. **Adicionar seção nova = só adicionar o link no menu + a `<section id>`** (o spy se auto-registra).
- **Menu dropdown:** clique no `.tn-cat` alterna `.open` no `.tn-group` (+ `aria-expanded`); só um grupo aberto por vez. Hover abre via `@media(hover:hover)`.
- **Burger (mobile):** `.tn-burger` alterna `.nav-open` no `.topnav` (+ `aria-expanded`, `aria-controls="tn-menus"`); ícones `.bi-menu`/`.bi-x`. Fecha com Escape, clique fora, ou clique em item.
- **Click-to-copy:** clique em `.hex`, `.code`, `.fr-row .val` ou `.sw` → `navigator.clipboard.writeText()` (fallback `document.execCommand('copy')`); feedback via toast `.copy-toast.show` (ícone check + "Copiado …"), some em ~1500ms.
- **Data table:** checkbox da linha → `is-sel` na `<tr>`; `input[data-density=".dtbl"]` → alterna `.compact`; `input[data-selall=".dtbl"]` → marca/desmarca todos e dispara `change` em cada um.
- **Download de assets:** `downloadSVG(id,nome)` serializa o `<symbol>` para blob; `downloadPNG(id,nome,W,H)` rasteriza em `<canvas>` (export de gradiente em 4K 3840×2160).

---

## 7. Microinterações ⭐

- **Tokens de tempo/curva:** `--motion-fast .15s · --motion .2s · --motion-slow .4s`; curva única `--ease:cubic-bezier(.2,.8,.2,1)`.
- **Padrão de transição:** propriedades animadas = `transform, box-shadow, border-color, background, color, opacity` (nunca layout/texto de leitura). Declaração típica: `transition:.2s var(--ease)`.
- **`@keyframes`:**
  - `spin` → `to{transform:rotate(360deg)}` (spinner, `.7s linear infinite`).
  - `shimmer` → `0%{background-position:100% 0} 100%{-100% 0}` (skeleton, `1.4s ease infinite`).
- **Catálogo de microinterações:** botão fill sobe 2px + glow no hover, `scale(.985)` no press; cartão `.inter` sobe 3px + sombra; accordion gira o caret 180°; toggle desliza o thumb; tab/nav/pager/cal trocam fundo/cor; tabela destaca linha no hover; seta de ordenação gira.
- **Reduced-motion:** tudo cai para `.01ms` (ver 3.3).

---

## 8. Acessibilidade ⭐

- **Contraste WCAG 2.1 AA** medido nos 2 temas (texto normal ≥4.5:1; grande/UI ≥3:1). Verificado por sweep automatizado (compondo fundos semi-transparentes sobre o pai e desativando transições antes de medir). 0 falhas em dark e light.
- **Foco visível:** outline 2px `--coral` + `box-shadow:var(--focus)` (anel 3px); guard para `forced-colors` (`Highlight`).
- **Cor nunca sozinha:** todo estado/semântica vem com ícone e/ou texto. (Por isso o info é teal, distinto do coral/vermelho.)
- **Alvos de toque:** `--touch-min:44px` em botões, `.check`, `.toggle`; controles densos (pager 40, dia do calendário 38) compensam com espaçamento.
- **Movimento:** respeita `prefers-reduced-motion`.
- **Semântica/ARIA:** `aria-pressed` (toggle de tema), `aria-expanded`+`aria-haspopup` (menu), `aria-controls` (burger), `aria-sort` (tabela), `role="status"` (spinner), `role="dialog"/aria-modal` (modal/cmdk), `aria-current="page"` (nav ativo), `aria-hidden` (ícones decorativos), `aria-label` (controles sem texto).
- **Utilitários:** `.sr-only` (texto só p/ leitor de tela), `.kbd` (teclas).
- **HTML nativo primeiro:** `<details>/<summary>` (accordion), `<table>` semântica, `<button>`/`<a>` corretos.

---

## 9. Responsividade

- Container `.wrap` (largura máxima + padding lateral), centralizado.
- Breakpoints de referência: Phone 390 / Tablet 810 / Desktop 1200.
- Tipografia fluida com `clamp()` (sem saltos). Grids responsivos colapsam (ex.: `.a11y` 2col→1col; `.logo-rules` 2col→1col; `.form-grid`).
- Mobile: navbar vira menu burger (`.nav-open`); botões podem quebrar linha (`white-space:normal`); tabela/app shell têm scroll horizontal próprio (`.dtbl-x`, `.appshell-x`).

---

## 10. Consumo numa infra (3 caminhos)

**A) HTML/CSS drop-in**
```html
<html lang="pt-BR"><head>
  <link rel="stylesheet" href="silka.css">                   <!-- fonte PRIMEIRO -->
  <link rel="stylesheet" href="corporal-design-system.css">
</head><body>
  <button class="cc-btn cc-fill">Assinar agora</button>
</body></html>
```
Dark é o padrão; `data-theme="light"` no `<html>` força claro; sem isso segue `prefers-color-scheme`. Para toggle sem flash, replique o IIFE de init no `<head>`.

**B) Tokens (qualquer stack)** — importe `corporal-design-tokens.json` e gere CSS vars / JS / Tailwind theme. Componentes consomem token, nunca hex.

**C) Framer** — crie **Color Styles** (cada um com valor Claro e Escuro), **Text Styles** por breakpoint (L/M/S = 1200/810/390), use `Button.tsx` como Code Component e ícones Phosphor Thin. O site publicado segue `prefers-color-scheme`.

---

## 11. Deploy / infra

- **Repos:** `Eddie-FacialAcademy/corporal-class-design-system` (irmão: `facialclass-design-system`).
- **Hospedagem:** GitHub Pages, servindo o `index.html` self-contained da raiz. **Sem build/CI** — `git push` na `main` publica.
- **Cache:** Pages (Fastly) tem `Cache-Control:max-age=600`; após push, propaga em ~1–3 min. No navegador, use hard refresh (Ctrl+Shift+R) ou query `?v=`.
- **Git:** credencial via Git Credential Manager (sem token em arquivo); `.git` fora do OneDrive (`AppData\Local\gitdirs\`); EOL travado em LF (`.gitattributes`); `desktop.ini` ignorado.
- **URL ao vivo:** https://eddie-facialacademy.github.io/corporal-class-design-system/

---

## 12. Governança e versionamento

- **SemVer:** MAJOR (quebra token/API), MINOR (adição retrocompatível), PATCH (bug/contraste/ajuste). Toda mudança visível entra no `CHANGELOG.md` no mesmo commit.
- **Arquitetura de tokens:** `primitive → semantic/intent → component`; o semântico **nunca sugere valor** (`--btn-bg`, não `--bordo-500`); componente referencia o semântico.
- **Regra das 3 equipes:** só vira padrão oficial o que tem 3+ usos reais.
- **Gate de pronto:** design (todos estados/variantes), a11y (teclado/foco/semântica), código (consome tokens), doc (quando usar / quando não / exemplo).
- **Depreciação:** marcar `deprecated`, manter ≥1 MINOR com caminho de migração, remover só em MAJOR.

---

## 13. Diferenças por marca (Corporal × Facial)

Mesma arquitetura, JS, componentes, escalas e semânticas. Mudam:

| Aspecto | Corporal Class | Facial Class |
|---|---|---|
| Arquivos | `corporal-design-system.css` · `corporal-design-tokens.json` · `copy-deck.corporal.json` | `facial-design-system.css` · `facial-design-tokens.json` · `copy-deck.facial.json` |
| Prefixo de classe (drop-in) | `cc-*` | `fc-*` |
| Chave de tema | `localStorage['cc-theme']` | `localStorage['fc-theme']` |
| id do cmdk | `cmdk-list-co` | `cmdk-list-fa` |
| Marca / Academy | Corporal Class / Corporal Academy | Facial Class / Facial Academy |
| Token primário | `--bordo2 #D6515C` | `--roxo2 #644389` |
| Accent interativo | `--coral` (#E88A92 dark / #C2434E light) | `--lilas` (#A289D7 dark / #644389 light) |
| `--info` (dark/light) | `#74C0D8` / `#2A7286` (**teal**, p/ não confundir com o coral/vermelho) | `#A289D7` / `#5E4A8C` (roxo) |
| Foco | `rgba(232,138,146,.55)` | `rgba(162,137,215,.55)` |
| Sombra (matiz) | `rgba(214,81,92,…)` | `rgba(100,67,137,…)` |
| Logo no nav | 30px (lockup com mais respiro) | 24px |

**Cores institucionais da Facial** (brandbook): `--brand-roxo #644389` · `--brand-lilas #A289D7` · `--brand-amarelo #FFE4A4` · `--brand-vermelho #FFB1BD` · `--brand-amarelado #FFCA9B` · branco · preto. Superfícies/texto no tema são tingidos no roxo. Semânticas success/warning/danger são **iguais** nas duas marcas; só o `--info` difere (Corporal teal, Facial roxo).

> Trocar de marca = trocar a linha de import (`corporal-…`↔`facial-…`) e o prefixo de classe. O resto do código é idêntico.

---

## 14. Seções do showcase (referência viva)

`01` Marca · `02` Cores · `03` Gradientes · `04` Tipografia · `05` Ícones · `06` Botões e componentes · `07` Feedback e sobreposições · `08` Estrutura e navegação · `09` Componentes avançados · `10` Acessibilidade · `11` Fundamentos do sistema · `12` Princípios e processo · `13` Voz e tom · `14` Tokens para copiar.

Cada seção tem `id="nav-…"` (alvo do scrollspy) e a maioria dos valores/códigos é **click-to-copy**.
