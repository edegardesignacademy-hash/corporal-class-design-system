# Voz e tom (guia de copy do Design System)

> Guia **generalista**. Vale para **todas as marcas** do Design System, presentes e futuras. As regras aqui são neutras de domínio. O vocabulário específico de cada marca (os termos do que ela ensina ou vende) fica em **`glossario-marca.md`**, um por marca, e **nunca cruza** para outra.

## Para que serve
Padroniza a copy de interface: hero, notas de seção, botões, alertas, estados vazios, validação, tooltips e o conteúdo de exemplo dos componentes. Objetivo: **builder para builder**. A copy ajuda quem monta a tela a decidir rápido e certo.

## Princípio geral
Técnica, direta, confiante, antiimproviso. Sem autoelogio, sem hipérbole, sem "happy talk". A copy diz o que a coisa faz e qual é o próximo passo.

## Adjetivos da voz
Técnica · direta · confiante · precisa · antiimproviso.

## Faça
- O rótulo do botão diz a **ação** ("Salvar", "Adicionar item"), nunca o genérico ("Enviar", "OK").
- No erro, diga **o que houve e o próximo passo** ("Não foi possível salvar. Verifique a conexão e tente de novo.").
- Voz ativa, frases curtas.
- Use o termo técnico quando ele for o certo. É material que ensina.
- A cor nunca comunica sozinha: acompanhe sempre de ícone e texto.
- pt-BR em tudo. Código em inglês (padrão da indústria).

## Não faça
- **Sem "&".** Escreva a palavra "e".
- **Sem travessão** (— em-dash nem – en-dash) no meio do texto. Use vírgula, dois-pontos, parênteses ou hífen comum ("-").
- Sem **hipérbole** ("a melhor", "incrível", "revolucionário"). Superlativo vazio enfraquece.
- Sem **"happy talk"** ("Bem-vindo!", "Que bom te ver!").
- Não **compare arquétipos** de profissional ("a diferença entre quem tem sucesso e quem tem medo").
- **Não misture domínio** entre marcas (ver "Separação de domínio").

## Estrangeirismos (o que o DS já traduziu)
Regra: **traduza** quando existe equivalente limpo em pt-BR; **mantenha** o que é nome próprio, sigla ou termo de código.

**Traduzir** (decisões já aplicadas no DS):
tabela de dados (data table) · paleta de comandos (command palette) · seletor de data (date picker) · barra de navegação (navbar) · barra lateral (sidebar) · janela modal (modal) · dica (tooltip) · balão (popover) · notificação (toast) · indicador de carregamento (spinner) · esqueleto (skeleton) · trilha de navegação (breadcrumb) · abas (tabs) · acordeão (accordion) · cartão (card) · estado vazio (empty state) · fundo (background) · padrão (default) · estado (status) · ponto de quebra (breakpoint) · malha (mesh) · alerta (alert).

**Manter** (sem tradução): token · hover e link (como estado/elemento) · avatar · pill (formato de raio) · nomes de ferramenta e código (Framer, Code Component, Color Style, Text Style, CSS, JSON, SVG, PNG) · siglas (WCAG, SemVer) · fontes (Silka, Poppins) · biblioteca de ícones (Phosphor) e seus pesos (Thin) · funções de código (`currentColor`, `clamp()`).

## Separação de domínio (rígida)
Cada marca só fala do **próprio domínio**. O vocabulário específico de cada marca está em **`glossario-marca.md`** (um por marca) e **nunca aparece em outro DS**. Ao ficar em dúvida, **erre para o neutro**, nunca para o domínio de outra marca.

- **Neutros (valem em qualquer marca):** corpo de texto, preenchimento de cor, token, cartão, fundo, escala, grade.
- **Nouns de produto** (trilha, módulo, aula, protocolo, certificado) valem para marcas do mesmo tipo (academia/curso). Marca de outro tipo adapta no próprio `glossario-marca.md`.

## Nomes de marca
Mantidos como são, **por extenso** quando institucional (ex.: "Marca Class", "Marca Academy"). Não abreviar nem inventar variações.

## Acessibilidade na copy
- Erros dizem **o que houve + como resolver**.
- Rótulos de botão **específicos** (a ação, não o genérico).
- A cor nunca é o único sinal: texto e ícone sempre acompanham.
- Mensagem curta ajuda a caber no alvo de toque e a manter o foco legível.

## Números
Algarismos com função **tabular** (não escrever número por extenso em tabelas e listas), para os dígitos alinharem. No DS isso já é global (`font-variant-numeric: tabular-nums`).

## Vozes editoriais (experts)
A microcopy de interface é **sempre neutra** (builder para builder). Vozes de experts (em aulas e conteúdo editorial) são definidas no material da marca, **fora do DS**, e não entram em botões, alertas e labels.

## Microcopy modelo (neutro)
- **Botão:** "Salvar" · "Adicionar item" (a ação, não "Enviar").
- **Estado vazio:** "Nenhum item ainda. Crie o primeiro."
- **Sucesso:** "Salvo. Suas alterações já estão aqui."
- **Erro:** "Não foi possível salvar. Verifique a conexão e tente de novo."
- **Confirmação destrutiva:** "Remover item? Essa ação não tem volta."

---
*Domínio e termos de cada marca: ver `glossario-marca.md` no pacote da marca.*
