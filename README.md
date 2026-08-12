# AGORA APROVA — Demonstração (quiz + demo jogável)

A página de vendas interativa do AGORA APROVA, no mesmo molde do `alter-ego-demo`:
**quiz de diagnóstico (23 telas) → app inteiro vivo, semeado com um aluno-exemplo
no dia 42 → qualquer botão de criação abre o convite com a moeda de −30%.**

```bash
powershell -ExecutionPolicy Bypass -File serve.ps1
```

Sobe em `http://localhost:3814` (launch.json: `agora-aprova-demo`). Arquivo único:
`index.html` = cópia do app `agora-aprova` + camada de demo. Nada persiste:
**cada visita renasce igual** (sem gate, sem pasta, sem Drive; storage isolado
em `agoraaprova_demo_v1`).

---

## O funil (overlay `#prequiz`, controlador `PQ`)

PERFIL (8) · DIAGNÓSTICO (6) · PLANO (10) = 24 telas. O `data-s` é só rótulo
estável — a ordem é a do DOM e o PQ navega por POSIÇÃO (`PQ.ids`), então a
tela de idade entra como `data-s="4"` **entre** o sexo (2) e o momento (3).
Ao mexer nas telas, conferir as fronteiras de seção em `sect()` e nas 3 barras.

| Tela | O que faz |
|---|---|
| s0 | placa AA + **o relógio de virada da tela Hoje** (`PQ.buildClock()`) + a frase da marca |
| s1 | a dor (4) → `DEMO_PAIN` — vira retrato, warn, métrica do case |
| s2 | sexo → metade da persona + foto-espelho |
| s4 | **idade** (≤16 · 17–18 · 19–24 · 25+) → banda jovem/veterano da persona **e a idade exibida no case** (`IDADES`), além da foto-espelho (`PQ_FOTOS`) |
| s3 | momento (terceirão · cursinho · repetente · trabalha) → chip do case |
| s5·s6 | boas mãos (retrato p&b) · conversas |
| s7 | **curso alvo** (Medicina/Direito/Eng./Psico/Enfermagem/não sei) → busca o corte REAL em `CURSOS_SISU` e vira `state.cursoAlvo` do app semeado |
| s8 | onde quebra → `DEMO_QUEBRA` |
| s9 | **áreas que assustam** (≤3, cores das áreas) → `DEMO_FRACAS` — inclinam fila, autoavaliação, simulados e estudo do seed |
| s10–12 | escala de concordância → puxa a nota estimada pra baixo |
| s13 | o método aberto (fórmula pública + letreiro de pesos da matriz) |
| s14 | prioridades (≤3) → `DEMO_TEMAS` → metas [2]/[3] e grandes objetivos |
| s15·s16 | tempo por dia · sim/não |
| s17 | 4 barras (~5s) com legendas personalizadas |
| s18 | **velocímetro real do app** (`notaGauge`) com nota estimada × corte do curso + ritmo/dia + **top-5 da fila** calculado da MATRIZ com as áreas fracas pesando 2× |
| s20 | a rampa até 8 NOV (rótulos dinâmicos: ≈nota → ~corte) |
| s21 | **o lacre do malote** — raspadinha canvas vestida de envelope ENEM |
| s22·s23 | o case (persona + oval de blur no rosto) e os números — **todos lidos do estado semeado** (`demoNums()`), nunca digitados |

`PQ.finish()` derruba o overlay → toast → tela Hoje da persona.

### O relógio na abertura

A tela 1 usa o **instrumento real do app**, não um número: `PQ.buildClock()` monta
o mesmo markup do `diasHeroCard()` (`.fc`, `.fc-digit`, `data-slot`) dentro de
`#pq-fc`. Como `#prequiz` vem antes de `#app` no DOM, o `fcTick()` que já roda
acha ESTES dígitos primeiro e é ele quem faz a virada — nenhum timer novo. Quando
`PQ.finish()` remove o overlay, o relógio da tela Hoje volta a ser o único
(verificado: continua virando depois do quiz).

Em telas de até 660px de altura os 2 cartões de número somem, e até 600px o cartão
do telefone também: o relógio é o herói da abertura e o botão não pode cair abaixo
da dobra. **Nada de `style=` inline nessa tela** — inline vence media query.

## As 4 personas (sexo × jovem/veterano)

Lucas (terceirão) · Rafael (trabalha e voltou) · Bianca · Camila. **A idade não é
fixa**: sai de `IDADES` conforme a banda escolhida (m 16/18/22/27 · f 16/17/21/26),
para o case ter a idade do visitante. Mesmo motor numérico; muda a pele.
`face:{x,y,w,h}` em **% da imagem original**, medidas na grade de contato
(`placeFaceBlur` converte pro crop real).

### ⚠ O deploy deixou de ser um arquivo só

