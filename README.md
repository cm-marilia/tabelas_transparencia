# Tabelas de Transparência — Câmara Municipal de Marília

Páginas HTML com dados de transparência da Câmara Municipal de Marília
(contratos, diárias, empresas sancionadas, julgamento das contas anuais).
Cada uma é uma tabela/painel independente — não formam um portal único,
cada uma é incorporada num ponto diferente do site oficial
<https://www.marilia.sp.leg.br>.

Os dados vêm de planilhas do **Google Sheets**, publicadas como CSV e lidas
diretamente pelo HTML — não há backend. A atualização dos dados é feita
direto na planilha; a página só exibe. Quem grava o carimbo de "atualizado
em" e mascara CPFs na origem são scripts de um repositório **privado**
separado (não fica aqui por rodar sobre dados sensíveis das planilhas —
ver seção [Repositório de scripts](#repositório-de-scripts)).

---

## Estrutura do repositório

```
tabelas_transparencia/
├── paginas/          páginas completas, hospedadas no GitHub Pages
│   ├── comum.css          estilo compartilhado por todas as páginas
│   ├── comum.js           funções compartilhadas (datas, exportação, iframe, etc.)
│   ├── brasao_marilia.png imagem do brasão, usada pelas páginas
│   ├── contratos.html
│   ├── diarias.html
│   ├── empresas_sancionadas.html
│   └── julgamentos_contas_camara.html
│
└── incorporacao/     fragmentos de HTML para colar no editor do site oficial
    ├── contratos.html
    ├── diarias.html
    ├── empresas_sancionadas.html
    └── julgamentos_contas_camara.html
```

### `paginas/` — o site de verdade

Cada arquivo aqui é uma página HTML completa e autossuficiente. É publicada
no **GitHub Pages** deste repositório e fica acessível em:

```
https://cm-marilia.github.io/tabelas_transparencia/paginas/<arquivo>.html
```

Ela lê o CSV publicado da planilha correspondente, monta a tabela/cards,
oferece busca, filtros e exportação (CSV/Excel/JSON/XML/impressão). É o
que efetivamente roda — todo o código funcional vive aqui.

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
de cada planilha está documentado no topo de cada arquivo `.html`.

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

As páginas de `paginas/` mostram um selo verde com a data/hora da última
atualização, lido diretamente da célula A1 da planilha (carimbo gravado
pelos scripts do repositório privado). O selo é sempre a informação mais
recente disponível — não é preciso mexer neste repositório para ele mudar.
