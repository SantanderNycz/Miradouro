# Esquema do JSON do relatório

O relatório é gerado dinamicamente a partir de um JSON. O `editor.html` (modo
**JSON**) importa esse JSON, mostra a pré-visualização e exporta o `index.html`
pronto a publicar. Este documento descreve o formato — serve também de
**prompt para o Claude**: cola-o no chat, junta os teus dados e pede o JSON.

> No editor, o botão **“Instruções p/ Claude”** copia uma versão resumida deste
> guia para a área de transferência.

## Estrutura de topo

```json
{
  "meta": { "brand": "...", "title": "...", "intro": "...", "footer": "...", "readingNotes": "..." },
  "tabs": [
    { "label": "Nome da aba", "blocks": [ /* blocos */ ] }
  ]
}
```

- `tabs` é uma lista. Podes ter **1 aba** (a barra de abas fica escondida — relatório mais enxuto) ou **várias**.
- O número de abas, os blocos e o conteúdo vêm todos do JSON — o HTML adapta-se.
- Em qualquer texto, envolve uma expressão em `**duplo asterisco**` para a pôr a **negrito**.
- **Cores**: um nome (`navy`, `navyLight`, `sage`, `terracotta`, `gold`, `goldLight`, `slate`) ou um hex (`"#4F46E5"`).
- **Formatos de número** (`yFormat` / `format` / `valueFormat`): `percent`, `percent1`, `eur`, `eurM`, `eur2M`, `kEur`, `eur2`, `eur1`, `eur3`, `eurPlain`, `number`.

### Papel semântico das cores (identidade visual)

| Nome | Uso |
|------|-----|
| `navy` | tom escuro principal (títulos, tabs ativas, cabeçalhos de tabela, tooltips, síntese) |
| `navyLight` | cor primária de interação / destaque (`#4F46E5`) |
| `gold` / `goldLight` | variações do destaque (eyebrows, labels, apoios) |
| `sage` | sucesso / positivo / acima da meta |
| `terracotta` | erro / negativo / abaixo da meta |
| `slate` | neutro / sem classificação / dados de suporte |

## Blocos disponíveis

Cada bloco é um objeto com um campo `type`.

### `kpis` — linha de cartões
```json
{ "type": "kpis", "items": [ { "eyebrow": "Objetivo", "value": "108%", "sub": "1,94 M€", "accent": "sage", "footnote": "fonte: Artsoft" } ] }
```

### `card` — agrupa blocos com cabeçalho
```json
{ "type": "card", "eyebrow": "...", "title": "...", "subtitle": "...", "blocks": [ /* ... */ ] }
```

### `row` — coloca blocos lado a lado (responsivo)
```json
{ "type": "row", "blocks": [ /* dois ou mais blocos */ ] }
```

### `barChart` — gráfico de barras
```json
{
  "type": "barChart",
  "data": [ { "lb": "Ago/26", "v": 108, "cor": "sage" } ],
  "xKey": "lb",
  "height": 300,
  "series": [ { "key": "v", "name": "Vendas", "color": "navy", "colorKey": "cor", "stackId": "a" } ],
  "yFormat": "percent",
  "yDomain": [0, 120],
  "referenceLine": { "y": 100, "label": "objetivo" },
  "barLabelFormat": "percent",
  "tooltip": { "valueFormat": "percent", "extraKey": "sub" },
  "legend": true,
  "legendItems": [ { "color": "sage", "label": "Em destaque" } ],
  "variants": [ { "label": "Botão A", "series": [/*...*/], "yFormat": "eurM" } ]
}
```
- `series[].color`: cor fixa da série. `series[].colorKey`: aponta um campo em cada linha de `data` com a cor dessa barra (barras multicoloridas).
- `series[].stackId`: barras com o mesmo `stackId` empilham.
- `variants`: cria um seletor (segmented control) que troca os campos indicados (ex.: alternar entre “Objetivo” e “Vendas”).
- `legendItems`: legenda manual de cores por baixo do gráfico. `legend: true`: legenda automática das séries.

### `table` — tabela
```json
{
  "type": "table",
  "rowToneKey": "_tone",
  "columns": [
    { "key": "lb", "label": "Mês", "align": "left", "bold": true, "tone": "navy" },
    { "key": "vnd", "label": "Vendas", "align": "right", "format": "eur" },
    { "key": "ating", "label": "Atingido", "align": "right", "format": "percent", "tone": "atingido" }
  ],
  "rows": [ { "lb": "Ago/26", "vnd": 1943983, "ating": 108, "_tone": "hero" } ]
}
```
- `tone: "navy"` pinta a célula de escuro; `tone: "atingido"` fica verde se `>= 100` e vermelho se `< 100`.
- `rowToneKey`: campo de cada linha (`"hero"` ou `"exp"`) que dá cor de fundo à linha.

### Texto e caixas
```json
{ "type": "note", "tone": "good", "text": "**Positivo.** ..." }        // good | warn | gold | neutral
{ "type": "banner", "tone": "gold", "text": "Caixa com moldura." }
{ "type": "synthesis", "title": "Síntese", "items": [ { "lead": "Ponto.", "text": "detalhe" } ] }
{ "type": "callout", "title": "Contexto", "text": "Caixa escura de destaque." }
{ "type": "heading", "eyebrow": "...", "title": "...", "subtitle": "..." }
{ "type": "text", "text": "Parágrafo simples." }
```

### `funnel` e `progress`
```json
{ "type": "funnel", "steps": [ { "label": "Investimento", "value": "45.907 €", "sub": "...", "color": "navy" } ] }
{ "type": "progress", "items": [ { "label": "Meta", "valueLabel": "71%", "pct": 71, "color": "navyLight" } ] }
```

## Exemplo mínimo (uma só aba)

```json
{
  "meta": { "brand": "Empresa · Relatório", "title": "Resumo de campanha", "footer": "2026" },
  "tabs": [
    { "label": "Resumo", "blocks": [
      { "type": "kpis", "items": [
        { "eyebrow": "Vendas", "value": "1,94 M€", "accent": "sage" },
        { "eyebrow": "Objetivo", "value": "108%", "accent": "navyLight" }
      ] },
      { "type": "card", "title": "Vendas por mês", "blocks": [
        { "type": "barChart", "xKey": "lb", "yFormat": "eurM",
          "data": [ { "lb": "Jul", "v": 1200000 }, { "lb": "Ago", "v": 1943983 } ],
          "series": [ { "key": "v", "name": "Vendas", "color": "navyLight" } ] }
      ] },
      { "type": "note", "tone": "good", "text": "**Acima da meta.** ..." }
    ] }
  ]
}
```
