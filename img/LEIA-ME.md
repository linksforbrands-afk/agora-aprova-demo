# As fotos da demo

## 1. As 4 fotos de pessoa — suas, e obrigatórias

Estas **não vêm de banco de imagem nenhum**: são as fotos que você mandou
(11/08/2026), convertidas para 900×1200 JPEG. Elas só existem aqui, então
**a pasta `img/` precisa ser publicada junto com o `index.html`** — sem ela o
quiz fica sem rosto.

| Arquivo | Onde entra | Roupa |
|---|---|---|
| `espelho-homem.jpg` | **começo do quiz** (telas "boas mãos" e diagnóstico), homem — **todas as idades** | formatura |
| `espelho-mulher.jpg` | **começo do quiz**, mulher — **todas as idades** | formatura |
| `case-homem.jpg` | **case de sucesso** (Lucas / Rafael) — é a que tem o **rosto borrado** | normal |
| `case-mulher.jpg` | **case de sucesso** (Bianca / Camila) — **rosto borrado** | normal |

A lógica: no começo a pessoa vê **onde quer chegar** (a formatura); no case ela vê
**alguém como ela hoje** (roupa normal), com o rosto borrado porque é um caso real
anônimo. As fotos de formatura aparecem em preto-e-branco (filtro do CSS).

**A caixa do borrão** fica no `index.html`, em `DEMO_PERSONAS()`:

```
case-homem   → face: { x: 42, y: 32, w: 42, h: 46 }
case-mulher  → face: { x: 50, y: 35, w: 38, h: 47 }
```

São porcentagens **da foto original**, não do cartão. O cartão mostra só ~50% da
altura (`object-fit: cover` + `object-position: center 18%`), então medir na foto
inteira erra o alvo — o jeito certo é desenhar o recorte do cartão com o oval por
cima e conferir. **Trocou a foto, remeça a caixa.**

## 2. As 10 fotos sem rosto — de banco, opcionais

São as que ilustram metas e grandes objetivos. Vêm do Unsplash e o nome é o ID de
lá (`photo-<id>.jpg`). Uma linha no `index.html` decide de onde carregam:

```js
function fotoOrigem() { return "cdn"; }   // ou "local"
```

| | `"cdn"` (padrão) | `"local"` |
|---|---|---|
| De onde vêm | images.unsplash.com | esta pasta |
| Sem internet | não aparecem | funcionam |
| Peso | leve (o CDN redimensiona) | +1,2 MB |

Se um arquivo local faltar, a foto cai no Unsplash sozinha — e vice-versa.
**Exceção:** metas e objetivos entram como `background-image` do CSS, que não
dispara evento de erro; para essas não há rede de segurança.

| Arquivo | O que é |
|---|---|
| `photo-1434030216411-…` | mão escrevendo — meta "registrar todo estudo" |
| `photo-1503676260728-…` | livros e maçã — meta "10 questões no capricho" |
| `photo-1541781774459-…` | gato na cama — meta "dormir antes das 23h30" |
| `photo-1456513080510-…` | livro aberto — objetivo "40 assuntos que mais caem" |
| `photo-1481627834876-…` | estantes — tema *fila* |
| `photo-1524995997946-…` | biblioteca — tema *simulados* |
| `photo-1455390582262-…` | caneta tinteiro — tema *redação* |
| `photo-1506784983877-…` | agenda e café — tema *rotina* |
| `photo-1495364141860-…` | despertador na mão — tema *foco* |
| `photo-1470252649378-…` | amanhecer — tema *revisão* |