As 4 fotos de pessoa são **suas**, não de banco de imagem, e vivem em `img/`.
Publicar apenas o `index.html` deixa o quiz **sem rosto**. Suba a pasta junto.

### Onde as fotos moram

As 21 fotos estão **baixadas em `img/`** (~2,1 MB) — veja `img/LEIA-ME.md`. Mas a
demo continua puxando do Unsplash por padrão, porque é isso que mantém o
`index.html` publicável como arquivo único. Uma linha decide:

```js
function fotoOrigem() { return "cdn"; }   // ou "local"
```

⚠ É `function`, não `const`: `demoSeed()` roda no boot, antes desta linha, e um
`const` aqui cai na zona morta (TDZ) e derruba o app inteiro — já aconteceu.

Nos dois modos há rede de segurança automática (local falhou → Unsplash; Unsplash
falhou → local), verificada nos dois sentidos. A exceção são as fotos de meta e
objetivo, que entram como `background-image` e não disparam evento de erro.

As tags fixas do HTML não guardam mais URL: guardam `data-foto="<id>"`
(+ `data-w`, `data-pb` para preto-e-branco) e são resolvidas por `aplicarFotos()`
no boot.

### Fotos de pessoa — as suas, 4 e só 4 (11/08/2026)

Nenhuma foto de banco de imagem sobrou no elenco humano. Nem avatares: a fileira
de rostinhos da tela do método e o avatar da conversa **foram removidos**.

| | Começo do quiz (espelho, p&b) | Case de sucesso (rosto borrado) |
|---|---|---|
| Homem, **todas as idades** | `espelho-homem` — formatura | `case-homem` — roupa normal |
| Mulher, **todas as idades** | `espelho-mulher` — formatura | `case-mulher` — roupa normal |

A lógica: no começo, **onde ela quer chegar** (formatura); no case, **alguém como
ela hoje** (roupa normal, rosto borrado). Por isso a idade não troca mais a foto —
só o nome e os números do case.

O resolvedor distingue os dois mundos pelo formato do id: se casa com o padrão do
Unsplash (`\d{9,13}-[a-z0-9]{12}`) é foto de banco e obedece à chave `fotoOrigem`;
qualquer outro nome (`espelho-homem`) é foto própria e sai direto de `img/`.

As duas fotos masculinas se invertem entre as bandas de propósito: assim **nenhum
fluxo mostra a mesma cara como espelho e como case** (verificado nas 4 combinações).
As 6 fotos de tema + as 4 sempre visíveis (mão escrevendo, livros+maçã, gato, livro
aberto) são todas sem rosto e **todas distintas**, então a tela Metas nunca repete
imagem.

## O seed (dia 42)

`demoSeed()` roda no boot (⚠ TDZ: MATRIZ/CURSOS ainda não existem → ids de
assunto **hardcoded**) e a cada resposta relevante (`PQ.syncPersona()` reseeda
tudo). Produz: ~140 registros de estudo (fracas com 2 toques/dia + 1 de largura
+ varredura que pinta o cartão-resposta), 3 simulados subindo (~509 → ~611 → ~721,
fórmula real `380+630·pct^1.2`), 11 redações (520→800), 3 revisões vencendo HOJE,
5 metas, 3 grandes objetivos (1 batido: "40 assuntos que mais caem"), 30 dias de
rotina, maratona de 7 dias vencida, 1 distração, diário e leitura do CORRETOR.
Nota geral ≈ 735–745 — logo abaixo do corte de Medicina, acima do de Enfermagem.

## Celular / iOS — o que já foi corrigido aqui (e NÃO no app pai)

O app que a demo mostra é a cópia do `agora-aprova`, então estes defeitos existem
lá também — se forem portados, é o mesmo bloco de CSS:

1. **Setas do carrossel de grandes objetivos em cima do título.** No celular o
   cartão empilha (foto em cima, painel embaixo) e as setas, centradas na altura
   TODA, caíam sobre o nome do objetivo. Agora sobem para o meio da faixa da foto
   (`top: 62px`, sem `transform`) e viraram alvos de 44px, o mínimo do iOS.
1b. **O cartão só empilhava até 700px, mas a UI de celular vai até 880px.** Entre
   701 e 880 (celular com zoom out, ou tablet) você tinha barra inferior de celular
   com o cartão em layout de desktop. O bloco todo passou para `max-width: 880px`.
1c. **A porcentagem era `#fff` sobre painel branco** (herdou a cor do cartão sobre
   foto): invisível na alma Papel, nas duas larguras. Agora `var(--accent)`.
1d. **`space-between` no painel largo** jogava o valor numa borda e a porcentagem
   na outra, com um vão de ~310px no meio — pior quanto maior o zoom out. No
   celular a linha virou `flex-start` com 12px, "Faltam Xd" alinhou à esquerda com
   o resto, e título/legenda ganharam `max-width: 34ch`.
