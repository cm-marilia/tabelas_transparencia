# Padrão das planilhas — Tabelas de Transparência da Câmara de Marília

Documento de referência para quem mantém as planilhas do Google Sheets que
alimentam as Tabelas de Transparência. Mantê-lo atualizado a cada mudança de
estrutura.

- **Última revisão:** 02/09/2026
- **Aplica-se a:** contratos, servidores, diárias, remuneração detalhada,
  estagiários, empresas sancionadas, painel orçamentário, painel de
  manifestações, julgamentos de contas.

---

## 1. Como funciona (resumo)

```
Google Sheets (você edita)
   └─ cada aba publicada na web como CSV  (Arquivo → Compartilhar → Publicar na web)
        └─ página HTML no GitHub Pages baixa o CSV e monta a tabela
             └─ o site oficial (marilia.sp.leg.br) embute a página num <iframe>
```

Cada página HTML tem, no topo do `<script>`, as URLs dos CSVs que ela consome.
Quando a estrutura de uma planilha muda (nome de aba, cabeçalho, nova aba de
ano), **a página no ar pode quebrar na hora** — por isso as regras abaixo.

---

## 2. Regras gerais (todas as planilhas)

### 2.1 Layout fixo

| Linha | Conteúdo |
|------|----------|
| **1** | Carimbo de atualização — célula **A1** apenas: `DADOS_ATUALIZADOS_EM: dd/mm/aaaa hh:mm` (preenchido automaticamente pelo Apps Script — **não digitar à mão**) |
| **2** | Cabeçalhos das colunas |
| **3 em diante** | Dados |

### 2.2 Nome da aba

- Planilhas **divididas por ano**: uma aba por ano, nome só com os 4 dígitos
  (`2023`, `2024`, `2025`, `2026`), **anos mais recentes à esquerda**.
- Planilhas **não divididas**: aba única chamada `Página1`.
- Nunca usar nome com espaço, acento ou maiúscula fora do padrão acima.

### 2.3 Cabeçalhos (linha 2)

- `snake_case`, **minúsculas, sem acento, sem espaço**: `data_contrato`,
  `valor_global`, `razao_social`.
- O nome do cabeçalho é um **contrato com o código**. Renomear um cabeçalho
  exige ajuste no HTML correspondente — avisar quem mantém o código e
  atualizar a seção 6 deste documento.
- Não inserir colunas "de trabalho" ou rascunho numa aba publicada.

### 2.4 Tipos de célula

| Tipo de dado | Como formatar no Sheets | Exemplo |
|---|---|---|
| **Identificador** (`numero_contrato`, `numero_processo`, `exercicio`, `ano`, `mes`, `matricula`, `cnpj`, `cpf_ou_cnpj`) | **Texto simples** (`Formatar → Número → Texto simples`) | `1/2023`, `TC-005147.989.24`, `2026` |
| **Data** | Data, formato **`dd/mm/aaaa`** (único formato em todas as planilhas) | `10/01/2023` |
| **Data + hora** | Data e hora, `dd/mm/aaaa hh:mm` | `31/08/2026 14:20` |
| **Valor monetário** | **Número puro**, sem `R$` na célula, 2 casas decimais. A página formata como `R$` na exibição. | `50000.00` (não `R$ 50.000,00`) |
| **CPF** (pessoa física) | Gravado **já mascarado**: `***.456.789-**`. O Apps Script mascara sozinho ao digitar (11 dígitos → máscara). | `***.456.789-**` |
| **CNPJ** (pessoa jurídica) | Completo — dado público. | `12.345.678/0001-90` |
| **Campo vazio** | Célula **vazia**. Nunca `-`, `N/A`, `---`, `0` no lugar de "não há". | |

> **Por que CPF mascarado na origem:** o CSV publicado contém exatamente o que
> está na célula. Se o CPF completo estiver na planilha, ele fica público na
> URL do CSV — a máscara do site é só cosmética. A proteção real (LGPD) é
> guardar já mascarado. A `mascararCPF()` nas páginas continua como rede de
> segurança. Vale para toda planilha com CPF (contratos, servidores,
> remuneração…).

> **Por que identificador como texto:** se `1/2023` ficar como data, o Sheets
> guarda internamente `01/01/2023`. O código só funciona pela formatação de
> exibição — e qualquer mudança de formato quebra ordenação e filtros **sem
> aviso**.

