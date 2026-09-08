<h1>
  <img src="brasao_marilia.png" alt="Brasão de Marília" width="58" align="top">
  Tabelas de Transparência — Câmara Municipal de Marília
</h1>

Este repositório contém as páginas HTML com dados de transparência da Câmara Municipal de Marília, exigidos pela legislação de transparência e avaliados pelo Programa Nacional de Transparência Pública (PNTP), da ATRICON. Cada uma é uma tabela/painel independente — não formam um portal único, cada uma é incorporada num ponto diferente do site oficial <https://www.marilia.sp.leg.br>.

Os dados vêm de planilhas do Google Sheets, publicadas como CSV e lidas diretamente pelo HTML — não há backend. A atualização dos dados é feita direto na planilha; a página só exibe.

---

## Início rápido

| Quero… | Vá para |
|---|---|
| mudar o texto ao redor de uma tabela no site oficial | editar o fragmento em [`incorporacao/`](incorporacao/) e colar no editor do site |
| mudar o comportamento de uma tabela/painel (busca, filtros, colunas) | editar a página em [`paginas/`](paginas/) |
| publicar a aba de um ano novo | [como-adicionar-um-ano.md](como-adicionar-um-ano.md) |
| conferir o formato que uma planilha precisa ter | [padrao-planilhas.md](padrao-planilhas.md) |
| entender o carimbo / a máscara de CPF, ou mexer nos scripts | [repositório privado de scripts](https://github.com/cm-marilia/tabelas_transparencia_scripts) |

Publicar qualquer alteração de código: **commit + push na `main`** → o
GitHub Pages republica sozinho em poucos minutos (sem build nem deploy). Se
a mudança foi em `incorporacao/`, colar também o novo conteúdo no editor do
site oficial — ver [Publicando uma alteração](#publicando-uma-alteração).

---

## As páginas

Todas ficam em `paginas/` e no ar em
`https://cm-marilia.github.io/tabelas_transparencia/paginas/<arquivo>.html`.
"Divisão" indica se a planilha de origem tem uma aba por ano ou uma aba só;
"selo" é o que a página mostra no cabeçalho.

| Página | O que mostra | Divisão | Selo |
|---|---|---|---|
| [contratos](paginas/contratos.html) | contratos firmados pela Casa — fornecedor, valor, objeto, vigência, PDF e processo no SAPL | por ano (nº do contrato) | carimbo da planilha |
| [diarias](paginas/diarias.html) | diárias e adiantamentos de viagem de vereadores e servidores | por ano (data de partida) | carimbo da planilha |
| [empresas_sancionadas](paginas/empresas_sancionadas.html) | sanções administrativas a contratados — quem está impedido de contratar e por quê | aba única | carimbo da planilha |
| [estagiarios](paginas/estagiarios.html) | quadro de estágio — admissões, desligamentos, lotação | aba única | carimbo da planilha |
| [julgamentos_contas_camara](paginas/julgamentos_contas_camara.html) | julgamento anual das contas da Câmara pelo TCE-SP | aba única | carimbo da planilha |
| [painel_manifestacoes](paginas/painel_manifestacoes.html) | dashboard da Ouvidoria — volume, assunto e canal das manifestações | aba única | sem selo |
| [painel_orcamentario](paginas/painel_orcamentario.html) | dashboard do duodécimo e da execução da despesa, em série histórica | aba única | "Atualizado mensalmente" |
| [remuneracao_detalhada_servidores](paginas/remuneracao_detalhada_servidores.html) | composição mês a mês da remuneração de cada servidor | por ano | "Atualizado mensalmente" |
| [servidores](paginas/servidores.html) | quadro de pessoal atual — cargo, lotação, jornada, admissão | aba única | carimbo da planilha |

---

## Estrutura do repositório

```
tabelas_transparencia/
├── brasao_marilia.png          imagem do brasão, usada pelo README e pelas páginas
├── padrao-planilhas.md         formato que cada planilha do Google Sheets precisa ter
├── como-adicionar-um-ano.md    passo a passo para publicar a aba de um ano novo
├── paginas/              páginas completas, hospedadas no GitHub Pages
│   ├── comum.css               estilo compartilhado por todas as páginas
│   ├── comum.js                funções compartilhadas (datas, exportação, iframe, etc.)
│   └── <assunto>.html          uma página autossuficiente por assunto (ver "As páginas")
│
└── incorporacao/         fragmentos de HTML para colar no editor do site oficial
    └── <assunto>.html          texto para o cidadão + <iframe> da página correspondente
```

### `paginas/` — o site de verdade

Cada arquivo aqui é uma página HTML completa e autossuficiente, publicada
no **GitHub Pages** deste repositório. Ela lê o CSV publicado da planilha
correspondente, monta a tabela/cards e oferece busca, filtros e exportação
(CSV/Excel/JSON/XML/impressão). É o que efetivamente roda — todo o código
funcional vive aqui.

### `incorporacao/` — o que vai no site oficial

Cada arquivo aqui é um **fragmento pequeno**, feito para ser colado direto
no editor de conteúdo do site oficial (`marilia.sp.leg.br`). Ele **não** é
hospedado nem executado sozinho — é só um `<iframe>` (apontando para a
página correspondente em `paginas/`), texto explicativo para o cidadão
sobre aquele assunto, e um script pequeno que ajusta a altura do iframe
automaticamente. Por isso os estilos aqui são todos **inline**: o editor do
site remove blocos `<style>` ao salvar.

> Colar apenas o conteúdo do arquivo no editor — sem `<!DOCTYPE>`, `<html>`,
> `<head>` ou `<body>`, o site oficial já fornece essa estrutura.

---

## Como tudo se conecta

```
Google Sheets (planilha)
   └─ aba publicada como CSV (Arquivo → Compartilhar → Publicar na web)
        └─ paginas/*.html (GitHub Pages) lê o CSV e monta a tabela
             └─ incorporacao/*.html (colado no site oficial) mostra a
                página acima dentro de um <iframe>
```

Quando a estrutura de uma planilha muda (nome de aba, cabeçalho, nova aba
de ano), a página em `paginas/` pode quebrar na hora — o formato esperado
de cada planilha (e o dicionário de colunas completo) está em
[padrao-planilhas.md](padrao-planilhas.md); cada `.html` também repete um
resumo no topo do próprio arquivo.

---

## Publicando uma alteração

1. Editar o arquivo em `paginas/` (ou `incorporacao/`, se for só o texto/
   iframe que mudou).
2. Commit e push para `main`.
3. O GitHub Pages republica sozinho em poucos minutos — não precisa de
   build nem deploy manual.
4. Se mudou algo em `incorporacao/`, colar o novo conteúdo no editor do
   site oficial (o `incorporacao/*.html` não é lido automaticamente por
   nada — é sempre colado manualmente).

---

## Repositório de scripts

Os scripts do Google Apps Script (carimbo de "atualizado em" na planilha,
máscara de CPF, criação de aba de ano) ficam em um repositório **privado**
à parte:

**[cm-marilia/tabelas_transparencia_scripts](https://github.com/cm-marilia/tabelas_transparencia_scripts)**

Separado deste porque roda sobre as planilhas de origem (que podem conter
dados antes de serem tratados/mascarados), enquanto este repositório só
tem código de exibição e dados já públicos.

---

## Selo "dados atualizados em"

A maioria das páginas mostra um selo verde com a data/hora da última
atualização, lido diretamente da célula A1 da planilha (carimbo gravado
pelos scripts do repositório privado). O selo é sempre a informação mais
recente disponível — não é preciso mexer neste repositório para ele mudar.

Três páginas fogem disso de propósito (ver as seções das planilhas em
[padrao-planilhas.md](padrao-planilhas.md)): `painel_orcamentario` e
`remuneracao_detalhada_servidores` exibem o texto fixo "Atualizado
mensalmente"; `painel_manifestacoes` não exibe selo nenhum.
