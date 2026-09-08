# Como adicionar um ano novo

Guia para publicar os dados de um ano novo nas páginas de transparência.
É para ser seguido do começo ao fim, sem precisar saber programar.

**Quando fazer:** na virada do ano, assim que passam a existir dados do ano
novo para lançar.

**Quais páginas têm divisão por ano:** só três. As outras (servidores,
estagiários, empresas sancionadas, julgamentos, painéis) usam uma aba só e
não precisam deste guia.

| Página | O ano de cada linha é o ano... | A planilha tem a data de atualização automática? |
|---|---|---|
| Contratos | do número do contrato (`5/2027` → 2027) | sim |
| Diárias | da data de partida da viagem | sim |
| Remuneração detalhada dos servidores | da folha de pagamento | **não** — é preenchida à mão |

**Como funciona, em resumo:** cada ano é uma aba separada na planilha do
Google Sheets, e cada aba é publicada com um endereço (link) próprio. A
página só conhece os anos cujos links estão escritos no código dela.
Adicionar um ano são, então, duas tarefas:

1. **Na planilha** — criar a aba do ano e publicá-la, o que gera o link.
2. **No código** — colar esse link na página.

Se você fizer só uma das duas, o ano novo **não aparece** no site.

---

## Antes de começar

- Acesso para editar a planilha do Google Sheets daquela página.
- Acesso para enviar alterações a este repositório (GitHub).
- Saber se a planilha tem o menu **Transparência** (o script que faz parte
  do trabalho sozinho). As de Contratos e Diárias têm; a de Remuneração
  detalhada não.

---

## Parte 1 — Na planilha (Google Sheets)

### 1.1 Criar a aba do ano

**Se a planilha tem o menu Transparência:**
clicar em **Transparência → Criar aba do próximo ano**. O script copia a
estrutura da aba mais recente, apaga os dados, coloca a aba nova na frente
das outras e já preenche a data de atualização na célula A1. Pronto — pule
para o passo 1.2.

**Se não tem (Remuneração detalhada), criar a aba à mão:**

1. Clicar com o botão direito numa aba de ano e escolher **Duplicar**.
2. Renomear a cópia só com os quatro dígitos do ano: `2027`.
3. Arrastar a aba para a **primeira posição** (anos mais novos ficam à
   esquerda).
4. Apagar os dados, mantendo os cabeçalhos.

> Nesta planilha os **cabeçalhos ficam na linha 1** e os dados começam na
> linha 2. (Nas de Contratos e Diárias é diferente: a linha 1 é a data de
> atualização, a linha 2 são os cabeçalhos e os dados começam na linha 3.)

### 1.2 Conferir os cabeçalhos

Os cabeçalhos vieram da cópia — **não mudar nada**. Se um nome de coluna
ficar diferente do das outras abas, a página pode parar de funcionar. O
formato completo de cada planilha está em [padrao-planilhas.md](padrao-planilhas.md).

### 1.3 Lançar os dados

Preencher as linhas do ano novo no mesmo padrão das abas anteriores: datas
em `dd/mm/aaaa`, valores sem "R$", campo sem informação fica em branco. Os
detalhes estão em [padrao-planilhas.md](padrao-planilhas.md).

### 1.4 Publicar a aba

É este passo que gera o link que a página vai ler. Ele **não** tem relação
com o botão **Compartilhar** — a planilha pode continuar restrita; só a aba
publicada fica pública.

1. Menu **Arquivo → Compartilhar → Publicar na web**.
2. Abrir a aba **Link**.
3. No primeiro campo, trocar "Documento inteiro" pela **aba do ano novo**.
   ⚠️ Nunca deixar em "Documento inteiro" — isso publica todas as abas,
   inclusive rascunhos.
4. No segundo campo, escolher **Valores separados por vírgula (.csv)**.
5. Clicar em **Publicar** e confirmar.
6. Verificar que **"Republicar automaticamente quando forem feitas
   alterações"** está marcado (já vem marcado). Sem isso, o link congela e
   para de receber o que você editar depois.

### 1.5 Copiar o link

O Google mostra um endereço parecido com este:

```
https://docs.google.com/spreadsheets/d/e/2PACX-.../pub?gid=123456&single=true&output=csv
```

Copiar o endereço inteiro. O trecho `gid=123456` é o que identifica a aba
daquele ano.

---

## Parte 2 — No código (este repositório)

### 2.1 Abrir o arquivo da página

| Página | Arquivo |
|---|---|
| Contratos | `paginas/contratos.html` |
| Diárias | `paginas/diarias.html` |
| Remuneração detalhada | `paginas/remuneracao_detalhada_servidores.html` |

### 2.2 Achar a lista de anos

Procurar no arquivo por **`FONTES_POR_ANO`** (fica perto do começo). É uma
lista com um ano por linha, do mais novo para o mais antigo:

```js
const FONTES_POR_ANO = {
    "2026": "https://docs.google.com/.../pub?gid=47019203&single=true&output=csv",
    "2025": "https://docs.google.com/.../pub?gid=1922578156&single=true&output=csv",
};
```