### 2.5 Publicação na web

- Publicar **somente as abas que a página usa** e **somente as colunas
  públicas**. "Publicar na web" expõe tudo que estiver marcado.
- Cada aba publicada gera uma URL própria com `&gid=NÚMERO`. Guardar essas
  URLs (seção 6).
- Revisar a cada nova aba: nenhuma coluna interna / rascunho foi exposta?

---

## 3. Divisão por abas de ano

Todas as planilhas de acervo são divididas por ano para não crescerem
indefinidamente (download grande + travamento do navegador). A página carrega
o ano corrente por padrão e oferece um seletor para anos anteriores.

**Critério de qual ano (por planilha) — ver seção 6.** Ex.: em `contratos` a
linha vai para a aba do **ano do número do contrato** (`numero_contrato =
5/2023` → aba `2023`), não do ano da assinatura.

Planilhas que são *retrato do momento* (ex.: `servidores` ativos) podem ficar
em aba única — indicado na seção 6.

---

## 4. Publicar a aba de um ano novo

Fluxo único, do começo ao fim. "Publicar na web" é **independente** do botão
*Compartilhar* — a planilha pode continuar **Restrita** (ninguém acessa o
arquivo em si); só o CSV da aba publicada fica público.

1. **Criar a aba** — menu **Transparência → Criar aba do próximo ano** (com
   o Apps Script instalado). Clona a estrutura da aba mais recente, limpa os
   dados, põe a aba nova na frente e já grava o carimbo em A1. Conferir só
   os cabeçalhos da linha 2 (vêm da cópia) e lançar os dados a partir da
   linha 3.
2. **Publicar a aba** — `Arquivo → Compartilhar → Publicar na web` → aba
   **Link** → no 1º seletor, trocar "Documento inteiro" pela aba do ano
   novo (⚠️ nunca deixar em "Documento inteiro" — expõe todas as abas,
   inclusive rascunhos) → no 2º seletor, **Valores separados por vírgula
   (.csv)** → **Publicar** → marcar **"Republicar automaticamente quando
   alterações forem feitas"** (sem isso o CSV congela na versão publicada).
3. **Copiar a URL publicada** (formato
   `https://docs.google.com/spreadsheets/d/e/2PACX-.../pub?gid=NÚMERO&single=true&output=csv`
   — o `gid` identifica a aba) e colar no objeto `FONTES_POR_ANO`, no topo
   do `<script>` da página correspondente (seção 6 indica o arquivo).
   Commitar.
4. **Conferir a página no ar** — o seletor de ano mostra o ano novo e a
   tabela carrega.

> Sem os passos 2–3 a página **não enxerga** o ano novo — ela só conhece as
> URLs que estão no código. Passo a passo com capturas de tela:
> `COMO-ADICIONAR-UM-ANO.md`, no repositório `tabelas_transparencia`.

**Sem o Apps Script instalado**, criar a aba na mão: nome = ano com 4
dígitos, A1 em branco (carimbar depois por *Transparência → Carimbar
agora*), linha 2 = cabeçalhos copiados de outra aba sem alterar nada,
arrastar para a primeira posição — depois seguir do passo 2 acima.

**Despublicar uma aba** (se precisar tirar do ar): mesma tela → **Conteúdo
publicado e configurações → Parar de publicar**. A página que aponta pra
aquela URL passa a mostrar erro de carregamento — remover também a entrada
de `FONTES_POR_ANO`.

---

## 5. O carimbo de atualização (Apps Script)

Cada planilha tem um script instalado (`Extensões → Apps Script`) que grava
o texto `DADOS_ATUALIZADOS_EM: dd/mm/aaaa hh:mm` na célula **A1** — com o
**mesmo valor** em todas as abas de ano, se a planilha for dividida por ano.
Fuso `America/Sao_Paulo`. Nunca digitar esse valor à mão.

O selo verde "dados atualizados em…" nas páginas lê esse valor e significa
*"a planilha foi conferida/atualizada nesta data"* — não "as linhas deste
ano mudaram". Por isso o mesmo carimbo em todas as abas, inclusive anos
antigos.