2. **Botão "+ Avançar" com 38px de altura.** `.gob-add` vem depois de
   `.gob-add-big` no arquivo e vencia o padding. Corrigido para 47px no celular.
3. **`backdrop-filter` sem `-webkit-`** em 10 lugares (barra inferior, fundo do
   modal, setas, chips, sidebar, more-sheet…). O Safari do iPhone só entende a
   versão prefixada até o iOS 17 — sem ela o vidro fosco simplesmente não existe.
4. **`env(safe-area-inset-bottom)` sem fallback** na barra inferior: sem o `, 0px`
   a declaração inteira cai fora onde `env()` não existe.
5. **`100vh` no CORRETOR** → `100dvh`, senão a barra de endereço do iOS empurra o
   campo de digitar para fora da tela.
6. **Grade do mês**: `-webkit-overflow-scrolling: touch` + `overscroll-behavior-x:
   contain`, para arrastar a grade não disparar o gesto de "voltar" do Safari.

### A barra da demo — a causa do rodapé quebrado no zoom out

O app padrão é responsivo porque a única coisa fixa nele é a navegação. A demo põe
**mais uma barra fixa** por cima (a da moeda), e ela estava com as medidas cravadas
no CSS: `bottom: 64px` (altura chutada da navegação) e `padding-bottom: 220px` de
reserva. Isso só fecha num tamanho de tela — em qualquer outro, sobra ou falta.

Agora **nada é chutado**. `demoFitCta()` mede as duas barras e publica:

```
--bn-h   → altura real da barra inferior (0 no desktop, onde ela some)
--cta-h  → altura real da barra da demo  (0 depois que o convite já foi aberto)
```

e o CSS consome: a barra fica em `calc(var(--bn-h) + 10px)` e a reserva do
conteúdo é `calc(var(--bn-h) + var(--cta-h) + 34px)` — **a última regra do
arquivo**, porque `body.atelier .view` tem especificidade maior e um `padding` de
atalho que apagaria a reserva. A medição roda no boot, em `resize`,
`orientationchange`, no `visualViewport` (a pinça do iOS não dispara `resize`) e
num `ResizeObserver` nas duas barras — então vale em qualquer zoom.

Junto vieram: o breakpoint da barra passou de 860 para **880** (onde a sidebar
vira navegação inferior — a faixa 861–880 tinha barra de desktop sobre navegação
de celular, e é justamente onde o zoom out cai); a barra vira **só o botão** em
telas de até 520px de altura; e `.grid-2/3/4 > * { min-width: 0 }` matou um
*blowout* de grid que dava 89px de rolagem lateral no desktop.

Medido (largura × altura, com o conteúdo rolado até o fim):

| Tela | reserva | folga do conteúdo | barra × navegação |
|---|---|---|---|
| 375×812 retrato | 254px (74+146+34) | 24px | 10px acima |
| 772×700 zoom out | 235px (74+127+34) | 24px | 10px acima |
| 836×390 deitado | 176px (74+68+34) | 24px | 10px acima |
| 942 desktop | 115px (0+81+34) | 16px | sem navegação |

Depois de abrir o convite a barra some, `--cta-h` vira 0 e a reserva encolhe
sozinha (254 → 108px). Nenhuma das 11 telas vaza na horizontal em nenhum tamanho.

**O cartão da contagem (`.dias-hero`)** tinha dois alinhamentos ao mesmo tempo no
celular: o relógio à esquerda (`.dh-wrap-fc { align-items: flex-start }`) e o bloco
do curso centralizado (`.dh-alvo { margin: 0 auto }`). Empilhado, agora tudo
centraliza e um filete de 1px separa as duas leituras (quanto falta × para onde) —
até 880px. Acima disso o layout original continua: relógio à esquerda, curso à
direita, sem filete.

O funil também ganhou dois níveis novos de altura: em **≤840px** (iPhone em pé) os
dois cartões de número da abertura deitam em linha (172px → 61px) para o botão
nascer visível, e em **≤480px** (celular deitado) a placa sai, o relógio encolhe e
as opções afinam — nunca abaixo dos 44px de alvo de toque.

## O convite (`demoPaywall`)

Stats/gráfico/duel/case **lidos do estado**; moeda 3D com `coindrama` (quase cai
no SEM DESCONTO, vira no −30%); âncoras 39,80 / 148,80 / 258,80 → **preços
decididos 27,80 / 98,80 (ATÉ O SISU, recomendado·Pix) / 178,80**.

⚠ **Trocar os links de checkout** em `DEMO_PLANS` (placeholders
`pay.kiwify.com.br/SEU-LINK-*`).

## Livres × trancados

`DEMO_FREE`: navegar (`go`), Conteúdo (abrir/filtrar), carrosséis, calendário,
assinalar metas, catálogo de cursos (`cursoModal`/`setCurso`), `faseModal`.
Todo o resto → `demoPaywall(DEMO_REASONS[k])`.
