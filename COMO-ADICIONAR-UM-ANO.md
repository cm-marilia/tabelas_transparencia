# Como adicionar um ano novo

Vale para toda página dividida por ano — o padrão é o mesmo em todas. Duas
partes: publicar a aba nova no Google Sheets e colar a URL no código da
página.

## 1. Na planilha (Google Sheets)

1. Criar a aba do ano novo. Se a planilha tiver o Apps Script instalado
   (repositório [tabelas_transparencia_scripts](https://github.com/cm-marilia/tabelas_transparencia_scripts)),
   usar o menu **Transparência → Criar aba do próximo ano** — ele clona a
   estrutura da aba mais recente, limpa os dados e já carimba a A1.
2. Conferir os cabeçalhos da linha 2 (vêm da cópia — não deve ser preciso
   alterar nada).
3. Lançar os dados a partir da linha 3.
4. Publicar a aba: `Arquivo → Compartilhar → Publicar na web` → no primeiro
   seletor, trocar "Documento inteiro" pela aba do ano novo → no segundo
   seletor, escolher **Valores separados por vírgula (.csv)** → **Publicar**.
   ⚠️ Não deixar em "Documento inteiro" — isso publica todas as abas.
5. Marcar **"Republicar automaticamente quando alterações forem feitas"**
   (fica ligado por padrão) — sem isso o CSV congela na versão publicada.
6. Copiar a URL publicada. Formato:
   `https://docs.google.com/spreadsheets/d/e/2PACX-.../pub?gid=NÚMERO&single=true&output=csv`
   — o `gid=NÚMERO` identifica a aba.

## 2. No código da página (este repositório)

1. Abrir o arquivo da página em `paginas/` (ex.: `paginas/diarias.html`).
2. Procurar por `FONTES_POR_ANO` — fica logo no início do `<script>`, na
   seção CONFIGURAÇÃO.
3. Acrescentar uma linha com o ano e a URL copiada:

   ```js
   const FONTES_POR_ANO = {
       "2027": "https://docs.google.com/spreadsheets/d/e/2PACX-.../pub?gid=NÚMERO&single=true&output=csv",
       "2026": "...",
       ...
   };
   ```

4. Commit e push para `main`. O GitHub Pages republica sozinho — a página
   já passa a mostrar o ano novo no seletor.

Sem o passo 4 do Sheets (publicar a aba) e sem colar a URL aqui, a página
**não enxerga** o ano novo — ela só conhece as URLs que estão no código.

## Outras coisas que mudam com o tempo

- **`diarias.html`** também tem `LEGISLATURAS`, logo abaixo de
  `FONTES_POR_ANO` — quando trocar a legislatura (2029), acrescentar a nova
  faixa de anos ali.
- **`contratos.html`** não tem legislatura, só `FONTES_POR_ANO`.

## Formato esperado da planilha (cabeçalhos, tipos de coluna, carimbo)

Cada página repete o formato esperado (nomes de coluna, tipos, carimbo em
A1) num comentário no topo do próprio arquivo `.html`. O padrão completo,
comum a todas as planilhas, está em [padrao-planilhas.md](padrao-planilhas.md).