O script também mascara CPFs (LGPD) na origem e traz um menu **Transparência**
com **Criar aba do próximo ano** e **Carimbar agora**. Código-fonte, agenda de
reforço por planilha e passo a passo de instalação: repositório privado
[tabelas_transparencia_scripts](https://github.com/cm-marilia/tabelas_transparencia_scripts).

---

## 6. Dicionário de colunas por planilha

> `[texto]`, `[data]`, `[número]`, `[url]` indicam o tipo esperado (seção
> 2.4). Cabeçalhos em `snake_case`, minúsculo, por extenso, sem contração.

### contratos — dividir por: ano do `numero_contrato`

| Coluna | Tipo | Observação |
|---|---|---|
| `numero_contrato` | texto | padrão `M/AAAA`, ex. `1/2023` |
| `cpf_ou_cnpj` | texto | |
| `razao_social` | texto | |
| `objeto_contrato` | texto | |
| `data_assinatura` | data | |
| `valor_contrato` | número | sem `R$` |
| `data_fim_vigencia` | data **ou** texto | data `dd/mm/aaaa`, ou `INDETERMINADA`. Se `situacao_contrato = Rescindido`, esta é a data da rescisão (não a vigência original). |
| `situacao_contrato` | texto | **vazio** = curso normal (a página calcula "Vigente"/"Encerrado" pela data). `Rescindido` = encerrado antecipadamente por descumprimento — fato **permanente**, não se apaga quando a sanção correspondente vence. Sem coluna de motivo: o motivo fica em `empresas_sancionadas.motivo_sancao` e no PDF via `link_processo_sapl`. |
| `modalidade_licitacao` | texto | |
| `fiscal_contrato` | texto | |
| `link_pdf_contrato` | url | |
| `link_processo_sapl` | url | |

**Regra para preencher `data_fim_vigencia` de um contrato rescindido:** se a
Portaria de penalidade cita a data real da rescisão, usar essa data; se não
cita nenhuma, usar a data da própria Portaria. Precedentes:

| nº | `data_fim_vigencia` gravada | Portaria |
|---|---|---|
| 55/2023 Petromar | 21/03/2024 (data da Portaria) | 11/2024 |
| 57/2023 Web Mídias | 17/04/2024 (rescisão real) | 15/2024 |
| 25/2024 Lucas Toniate | 30/07/2024 (rescisão real) | 23/2024 |
| 6/2025 Soretto | 06/06/2025 (data da Portaria) | 7/2025 |
| 51/2024 Flex Services | 31/08/2026 (data da Portaria) | 18/2026 |

> 3 contratos com `data_fim_vigencia = INDETERMINADA` (32/2023, 33/2023,
> 40/2023 — energia CPFL e Correios) são legítimos, ficam como estão.

### diárias — dividir por: ano da `data_partida`

| Coluna | Tipo | Observação |
|---|---|---|
| `nome_servidor` | texto | |
| `cargo` | texto | traz "Ver. Fulano" — usado pelo filtro de gabinete |
| `quantidade_diarias` | texto | ex. `2 DIÁRIAS E 2/3 DE DIÁRIA` |
| `data_partida` | data | ano dela define a aba |
| `data_retorno` | data | |
| `valor_diarias` | número | sem `R$` |
| `valor_adiantamento` | número | `0` gravado como célula vazia (regra 2.4) |
| `dotacao` | texto | só `Corpo Administrativo` ou `Corpo Legislativo` |
| `local_destino` | texto | |
| `cidade_destino` | texto | |
| `motivacao_viagem` | texto longo | |

> Linhas da mesma viagem repetidas com `dotacao` diferente são **rateio
> real**, não dupla contagem — manter as duas.

Planilha auxiliar `gabinetes` — 2 colunas: `gabinete` [texto] |
`legislatura` [texto, `"20"` ou `"21"`]. Alimenta o filtro "Gabinete" da
página (só mostra os vereadores da legislatura carregada).

Prompt pronto para lançar novos pareceres de diárias (PDF → linhas):
`prompt-lancar-diarias.txt`, na raiz do repositório `tabelas_transparencia`.

### empresas_sancionadas — aba única (não divide por ano)

A página só mostra sanções **vigentes** por padrão (compara
`data_fim_sancao` com hoje); isso exige todas as linhas juntas — dividir por
ano quebraria a lógica.

| Coluna | Tipo | Observação |
|---|---|---|
| `cpf_ou_cnpj` | texto | CNPJ completo; CPF (11 díg.) mascarado pelo Apps Script |
| `razao_social` | texto | |
| `numero_contrato` | texto | padrão `M/AAAA` ou `NN/AAAA` |
| `objeto_contrato` | texto | resumido (ex.: `Aquisição de café em grãos`) |
| `motivo_sancao` | texto | `Inexecução contratual` em todas as linhas até hoje |
| `data_fim_sancao` | data | vazio = sanção sem prazo (sempre exibida) |
| `observacao` | texto longo | narrativa da Portaria — fórmula fixa, ver `prompt-lancar-apenados.txt` na raiz do repositório `tabelas_transparencia` |

### estagiarios — aba única (não divide por ano)

| Coluna | Tipo |
|---|---|
| `nome_estagiario` | texto |
| `data_admissao` | data |
| `data_desligamento` | data, pode ser vazio |

### julgamentos_contas_camara — aba única (não divide por ano)

| Coluna | Tipo |
|---|---|
| `exercicio` | texto |
| `processo` | texto |
| `julgamento` | texto |
| `transito_julgado` | data |
| `itens_irregulares` | texto, pode ser vazio |

### painel_manifestacoes — dividir por: ano da `data_abertura`

| Coluna | Tipo |
|---|---|
| `tipo_manifestacao` | texto |
| `assunto` | texto |
| `canal_entrada` | texto |
| `data_abertura` | data |
| `municipio` | texto |
| `data_conclusao` | data, pode ser vazio |

### painel_orcamentario — dividir por: `ano`

| Coluna | Tipo |
|---|---|
| `ano` | texto |
| `mes` | texto, `JAN`..`DEZ` |
| `duodecimo_previsto` | número |
| `duodecimo_repassado` | número |
| `valor_empenhado` | número |
| `valor_liquidado` | número |
| `valor_pago` | número |
| `resultado` | número |

### remuneracao_detalhada_servidores — dividir por: `ano`

| Coluna | Tipo |
|---|---|
| `ano` | texto |
| `mes` | texto |
| `matricula` | texto |
| `nome_servidor` | texto |
| `vencimentos_brutos` | número |
| `referencia` | texto |
| `vantagens_pessoais` | número |
| `outras_verbas_remuneratorias_legais_judiciais` | número |
| `verbas_indenizatorias_eventuais` | número |
| `auxilio_saude_alimentacao` | número |
| `abono_permanencia` | número |
| `redutor` | número |

> Planilha de origem tem 16 colunas — as 4 restantes (13–16) ainda não
> mapeadas, ver pendências.

### servidores — aba única (retrato do quadro atual)

| Coluna | Tipo |
|---|---|
| `nome_funcionario` | texto |
| `cargo` | texto |
| `lotacao` | texto |
| `data_admissao` | data |
| `data_demissao` | data, vazio = ativo |
| `jornada` | texto |

---

## 7. Pendências e diagnóstico

Itens ainda em aberto, por planilha — mover para o histórico (ou apagar)
assim que resolvidos.

- **remuneracao_detalhada_servidores** — 4 colunas da planilha de origem
  (13–16) ainda não mapeadas para o dicionário da seção 6.
- **servidores** — a planilha de origem tem uma 7ª coluna vazia, sem
  cabeçalho, a remover.

---

## 8. Checklist ao mexer numa planilha

- [ ] Linha 1 = só o carimbo em A1; linha 2 = cabeçalhos; dados a partir da 3.
- [ ] Cabeçalhos em `snake_case`, sem acento — se renomeou algum, avisou o código e atualizou a seção 6.
- [ ] Identificadores formatados como **Texto simples**.
- [ ] Datas todas em `dd/mm/aaaa`.
- [ ] Valores monetários como número, sem `R$`.
- [ ] **CPFs mascarados** na planilha (`***.456.789-**`); CNPJ completo.
- [ ] Campos "sem informação" = célula vazia.
- [ ] Publiquei **a aba**, não "Documento inteiro"; nenhum rascunho no ar
      (*Publicar na web → Conteúdo publicado* lista o que está no ar).
- [ ] Só colunas públicas na aba publicada — nenhuma coluna interna/rascunho exposta.
- [ ] Aba nova de ano: publicada, `gid` anotado, URL adicionada ao HTML, página no ar conferida.
