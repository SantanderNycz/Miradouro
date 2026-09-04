# Miradouro

**Miradouro** é um estúdio para criar relatórios de campanha interativos e
publicá-los como páginas estáticas. Inseres os dados (por formulário ou por
JSON), vês o relatório em tempo real e exportas um `index.html` autónomo, pronto
para deploy.

O nome é fixo — é a identidade do software. O nome da **empresa/cliente** que
aparece no relatório é definido por ti, no campo do cabeçalho.

## Como funciona

Tudo corre no browser, sem _build_ e sem instalar nada: React, Recharts e Babel
são carregados por CDN e o relatório é transpilado na própria página.

O relatório é **guiado por dados (JSON)**: um motor genérico monta abas e blocos
(KPIs, gráficos, tabelas, notas, síntese, funil, etc.) a partir de um JSON. O
número de abas e o conteúdo adaptam-se ao JSON — **uma só aba esconde a barra de
abas**. O formato está descrito em [`SCHEMA.md`](./SCHEMA.md).

## O estúdio (`index.html`)

A página principal é o estúdio. Tem **dois modos**, que partilham o mesmo motor e
a mesma pré-visualização:

**Modo Formulário** — preenches campos (cabeçalho, KPIs, tabelas, gráficos e
textos) para o relatório padrão de 4 abas. À direita atualiza em tempo real.

**Modo JSON** — colas ou importas um JSON (por exemplo, um gerado pelo Claude) e o
relatório adapta-se: número de abas, blocos e conteúdo vêm todos do JSON. É aqui
que crias abas à medida, removes abas ou fazes um relatório mais enxuto. O botão
**“Instruções p/ Claude”** copia o formato para colares no chat. Ver
[`SCHEMA.md`](./SCHEMA.md).

Comuns aos dois modos:

- **Guardado automaticamente** no browser (localStorage).
- **`**duplo asterisco**`** à volta de uma expressão põe-na a **negrito** nos textos.
- **⬇ Exportar index.html** — gera um relatório autónomo (dados embutidos).
- **Exportar/Importar** dados (JSON).

## Ver localmente

Basta abrir o `index.html` no browser (é preciso ligação à internet, por causa dos
CDNs). Para servir localmente e evitar restrições de `file://`:

```bash
python3 -m http.server 8000
# depois abrir http://localhost:8000
```

## Deploy do estúdio

Qualquer alojamento estático serve; o `index.html` (o estúdio) está na raiz.

- **Vercel** — importar o repositório; _framework preset_ = "Other", **sem** _build_.
  Não é preciso `vercel.json`. Se usares um, **não** incluas um _rewrite_
  `"/(.*)" → "/index.html"` sem teres um `index.html` — foi isso que já derrubou o
  site (a raiz reencaminha para um ficheiro inexistente e dá 404).
- **GitHub Pages / Netlify / Cloudflare Pages** — sem _build command_; pasta/output = raiz.

## Publicar um relatório

Um relatório é um deploy **separado** do estúdio (um por campanha/cliente):

1. No estúdio, edita os dados (formulário ou JSON).
2. **⬇ Exportar index.html** — descarrega o relatório autónomo.
3. Faz deploy desse `index.html` (novo projeto Vercel, Netlify, etc.).

O relatório exportado é gerado pelo mesmo motor do estúdio, por isso mantêm sempre
a mesma linguagem visual.

## Estrutura

```
index.html    o estúdio Miradouro (página principal)
SCHEMA.md     formato do JSON dos relatórios (e prompt para o Claude)
README.md
```
