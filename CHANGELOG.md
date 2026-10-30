# Registo de alterações — Piri-piri

## 2026-10-30 — estado base `default` e quatro camadas de tokens

### Alterado
- **Descrições dos tokens em inglês** (primitivos, marca e semânticos; 504 no Figma e nos JSON), no formato "Use for…" / "Use as…". O `CLAUDE.md` passa a dizer que a descrição do token é a exceção ao português; a documentação continua em português.
- **O estado base chama-se `default` em todo o lado** (nomes de tokens, valores da propriedade `state` no Figma, specs). `rest` não se usa. Revoga a regra 1 anterior (estado base implícito) e a decisão de 2026-10-29 sobre `rest`. Tokens sem estados continuam sem sufixo.
- **Quatro camadas:** primitivo → **marca** → semântico → componente. A marca é nova (`tokens/brand/<marca>.json`, `brand-{categoria}-{papel}-{passo}`); o semântico aponta para a marca (ou para primitivos em valores sem marca); o componente continua a apontar só para semânticos.
- Atualizados para coerência: `design-system-specialist` (regra "Default is implicit", vocabulário de estados), `ds-component-spec` (`check_spec.py` exige `default` em `states`; modelo de spec), `ds-definition-of-done` (`check_token_layers.py` com camada de marca; verificação Figma com `default`).

### Adicionado
- `tokens/primitives/` (`color.json`, `typography.json`, `scale.json`): 371 primitivos copiados da coleção `.primitives` do ficheiro Figma "DS-Tangity-foundations-2026" (359) mais a escala `color-ntt-blue` (12 passos: 10–95). `radius` copiado com os valores resolvidos (no Figma são aliases da escala). Cada token com `$description` genérica por grupo.
- Por confirmar: `font-weight-800` vale 900 no Figma (copiado tal como está); unidade de `letter-spacing` (-5 … 10).

- `tokens/brand/ntt.json` (primeira marca): `brand-color-primary-*` (12 passos → `color-ntt-blue`), `brand-color-neutral-*` (11 passos → `color-slate`), `brand-font-family-{heading|body|mono}` (→ Inter / JetBrains Mono) e `brand-radius-{sm|md|lg}` (→ 4/8/16). Escolhas minhas, por validar: neutros = slate; Inter nos títulos e no corpo.
- Figma: coleção `primitives` (modo `value`) criada no ficheiro "DS-Fpundations-2027" com os 371 primitivos (269 cor, 99 número, 3 texto): descrição, scopes vazios (escondidos nos seletores) e code syntax WEB `var(--color-slate-50)` em todas. Nomes Figma com `/`, código em kebab-case.
- Figma: coleção `brand` (modo `ntt`) com os 29 tokens de marca como aliases dos primitivos; descrição, scopes vazios e code syntax WEB (`var(--brand-color-primary-50)`). Nomes Figma `brand/color/primary/50`.
- `tokens/semantic/` (`color.light.json`, `color.dark.json`, `layout.json`): 104 tokens semânticos. 61 de cor por modo (fundo, texto, ícone, contorno, ação primária/secundária/destrutiva, feedback de erro/aviso/sucesso/informação) a apontar para a marca ou para primitivos; 43 sem modo (espaço `inset|stack|inline|gap` xs–xl, raio, família/tamanho/peso de letra, altura de linha).
- Figma: coleção `semantic` (modos `light` e `dark`) com os 104 tokens como aliases, scopes estreitos por papel (fundo → preenchimentos, texto → texto, contorno → traço, espaço → gap, etc.), descrição e code syntax WEB.
- Decisões: `radius-pill` (e não `radius-full`) e `font-family-code` (e não `mono`), porque os nomes `radius-full` e `font-family-mono` já existem nos primitivos e o Style Dictionary não aceita duplicados. Ligações em `primary-40` (e não `50`) porque `50` não chega a 4.5:1 sobre o fundo subtil.
- Em falta, por decisão a tomar: `color-overlay-scrim` (não há primitivo preto a 50%), elevação (só há as cores das sombras nos primitivos) e movimento (sem primitivos de duração/curva).


### Testado
- Contraste calculado com `contrast.py`, nos dois modos: 81 pares (texto 4.5:1, contornos e ícones 3:1, texto sobre ações e feedback) passam. O anel de foco tem de ficar afastado do componente (offset), porque `color-border-focus` e o fundo da ação primária são a mesma cor (1.0:1).
- Figma: lida de volta a coleção `semantic` (104; scopes, descrição, code syntax e aliases em ambos os modos; valores resolvidos conferidos em 8 tokens).
- Figma: lidos de volta os 29 aliases da marca com o valor resolvido de cada um; todos apontam para o primitivo certo.
- Figma: lidas de volta as 371 variáveis (tipos, valores, descrição, scopes, code syntax); sem diferenças nas contagens nem nos valores verificados (ntt-blue, cores com transparência, font-weight-800 = 900 copiado).
- `check_token_layers.py` nos primitivos: passa (371). `check_token_layers.py` com um conjunto mínimo de 4 camadas: passa quando o componente aponta para um semântico e falha quando salta para um token de marca. `check_spec.py` alterado mas ainda não corrido com uma spec nova.

## 2026-10-29 — configuração inicial das skills

### Adicionado
- `CLAUDE.md`: convenção única de nomes de tokens (estado base implícito; estado no fim, unido ao último segmento), WCAG 2.2 AA, português de Portugal, mapa de skills e regras de trabalho com Figma.
- 7 skills importadas para `.claude/skills/` com correções (detalhe no `CHANGELOG.md` de cada uma): `design-system-specialist`, `design-system-creator`, `figma-use`, `figma-generate-library`, `figma-code-connect`, `accessibility-review`, `ux-copy`.
- 4 skills novas:
  - `ds-component-spec` — contrato do componente num só ficheiro (YAML + 9 secções) e `check_spec.py`.
  - `ds-a11y-check` — componente contra a spec; `contrast.py` calcula contraste por modo a partir dos tokens.
  - `ds-sustainability-check` — peso, dependências, animação, imagens, tipos de letra; `measure.py`.
  - `ds-definition-of-done` — porta final com evidência; `check_token_layers.py` e script Figma só de leitura.
- `sustainability-budgets.json` vazio: os orçamentos são decisão da equipa.

### Testado
- `check_spec.py`, `contrast.py`, `measure.py` e `check_token_layers.py` com um conjunto de tokens e uma spec de teste (casos que passam e que falham).

### Testado no Figma e no Style Dictionary (ficheiro de teste "Piri-piri — testes das skills")
- Variável e grupo com o mesmo nome: o Figma aceita (4 casos, incluindo renomear). O Style Dictionary 4.4.0 descarta em silêncio um token aninhado noutro token. A regra 2 do `CLAUDE.md` mantém-se, agora com o motivo verificado; a justificação anterior ("o Figma deve comportar-se da mesma forma") estava errada.
- Script Figma da `ds-definition-of-done`: um conjunto Button de teste com 6 variantes e 4 defeitos de propósito. A primeira versão encontrou 3; falhou o texto com duas cores (`figma.mixed`). A versão corrigida lê os segmentos de texto e encontrou os 4, num teste corrido com o script exatamente como está no ficheiro.
- ~~Decisão nova: no Figma, o estado base da propriedade `state` chama-se `rest`.~~ **Revogada em 2026-10-30** (ver entrada seguinte): `rest` não se usa.
