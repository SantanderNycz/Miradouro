# Dias Quentes — Agosto 2026

Relatório de campanha da Laskasas Portugal, publicado como página estática.

## O que é

Uma única página (`index.html`) autossuficiente. Não precisa de _build_ nem de
instalar nada: React, Recharts e Babel são carregados por CDN e o relatório é
transpilado no próprio browser.

O relatório é **guiado por dados (JSON)**: um motor genérico monta abas e blocos
(KPIs, gráficos, tabelas, notas, síntese, funil, etc.) a partir de um JSON. O
número de abas e o conteúdo adaptam-se ao JSON — uma só aba esconde a barra de
abas. O formato está descrito em [`SCHEMA.md`](./SCHEMA.md).

O `report_dias_quentes_ago2026.jsx` é o componente original, mantido apenas como
referência histórica.

## Ver localmente

Basta abrir o `index.html` no browser. Como recorre a CDNs, é preciso ligação à
internet. Para servir localmente (recomendado, evita restrições de `file://`):

```bash
# Python 3
python3 -m http.server 8000
# depois abrir http://localhost:8000
```

## Deploy

Qualquer serviço de alojamento estático serve. O `index.html` está na raiz.

- **GitHub Pages** — em _Settings → Pages_, escolher a branch e a pasta raiz (`/`).
- **Netlify** — arrastar a pasta para o painel, ou ligar o repositório (sem _build
  command_; _publish directory_ = raiz).
- **Vercel** — importar o repositório; _framework preset_ = "Other", sem _build_.
- **Cloudflare Pages** — igual: sem _build command_, _output directory_ = raiz.

## Estúdio (`editor.html`)

Página para produzir e editar relatórios sem tocar em código. Tem **dois modos**,
que partilham o mesmo motor e a mesma pré-visualização:

**Modo Formulário** — preenches campos (cabeçalho, KPIs, tabelas, gráficos e
textos) para o relatório padrão de 4 abas. À direita atualiza em tempo real.

**Modo JSON** — cola ou importa um JSON (por exemplo, um gerado pelo Claude) e o
relatório adapta-se: número de abas, blocos e conteúdo vêm todos do JSON. É aqui
que crias abas à medida, removes abas ou fazes um relatório mais enxuto. O botão
**“Instruções p/ Claude”** copia o formato para colares no chat. Ver
[`SCHEMA.md`](./SCHEMA.md).

Comuns aos dois modos:

- **Guardado automaticamente** no browser (localStorage).
- **`**duplo asterisco**`** à volta de uma expressão põe-na a **negrito** nos textos.
- **⬇ Exportar index.html** — gera um relatório autónomo (dados embutidos) pronto a
  subir à Vercel, substituindo o `index.html` publicado.
- **Exportar/Importar** dados (JSON).

Fluxo típico: editar (formulário ou JSON) → _Exportar index.html_ → substituir o
`index.html` no repositório → novo deploy. O `editor.html` funciona aberto
localmente; se o publicares fica em `/editor.html`.

O `index.html` publicado é gerado exatamente pelo mesmo motor do estúdio, por isso
o editor e o relatório mantêm sempre a mesma linguagem visual.

## Estrutura

```
index.html                        relatório publicado (gerado a partir do estúdio)
editor.html                       estúdio: modo formulário + modo JSON, exporta o index.html
SCHEMA.md                         formato do JSON (e prompt para o Claude)
vercel.json                       config de serving estático na Vercel
report_dias_quentes_ago2026.jsx   componente React original (referência)
README.md
```