### 2.3 Acrescentar o ano novo

Adicionar **uma linha no topo da lista**, com o ano entre aspas, dois
pontos, o link entre aspas e uma vírgula no fim:

```js
const FONTES_POR_ANO = {
    "2027": "COLE_AQUI_O_LINK_COPIADO_NA_PARTE_1",
    "2026": "https://docs.google.com/.../pub?gid=47019203&single=true&output=csv",
    "2025": "https://docs.google.com/.../pub?gid=1922578156&single=true&output=csv",
};
```

### 2.4 Só na página de Diárias: virada de legislatura

Isto só acontece de quatro em quatro anos (a próxima virada é em 2029).
Se o ano que você está adicionando **começa uma legislatura nova**:

1. No mesmo arquivo, procurar por **`LEGISLATURAS`** e acrescentar a nova no
   topo da lista:

   ```js
   const LEGISLATURAS = [
       { numero: "22", rotulo: "22ª legislatura (2029–2032)", inicio: 2029, fim: 2032 },
       { numero: "21", rotulo: "21ª legislatura (2025–2028)", inicio: 2025, fim: 2028 },
       // ...
   ];
   ```

2. Na planilha auxiliar de **gabinetes** (a que lista os vereadores),
   acrescentar as linhas dos vereadores novos com o número da legislatura
   nova. Se essa planilha ainda não estiver publicada, publicá-la como no
   passo 1.4.

Em anos comuns (que seguem na mesma legislatura), pular este passo.

### 2.5 Salvar e enviar

Salvar o arquivo, fazer **commit** e **push** para o `main`. Em poucos
minutos o GitHub Pages republica a página sozinho — não precisa fazer mais
nada no servidor.

---

## Parte 3 — Conferir no site

1. Esperar uns 2 ou 3 minutos depois do push.
2. Abrir a página no ar:
   `https://cm-marilia.github.io/tabelas_transparencia/paginas/<arquivo>.html`
3. Conferir que:
   - o ano novo aparece no seletor de ano (nas diárias, no seletor de
     legislatura);
   - ao escolher o ano novo, a tabela carrega;
   - a quantidade de linhas faz sentido;
   - a data em "dados atualizados em" está certa (na remuneração, aparece
     "Atualizado mensalmente").

---

## Se algo der errado

| O que você vê | Causa provável | O que fazer |
|---|---|---|
| O ano novo não aparece no seletor | o link não foi colado no `FONTES_POR_ANO`, ou o push não subiu | revisar a Parte 2 e confirmar o commit no GitHub |
| O ano aparece, mas a tabela dá erro ou fica vazia | a aba não foi publicada, foi publicada em formato diferente de CSV, ou o link ficou incompleto | refazer o passo 1.4 e copiar o link de novo |
| A tabela some ou vem com colunas trocadas | algum cabeçalho foi renomeado ou mudou de lugar na aba nova | comparar os cabeçalhos com uma aba antiga e com [padrao-planilhas.md](padrao-planilhas.md) |
| O site mostra dados antigos; some uma linha que você lançou depois | "Republicar automaticamente" ficou desmarcado | marcar de novo em Publicar na web |
| Apareceram abas de rascunho no site | foi publicado "Documento inteiro" em vez da aba | em Publicar na web, publicar apenas a aba certa |

---

## Remover um ano do site

1. **Na planilha:** Arquivo → Compartilhar → Publicar na web → **Conteúdo
   publicado e configurações → Parar de publicar** (a aba daquele ano).
2. **No código:** apagar a linha daquele ano no `FONTES_POR_ANO`, salvar,
   commit e push.

Fazer os dois. Só parar de publicar deixa a página tentando abrir um link
que não existe mais.

---

## Checklist

**Planilha**
- [ ] Aba do ano criada, nome só com os quatro dígitos, na primeira posição.
- [ ] Cabeçalhos iguais aos das abas anteriores.
- [ ] Dados lançados no padrão (datas, valores, campos vazios).
- [ ] Aba publicada como CSV — a aba, não "Documento inteiro".
- [ ] "Republicar automaticamente" marcado.
- [ ] Link copiado.

**Código**
- [ ] Link colado no topo do `FONTES_POR_ANO` da página certa.
- [ ] Diárias, só na virada de legislatura: `LEGISLATURAS` e planilha de
      gabinetes atualizadas.
- [ ] Commit e push para o `main`.

**Site**
- [ ] O ano novo aparece no seletor e a tabela carrega.
- [ ] Contagem de linhas e data de atualização conferem.

---

## Onde ver mais

- **Formato das planilhas** (cabeçalhos, tipos de coluna, qual ano recebe
  cada linha): [padrao-planilhas.md](padrao-planilhas.md).
- **Visão geral do projeto:** [README.md](README.md).
- **Scripts da planilha** (menu Transparência, data de atualização, máscara
  de CPF): repositório privado
  [tabelas_transparencia_scripts](https://github.com/cm-marilia/tabelas_transparencia_scripts).
