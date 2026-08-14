# Contexto do repositório FERRAMENTAS

> Registro do estado do projeto e das decisões tomadas, pra retomar o trabalho em
> sessões futuras sem precisar reconstruir o raciocínio do zero.

## O que é este repositório

Portal estático (`index.html`) de ferramentas de obra/planejamento, sem build nem
dependências — cada ferramenta é um arquivo HTML standalone (HTML+CSS+JS inline).
Publicado via GitHub Pages em `mateusm23/FERRAMENTAS` (repositório **público** —
nunca commitar dados reais de obra/cliente, só código).

## Estrutura de pastas (estado atual)

```
ferramentas/
├── index.html                                  ← portal, lista os cards de cada ferramenta
├── Agilean/
│   ├── Programação Semanal/
│   │   ├── MONTAGEM DE PROGRAMAÇÃO SEMANAL v2.html   (ativa, card visível)
│   │   └── MONTAGEM DE PROGRAMAÇÃO SEMANAL.html      (HISTÓRICO, card oculto)
│   ├── Análise de Peso Financeiro/
│   │   └── ANALISE DE PESO FINANCEIRO.html           (ativa, card visível)
│   ├── Análise Física do Projeto/
│   │   ├── ANALISE FISICA DO PROJETO.html            (v1, backup, não linkada)
│   │   ├── ANALISE FISICA DO PROJETO v3.html         (card oculto — obsoleta, sem gráficos)
│   │   └── ANALISE FISICA DO PROJETO v4.html         (ativa, card visível — tem gráficos)
│   └── Relatório Semanal/
│       ├── RELATORIO SEMANAL AGILEAN.html            (ativa, card visível)
│       └── base de teste/                             (XML real de teste — nunca commitar, ver .gitignore)
├── Obsoletos/
│   └── ANALISE FISICA DO PROJETO v2.html             (arquivada, fora do portal)
├── Microsoft Project/
│   ├── Relatório Semanal/RELATORIO SEMANAL.html      (ativa, card visível — versão sem plugin Agilean)
│   └── Linha de Balanço/gerador-linha-de-balanco.html (ativa, card visível)
├── Prevision/
│   └── Programação Semanal/PROGRAMACAO SEMANAL PREVISION.html (ativa, card visível)
└── Outros/
    ├── Efetivo de Obra/
    │   ├── EFETIVO DE OBRA.html               (ativa, card visível — única ferramenta sem CDN, tudo inline)
    │   └── base de teste/                       (arquivos reais de exemplo, nunca commitar, ver .gitignore)
    └── Chamada de Aporte Semanal/
        ├── CHAMADA DE APORTE SEMANAL.html      (ativa, card visível, gera PDF a partir do Excel)
        └── Modelo_Chamada_de_Aporte_Semanal.xlsx (modelo pra download, clone sanitizado do arquivo real - ver seção abaixo)
```

## Cards do portal (`index.html`) — o que está visível/oculto e por quê

- **Visíveis**: Programação Semanal (Agilean v2), Análise de Peso Financeiro,
  Análise Física do Projeto (v4), Relatório Semanal de Obra (Agilean e MS
  Project), Gerador de Linha de Balanço, Programação Semanal (Prevision),
  Efetivo de Obra (seção **Outros**, nova em 2026-08-01), Chamada de Aporte
  Semanal (seção **Outros**, nova em 2026-08-10).
- **Ocultos** (`style="display:none"` no `<a class="tool-card">`):
  - Programação Semanal **HISTÓRICO** — versão anterior, mantida só como referência.
  - Análise Física do Projeto **v3** — obsoleta, substituída pela v4 (que tem
    gráficos interativos Chart.js: Curva S e barras mensais). Ficou oculta em
    2026-07-13 depois de confirmar com o usuário que as duas eram a mesma
    ferramenta em estágios diferentes (mesmo tipo de arquivo `.xlsx/.xls`
    exportado do Agilean, mesma descrição interna).
- **Cuidado histórico**: o commit `b4c89b2` ocultou dois cards de uma vez
  (HISTÓRICO + Peso Financeiro). Ocultar o Peso Financeiro foi **erro** — ele
  não é duplicata de nada, é a única ferramenta cujo fluxo é colar a tabela do
  **plugin Agilean no Excel** (opção "Mensal" → "Base"), diferente de v3/v4 que
  recebem upload de arquivo `.xlsx` exportado. Foi reexibido em 2026-07-13.
  Se aparecer outro card oculto sem explicação clara, desconfiar e checar o
  `git log -- index.html` antes de assumir que a decisão foi correta.
- **Largura dos cards**: `.grid` usa `grid-template-columns: repeat(auto-fill,
  minmax(280px, 1fr))` (aumentado de 220px em 2026-07-13, a pedido do usuário).
- **Descrições**: reescritas em 2026-07-13 pra deixar explícita a fonte de
  dados de cada ferramenta (upload de XML/XLSX vs. colar tabela do plugin
  Excel) e, nos dois Relatórios Semanais, que R00/R01 são o mesmo cronograma
  antes/depois de medir. Sem travessão (—) nas descrições — preferência do
  usuário, usar vírgula pra encadear frases.

## Relatório Semanal de Obra (Agilean) — leitura do % de execução

Arquivo: `Agilean/Relatório Semanal/RELATORIO SEMANAL AGILEAN.html`.

Desde 2026-07-13, o `% Executado` (`t.pct`) de cada atividade não vem mais do
`PercentComplete` nativo do MS Project — vem do campo customizado do Agilean
**"Agilean: % Avanço"** (campo `Número20`, `FieldID` fixo `188743996` no XML,
função `readPct()`), que tem escala 0–100 com casas decimais (ex.: `83.62`).

- **Fallback**: se a atividade não tiver esse campo preenchido (comum em
  tarefas de resumo ou ainda sem apontamento — validado contra XML real:
  611 tarefas usavam o campo Agilean, 2338 caíram no fallback), usa o
  `PercentComplete` nativo. Decisão do usuário: preferiu esse fallback
  silencioso a tratar como 0%, pra não subestimar atividades sem apontamento
  no campo customizado.
- **Exibição vs. lógica**: o valor decimal exato é mantido para toda a lógica
  de status/comparação (`t1.pct === 100`, `> 0`, `< 100` etc. — arredondar aqui
  faria uma atividade em 99.6% aparecer como concluída). Só a exibição nas
  tabelas arredonda, via helper `fpct()`.
- **Cuidado ao mexer em campos customizados do Agilean**: o `FieldID` de um
  campo customizado (Número/Texto) só pode ser confirmado abrindo um XML real
  exportado e procurando o bloco `<ExtendedAttributes>` — não dá pra adivinhar
  pelo nome do campo. Existe também um campo legado `Texto1` com alias
  `"1Agilean: % Avanço"` (note o "1" no início) que **não** é o campo certo —
  é sobra/duplicata antiga, ignorar. O campo certo é sempre o tipo `Número`.
  O mesmo padrão já existia pro campo "Agilean: Local" (`Texto3`,
  `FieldID 188743737`, constante `AGILEAN_LOCAL_FIELD_ID`).

### Dados de teste sensíveis

`Agilean/Relatório Semanal/base de teste/` guarda um XML real (`Be Garden
(Agilean) (1).xml`) usado pra validar o mapeamento acima contra dados reais.
Está no `.gitignore` (`**/base de teste/`) — nunca sobe pro GitHub, repositório
é público e o arquivo tem cronograma completo de obra de cliente.

## Reformulação da Análise Física do Projeto (v3 → v4)

A v3 ficou pronta com a reformulação descrita abaixo, mas foi **superada pela
v4** (que adicionou gráficos interativos com Chart.js — Curva S e barras
mensais) e por isso está oculta no portal desde 2026-07-13. Mantendo o
histórico da v3 aqui só como referência de arquitetura, caso algo similar
precise ser reaproveitado:

### O que foi feito na v3

1. **Base de dados** — parser detecta colunas de data por texto de cabeçalho
   (fuzzy, tolerante a variação de nome), não por posição fixa: 3 pares de data
   por etapa (Planejado Base, Planejado, Real). Colunas de % (Contribuição e
   Avanço) também por cabeçalho, não por posição — o export real do Agilean
   insere colunas de data antes das de %, o que quebraria leitura posicional.
2. **Tela de introdução** — timeline horizontal de 6 passos ensinando a
   exportar do Agilean, zona de arraste, some após upload.
3. **Navegação lateral unificada** — menu fixo com 7 seções (Informações
   Gerais, Curva S, Previsto x Realizado, Datas Marcos, Desvios, Resumo
   Semanal, Exportar Resumo Geral), filtro e nível de agrupamento globais.
4. **Filtros fixos na sidebar** (Unidade/Local/Macro Etapa/Etapa) — componente
   custom sem dependência externa. Painel anexado direto no `<body>` (não
   dentro de `.side-nav`, que tem `overflow-y:auto` e por regra do CSS isso
   faz o eixo X virar `auto` implicitamente, clipando elemento absoluto largo
   dentro dela — não reintroduzir um painel `position:absolute` como filho
   direto da `.side-nav`).
5. **Resumo Semanal** — a base do Agilean só tem granularidade de mês; semana é
   estimada por rateio (`taxaDiária = contribuição do mês ÷ dias do mês`).
   Sempre rotulado como estimativa, não é avanço real reportado por semana.

## Efetivo de Obra (Outros) — cruzamento Cadastro x Acesso por Equipamento

Arquivo: `Outros/Efetivo de Obra/EFETIVO DE OBRA.html`. Calcula quantas pessoas
trabalharam em cada obra, por dia e por empreiteiro, cruzando dois arquivos
que o usuário sobe por drag-and-drop:

- **Cadastro de Pessoas** (.csv/.xlsx): colunas por texto de cabeçalho —
  Nome, Empresa (empreiteiro), Classificação. Não tem ID em comum com o outro
  arquivo — o cruzamento é só por **nome normalizado** (maiúsculo, sem acento,
  espaço colapsado).
- **Acesso por Equipamento** (.xls/.xlsx): relatório paginado de
  catraca/facial (testado com export do "Secullum Acesso"). Cada página repete
  cabeçalho de impressão e uma linha `EQUIPAMENTO: <nome>` — não existe coluna
  "Obra" própria, ela vem embutida no nome do equipamento (ex.:
  `facial controlid GARDEN` → obra real "BE GARDEN", `Topdata - BONIFACIO` →
  obra "BONIFACIO"). Como o texto do equipamento não corresponde 1:1 ao nome
  real da obra, a ferramenta **não tenta adivinhar** — mostra uma tela de
  confirmação manual (equipamento bruto → campo de texto editável) antes de
  gerar o relatório, com o mesmo espírito da tela de Classificação da Linha de
  Balanço. Colunas do relatório (NOME/DATA/HORA/DESCRIÇÃO) são localizadas por
  texto de cabeçalho, redetectado a cada página — não por posição fixa.

### Regra de pareamento Entrada/Saída (`pairEvents`)

Por pessoa+equipamento, eventos ordenados cronologicamente e pareados em fila
(Entrada abre, próxima Saída fecha). Decisões:

- **O turno pertence ao dia da Entrada**, mesmo cruzando meia-noite — decisão
  do usuário, pra não dividir nem duplicar a contagem de um plantão noturno
  entre dois dias. Validado contra dado real: 48 turnos atravessando meia-noite
  no arquivo de exemplo, todos com duração 13-16h (plantão noturno plausível).
- **Limite de 16h por turno** (`MAX_SHIFT_MINUTES`): sem isso, uma pessoa que
  simplesmente não bate ponto por 2-3 dias (foi embora, trocou de obra) faz o
  pareamento sequencial juntar a Entrada de um dia com a Saída de dias depois,
  gerando turnos de 70h+. Acima do limite, a Entrada vira "sem saída
  registrada" e a Saída distante vira um evento órfão "sem entrada
  registrada", em vez de 1 turno só. Threshold escolhido com folga sobre o
  maior turno noturno real observado (~13.7h).
- **Batidas repetidas em sequência** (Entrada→Entrada ou Saída→Saída, ruído
  de catraca/facial) são colapsadas: fica a primeira Entrada e a última Saída
  da sequência.
- Entrada sem Saída depois, ou Saída sem Entrada antes (comum nas bordas do
  período do relatório — 24 casos no exemplo) contam como presença no dia mas
  não somam horas.

### Tela de resultado — tabela expansível Obra > Empresa > Dia > Pessoa (reformulada em 2026-08-01)

A tela de resultado não é mais uma tabela plana — virou uma tabela por obra
(linhas = empreiteiro, colunas = dia, célula = nº de pessoas) com drill-down
inline, tudo dentro da própria tabela (sem popup/modal, pedido explícito do
usuário: "sem pop up"). Lógica em `buildPivot()`, substituiu a antiga
`buildResumo()` (removida).

- **Clicar numa célula** (empreiteiro × dia) insere uma `<tr>` embaixo com a
  lista de pessoas daquele dia. **Clicar numa pessoa** abre, dentro da mesma
  célula (sem nova linha de tabela — só um `<div>` que aparece/some), o
  **memorial de cálculo**: cada intervalo Entrada→Saída pareado com a duração,
  e turnos que cruzam meia-noite mostram explicitamente
  `(turno noturno, saída em DD/MM)` usando o campo `diaSaidaISO` do turno (a
  data real da Saída, guardada à parte do `diaISO` que é sempre o dia da
  Entrada). Só um detalhe aberto por vez por linha de empresa — abrir outro
  fecha o anterior.
- **Cada bloco de obra tem uma linha TOTAL** (soma das empresas daquele dia),
  e se houver mais de uma obra sem filtro ativo, um bloco final **Total Geral**
  soma todas as obras — pedido do usuário: mostrar as obras separadas mas com
  totalizador entre elas.
- **Busca por pessoa** (`buscarPessoa()`): campo de texto acima da tabela,
  não mexe na tabela principal — mostra um bloco à parte com o nome, obra,
  empresa e um mini-grid de horas por dia (pra responder "em quais dias essa
  pessoa trabalhou"). Se o texto bater com mais de uma pessoa diferente
  (ex.: "silva"), mostra botões pra escolher qual antes de exibir o resultado.
  Clicar num dia do resultado da busca abre o mesmo memorial de cálculo.
- **Mínimo de tempo pra contar como presença** (tela de configuração, campo
  `input-minimo`, formato `H:MM`): pessoa cujo total de minutos **medidos**
  naquele dia (`minutosMedidos`, soma dos turnos com Entrada E Saída) fica
  abaixo do mínimo não entra no headcount da célula, mas continua aparecendo
  no drill-down (marcada "abaixo do mínimo", cinza) — nada fica escondido, só
  não conta no número. **Importante**: o mínimo só se aplica quando existe
  medição (`temMedicao === true`); gente com só batida incompleta (sem
  entrada ou sem saída, `minutosMedidos` não confiável) sempre conta,
  independente do mínimo — não dá pra aplicar "tempo mínimo" em cima de uma
  duração que não foi medida. Em branco = 0 = sem filtro, não muda
  comportamento de quem não configurar nada.
- **Empresa não cadastrada**: uma linha só por obra, nome fixo
  `EMPRESA NÃO CADASTRADA`, fundo amarelo claro + badge, com um botão
  "Ver lista de nomes" que expande (inline, mesma mecânica de drill-down) uma
  lista simples só de nomes + nº de turnos, pro usuário copiar e atualizar o
  Cadastro real por fora da ferramenta depois. **Isso substituiu** a versão
  anterior (painel com campo de texto pra digitar a empresa de cada pessoa na
  hora, recalculando ali) — removida a pedido do usuário porque o valor
  digitado se perdia ao fechar a ferramenta; o fluxo correto é atualizar o
  Cadastro de verdade e re-processar.
- A coluna Classificação do Cadastro continua um filtro configurável na tela
  de configuração (checkboxes) — pessoas sem cadastro nenhum não são afetadas
  por esse filtro (não têm Classificação pra filtrar).

### Decisão manual (Automático / Sim / Não) por pessoa+dia (2026-08-01)

Casos ambíguos — batida incompleta (sem Entrada ou sem Saída) ou turno abaixo
do mínimo configurado — entram automaticamente numa regra padrão, mas o
usuário pode **sobrepor manualmente** essa decisão pra qualquer pessoa em
qualquer dia, direto no memorial de cálculo: 3 botões, **Automático / Sim,
contar / Não, não contar**. Motivo do usuário: às vezes uma pessoa "só passou
rapidinho" e ele quer decidir caso a caso se aquilo conta como dia trabalhado.

- Override guardado em `state.overridesManuais` (chave
  `obra||empresa||dia||nomeNorm` → `'MANUAL_CONTA'` ou `'MANUAL_NAO_CONTA'`),
  aplicado dentro do próprio `buildPivot()` (parâmetro `overridesManuais`) —
  ele decide `entry.conta`/`entry.decisao` ali, então qualquer rebuild futuro
  (ex.: iria acontecer se "Gerar Relatório" fosse clicado de novo) já nasce
  respeitando os overrides ativos. Zerado em "Nova análise".
- **A tela não é re-renderizada por completo quando o usuário troca a
  decisão** — de propósito, pra não fechar a célula/memorial que ele estava
  olhando. Em vez disso, `aplicarDecisaoManual()` muda o objeto `entry`
  direto (o mesmo objeto é compartilhado por referência entre
  `pivot.porPessoaDia` e a árvore `pivot.obras[].empresas[].porDia[].pessoas`,
  então mutar um só já reflete em ambos os lugares) e propaga a diferença
  (+1/-1) manualmente pros números já desenhados na tela: célula da
  empresa×dia (`data-cell-key`), linha TOTAL da obra (`data-total-cell`),
  Total Geral (`data-total-geral-cell`) e o KPI "Pessoas no efetivo"
  (`#kpi-pessoas-efetivo`). Se decidir mexer nessa lógica de novo, cuidado pra
  não esquecer de atualizar um desses 4 pontos — não tem teste automático que
  pegue isso, só validado manualmente com Playwright clicando de verdade.
- Excel exportado ganhou a coluna **"Como foi decidido"** na aba Detalhado
  (Automático / Manual - contou / Manual - não contou), separada da coluna
  "Contabilizado no efetivo" (Sim/Não) — pedido explícito do usuário de
  rastrear isso no relatório final.

### Tela de Pendências (2026-08-01)

Depois de usar a decisão manual, o usuário reclamou que precisava abrir célula
por célula pra achar os casos ambíguos — pediu uma tela só com o que precisa
revisar. Virou uma segunda "view" dentro da tela de resultado (botões
**📋 Tabela / ⚠ Pendências pra revisar**, alternando `display` de dois `<div>`
irmãos — `#pivot-view` e `#pendencias-view` —, a tabela principal nunca é
destruída, só escondida, o que importa porque os updates cirúrgicos da decisão
manual (`data-cell-key`, `data-total-cell` etc.) continuam funcionando em
elementos escondidos).

- **O que entra na lista** (`ehPendencia(e)`): batida incompleta
  (`!e.temMedicao`) OU turno abaixo do mínimo configurado
  (`e.minutosMedidos < state.minMinutos`) — os dois motivos que fazem a régua
  automática não ter certeza. O badge no botão (`#badge-pendencias`) conta só
  as que **ainda estão em `AUTOMATICO`** (não decididas manualmente).
- Cada card reaproveita o **mesmo `renderMemorial()`** usado no drill-down da
  tabela — mesmo componente, mesmos botões Automático/Sim/Não — só que exibido
  direto na lista, sem precisar abrir célula → pessoa primeiro.
- **Checkbox "Mostrar já decididas"** (desmarcado por padrão): ao decidir uma
  pendência, ela some da lista na hora (`aoDecidir` callback passado pro
  `renderMemorial`, chama `renderPendencias()` de novo) — efeito "checklist"
  proposital. Marcando a checkbox, as já decididas voltam pra lista com opacidade
  reduzida (classe `.resolvida`), útil pra revisar decisões tomadas antes.
- Filtro de obra e busca por nome próprios dessa tela (`#pend-filtro-obra`,
  `#pend-busca-nome`), independentes dos filtros da tabela principal.
- **Cuidado se mexer em `aplicarDecisaoManual`**: o `atualizarBadgePendencias()`
  e o `aoDecidir()` precisam rodar **sempre**, não só quando `p.conta` muda —
  uma pessoa que já contava por padrão (batida incompleta) pode virar
  `MANUAL_CONTA` sem alterar nenhum headcount, mas isso já é o suficiente pra
  sair da lista de pendências abertas.

### Barra de linha do tempo no memorial (2026-08-01)

Dentro do memorial (`renderTimelineBar()`, topo do `renderMemorial()`, antes
das linhas de texto), uma barra 00h–24h mostra visualmente quando a pessoa
esteve no site naquele dia, sem precisar ler os horários em texto:

- **Turno completo** (Entrada+Saída medidas): retângulo azul, posição/largura
  calculadas em % de 1440 minutos (`minutosDoDia()` extrai HH:MM da string de
  hora). Larguras muito pequenas (ex.: 8 segundos) têm um mínimo de `0.4%`
  pra não sumir visualmente.
- **Turno noturno** (cruza meia-noite, `diaSaidaISO !== dia`): o retângulo vai
  até a borda direita (100%, fim do dia), não até a hora real da saída — e
  ganha a classe `.continua`, que desenha um `»` na ponta pra deixar claro que
  continua no dia seguinte (o texto abaixo já dizia isso; a barra reforça
  visualmente).
- **Batida incompleta** (sem Entrada ou sem Saída): em vez de barra sólida,
  um trecho **hachurado laranja** (`repeating-linear-gradient` diagonal) só na
  parte que dá pra inferir — do início do dia até a saída solta, ou da entrada
  solta até o fim do dia. É propositalmente diferente visualmente da barra
  azul sólida, porque não é uma medição real, só o que dá pra saber com o
  dado que existe.
- Mesmo componente reaproveitado em todo lugar que já usava `renderMemorial`
  (drill-down da tabela, busca por pessoa, tela de Pendências) — não precisou
  duplicar nada.

### Bibliotecas via CDN (voltou atrás em 2026-08-01 — histórico abaixo)

O requisito original era um arquivo **100% autocontido, sem CDN** — a
biblioteca SheetJS chegou a ficar vendorizada inline em Base64 (`XLSX_LIB_B64`
+ `TextDecoder` + `eval` indireto, pra sobreviver a um bug real do Live
Server do VS Code que corrompia o arquivo — ver histórico completo no
`git log` do arquivo se precisar reconstruir esse raciocínio). **O usuário
pediu pra abandonar essa regra** quando decidiu usar ExcelJS pra formatar o
Excel exportado, alinhando com o padrão do resto do portal. Hoje o `<head>`
carrega as duas bibliotecas normalmente:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/exceljs/4.3.0/exceljs.min.js"></script>
```

Mesmas versões usadas em `Agilean/Programação Semanal/MONTAGEM DE
PROGRAMAÇÃO SEMANAL v2.html` (copiei o padrão de lá: `CL` de cores, `bdr()`
de borda fina, `apply()`/`estilizarCell()` pra estilo de célula, `writeBuffer()`
+ `Blob` + `<a download>` pra baixar). Isso também significa que a ferramenta
**precisa de internet** pra abrir agora (antes funcionava 100% offline) — se
o usuário reclamar disso no futuro, é porque essa troca foi deliberada, não
esquecimento.

**Testando localmente sem internet**: o Playwright deste sandbox não tem
acesso à internet real, então os testes interceptam as chamadas de CDN e
respondem com as cópias locais (`node_modules/xlsx` e `node_modules/exceljs`
baixados via `npm install` no diretório de scratch) via `page.route(...)` —
ver `cdn_stub.js` nos scripts de teste. Isso é só uma necessidade do ambiente
de teste; no navegador real do usuário, os `<script src>` batem no cdnjs
normalmente.

### Excel exportado: 3 abas com ExcelJS (2026-08-01)

Reescrito do zero pra sair da lista corrida (SheetJS puro, sem estilo) e virar
um relatório de verdade, espelhando o que já existe na tela:

- **"Resumo"** — a mesma matriz Obra→Empreiteiro×Dia da tela, um bloco por
  obra (título mesclado azul-marinho, cabeçalho de dias, linha "EMPRESA NÃO
  CADASTRADA" em amarelo clarinho, linha TOTAL por obra, e um TOTAL GERAL no
  fim se houver mais de uma obra no recorte). Primeira coluna congelada
  (`views:[{state:'frozen', xSplit:1}]`).
- **"Tabela Detalhada"** — usa o **agrupamento nativo do Excel** (outline —
  os botões `+`/`-` na margem esquerda) em vez de reimplementar o drill-down:
  `row.outlineLevel = 0..4` (Obra→Empreiteiro→Dia→Pessoa→Turno) e
  `row.hidden = true` pra tudo com nível ≥ 1, então o arquivo abre só com
  Obra/Empreiteiro visíveis (pedido do usuário: "recolhida só nível 1 e 2" —
  na prática isso já esconde 2/3/4 em cascata, já que colapsar um nível
  esconde tudo aninhado nele). Precisa de
  `worksheet.properties.outlineProperties = {summaryBelow:false,
  summaryRight:false}` porque a linha-resumo (Obra/Empreiteiro/Dia) fica
  **acima** dos detalhes dela, não abaixo (padrão do Excel é `summaryBelow:
  true`). Cor de fundo diferente por nível (navy/pale/g1/branco), e nível 4
  (turno) fica com fundo âmbar quando é batida incompleta ou turno noturno —
  mesma lógica visual do memorial da tela. **Não dá pra abrir Excel de
  verdade neste ambiente pra conferir visualmente os botões `+`/`-`** —
  validado programaticamente relendo o arquivo gerado com o próprio ExcelJS
  (`row.outlineLevel`, `row.hidden`, cores) e confiando no formato OOXML
  padrão pro resto.
- **"Ponderações"** — duas tabelas na mesma aba: casos ambíguos (batida
  incompleta OU abaixo do mínimo, **os dois tipos**, resolvidos ou não —
  pedido explícito do usuário foi incluir ambos) com a coluna Decisão
  (Automático/Manual-contou/Manual-não contou, linha em amarelo se ainda
  `AUTOMATICO`), e a lista de funcionários sem empresa.

**Exportar tela filtrada ou tudo**: se tiver filtro de Obra ou Empreiteiro
ativo na tela ao clicar "Exportar Excel", abre um modal (`#modal-export`)
perguntando "Somente filtrado" ou "Exportar tudo" — sem filtro nenhum, exporta
direto sem perguntar. `obrasParaExport(modo)` devolve uma cópia rasa das
obras com `empresas` filtradas, mas **nunca recalcula `totalPorDia`** — a
linha TOTAL de cada obra sempre reflete o total real dela, mesmo filtrando por
empreiteiro (mesmo comportamento já validado na tela pra não confundir
"filtrei uma empresa" com "essa é a obra inteira").

### Overlay de carregamento com duração mínima artificial (2026-08-01)

`comCarregando(mensagens, duracaoMs, fn)` — pedido explícito do usuário: o
processamento de verdade é rápido demais (dados de teste processam em
milissegundos), e ele quer o overlay segurando por mais tempo de propósito
("dar uma sensação de processamento"), com mensagens que trocam ao longo do
tempo pra parecer um pipeline de várias etapas:

- Ler Cadastro / ler Acessos: **5000ms mínimo**, mensagem única.
- Gerar Relatório: **10000ms mínimo**, 4 mensagens rotativas (`Compilando
  dados...` → `Cruzando com o Cadastro...` → `Tratando turnos noturnos...` →
  `Calculando pendências e totais...`).
- Exportar Excel: **10000ms mínimo**, outras 4 mensagens rotativas.

Implementação: guarda o horário de início, roda `fn()` de verdade (que
normalmente termina bem antes do tempo mínimo), e só esconde o overlay depois
de completar `duracaoMs` no total (`await setTimeout(faltando)`) — ou seja,
**o tempo mínimo nunca é pulado mesmo que o processamento real seja
instantâneo**, mas também nunca trava mais que o necessário se o processamento
real demorar mais que o mínimo configurado (usa `Math.max`). Continua tendo o
`setTimeout(…, 30)` inicial antes de rodar `fn()`, pelo mesmo motivo de sempre:
dar tempo do navegador pintar o spinner antes de travar a thread com trabalho
síncrono pesado (parsing do Excel, `buildPivot`).

**Parâmetro `aoFinalizar(resultado)`** (4º argumento, opcional): só roda
**depois** do tempo mínimo todo ter passado, logo antes de esconder o overlay.
Existe especificamente pro export Excel — na primeira versão, `fn()` já fazia
`a.click()` pra disparar o download, e como `fn()` termina quase instantâneo,
o download começava (barra de download do navegador aparece, é um efeito
visível *fora* da nossa página, não fica escondido atrás do overlay) enquanto
a tela ainda mostrava "Finalizando o arquivo..." por mais alguns segundos —
quebrava a sensação de "terminou = já baixou" (usuário via o download começar
antes da animação acabar). Correção: `fn()` só monta o workbook e devolve o
buffer (`return await wb.xlsx.writeBuffer()`); o `a.click()` que dispara o
download de verdade virou o `aoFinalizar`, que só roda depois do tempo mínimo
— validado medindo com Playwright que o evento `download` dispara no mesmo
instante (0ms de diferença) em que o overlay some.

### Tutoriais na tela de upload (2026-08-01)

Dois blocos expansíveis (`.tutorial-toggle` / `.tutorial-box`) abaixo dos
dropzones, cada um explicando um arquivo:

- **Cadastro de Pessoas**: mostra a estrutura esperada (tabela de exemplo) e
  onde conseguir (`Cadastros → Pessoas` no portal do Secullum). **De
  propósito não inclui a URL específica** do portal Secullum da empresa
  (teria o nome do tenant/cliente, ex. `.../Bekaa/...`) — o repositório é
  público, e isso identificaria a empresa. Confirmado com o usuário antes:
  ele pode pedir pra incluir a URL depois se quiser, mas o padrão ficou sem.
  **Dados de exemplo da tabela são fictícios** (`FULANO DA SILVA SANTOS`
  etc.) — nunca usar nomes reais do arquivo de teste em conteúdo que vai pro
  HTML commitado, mesmo que pareçam inofensivos.
- **Acesso por Equipamento**: passo a passo exato validado com o usuário
  (screenshot da tela de configuração do relatório no Secullum): Relatórios →
  Acessos → Acesso por Equipamento → todos os filtros em Todos/Todas exceto
  "Exibir somente acessos liberados" (marcado) → Concluir → Salvar → formato
  Microsoft Excel.

### Dados de teste

`Outros/Efetivo de Obra/base de teste/` guarda os 2 arquivos reais de exemplo
usados pra validar a lógica acima (cadastro pequeno de propósito, só pra
testar o cruzamento — não é a base completa de nenhuma obra real). Coberto
pela regra `**/base de teste/` do `.gitignore`.

## Chamada de Aporte Semanal (Outros)

Arquivo: `Outros/Chamada de Aporte Semanal/CHAMADA DE APORTE SEMANAL.html`. Sobe
a planilha do modelo "Chamada de Aporte Semanal" e gera um PDF, replicando o
visual real do Excel (cores, grade, hierarquia) em vez de um layout próprio,
a pedido explícito do usuário depois de ver a primeira versão redesenhada.

- **Extração por rótulo, não por endereço fixo de célula** (2026-08-14):
  a primeira versão lia a aba "Resumo Gerencial" por endereço fixo (`C7`,
  `E36` etc.). Quebrou na primeira semana de uso real: a planilha real do
  financeiro (SAGA DENZA) diverge do modelo — tem **dois** blocos de
  identificação parecidos ("GERENCIAMENTO DE OBRAS", com dados da própria
  Trinus, e "DADOS DO EMPREENDIMENTO", com dados do projeto/cliente) e o
  usuário precisou excluir linhas de cada bloco, o que desalinhou todo
  endereço fixo abaixo deles e fez o PDF sair com dado errado/em branco sem
  nenhum erro visível. Reescrito pra localizar cada campo pelo **texto do
  rótulo** (coluna B, dentro do intervalo de linhas da seção correspondente
  — `locateSections()`/`findFieldRow()`/`fieldVal()`), e a tabela "Controle
  EAP" pelo **texto do cabeçalho de coluna** (`DESCRIÇÃO`, `NÍVEL` etc. —
  `extractEAP()` acha a linha de cabeçalho procurando "DESCRICAO" e mapeia
  cada coluna esperada pelo texto encontrado naquela linha, em vez de
  assumir letra de coluna fixa). A aba "Detalhado" manteve letra de coluna
  fixa (nunca divergiu do modelo em produção) mas passou a achar a linha de
  cabeçalho dinamicamente (procurando "ATUALIZACAO") em vez de assumir linha
  5. Card "Identificação" agora lê do bloco "DADOS DO EMPREENDIMENTO"
  (antes lia, por engano, do bloco da Trinus); "Gestor Vertical" cai de
  volta pro bloco "GERENCIAMENTO DE OBRAS" quando a planilha não tem essa
  linha no bloco do empreendimento (caso real do SAGA DENZA). `extractEAP()`
  agora lança erro explícito se não achar as colunas "DESCRIÇÃO" ou "NÍVEL"
  (falha alto e claro em vez de silenciosamente misturar dados). Validado
  rodando a extração completa + geração de PDF fora do navegador (Node +
  pacote `pdfmake` server-side) contra o modelo e contra uma planilha real
  de produção, e conferindo visualmente (`pdftoppm`) que os valores batem
  célula a célula com o arquivo de origem.
- **Biblioteca de PDF**: usa **pdfmake** (`cdn.jsdelivr.net`), diferente do
  padrão `jsPDF + html2canvas` usado no Relatório Semanal (Agilean/MS
  Project). Decisão deliberada, não inconsistência: pdfmake gera PDF vetorial
  de verdade (texto selecionável, arquivo pequeno) e tem suporte nativo a
  `headerRows` repetindo em cada página, essencial pra uma tabela de EAP com
  300+ linhas espalhadas por várias páginas. `jsPDF+html2canvas` rasteriza a
  tela, o que não fazia sentido aqui.
- **Cores extraídas da planilha real** (não inventadas): navy `#1F3864` /
  `#2E5395` pros títulos e cabeçalhos de tabela, amarelo `#FFF2CC` com fonte
  azul pras células de preenchimento manual, cinza `#F2F2F2` pras células
  calculadas, laranja `#C55A11` + pêssego `#FCE4D6` pro destaque do valor do
  aporte. Verificado abrindo a planilha com `openpyxl` e lendo `cell.fill`
  célula por célula, não adivinhado.
- **Coluna `NIVEL` da aba "Controle EAP"** mapeia direto pro que o usuário
  pediu como "Sintético" x "Analítico": Sintético = linhas com `NIVEL <= 3`,
  Analítico = todas. O nível pedido inicialmente foi 2, o usuário corrigiu
  pra 3 depois de ver o PDF (nível 2 sozinho ficava com poucas linhas,
  informação insuficiente pra um resumo). Cor de fundo por nível
  (`levelTint1/2/3`, degradê de azul mais forte pra mais claro) é reforço
  visual criado pra esta ferramenta, não existe assim na planilha original.
- **Coluna "Processo" da aba Detalhado**: a célula é um valor de data por
  baixo, mas o formato de exibição do Excel mostra texto tipo "17-Jan". Ler
  `cell.v` (interpretado como Date) e reformatar como `dd/mm/aaaa` fica
  errado. Correto é ler `cell.w` (a string já formatada pelo Excel) e exibir
  como texto puro, sem reformatar.
- **Repetição de título de seção em tabelas longas**: em vez de tentar fazer
  o `header` do documento pdfmake saber em qual seção lógica cada página está
  (não dá, pdfmake não expõe isso antes de paginar), a barra de título e o
  subtítulo de cada seção (ex.: "Controle de EAP - Analítico") são embutidos
  como as primeiras linhas da própria tabela (`headerRows: 2` ou `3`, função
  `titleHeaderRows()`), então repetem automaticamente em toda página que a
  tabela ocupar. Mais simples e confiável do que qualquer tentativa de
  rastrear posição de página.
- **Cuidado ao trocar orientação da página no meio do documento**
  (`pageOrientation: 'landscape'` numa seção específica, usado nas 3 tabelas
  vindas da aba Detalhado): colunas de largura fixa que somam menos do que a
  largura útil real da página (761.89pt numa A4 paisagem com margem de 40pt)
  ainda assim vazaram 2 a 4pt da borda direita **da página**, não só da
  margem, quando a soma ficava perto do limite. Não foi totalmente
  diagnosticado (comportamento consistente entre várias tentativas, parece
  ligado especificamente à página onde a troca de orientação acontece), mas
  contornado dando bastante folga: usar `LANDSCAPE_CENTER_WIDTH = 730` (não
  761.89) como referência pra centralizar/dimensionar colunas, e preferir
  larguras fixas em vez de coluna `'*'` nessas tabelas. Validado renderizando
  o PDF de verdade com `pdfjs-dist` + `@napi-rs/canvas` num teste headless e
  medindo a posição real (`x + width`) do texto mais à direita de cada
  página contra `page.view[2]`, não só "parece certo visualmente".
- **Sem travessão longo em nada do código desta ferramenta** (HTML, CSS, JS,
  textos do PDF), pedido explícito do usuário, separado da regra já
  existente pras descrições do `index.html`. Trocado por hífen, parênteses
  ou vírgula conforme o contexto.
- **Tudo roda no navegador do usuário**: leitura do Excel (SheetJS), extração
  da logo embutida (JSZip) e montagem do PDF (pdfmake) são 100% client-side,
  nenhum dado sobe pra servidor algum, importante porque a planilha de
  origem tem informação financeira sensível (valores de contrato, saldo
  bancário, fornecedores).
- **Opções de seção configuráveis**: checkboxes (Resumo Gerencial, EAP
  Sintético, EAP Analítico, Histórico de Pagamentos, Histórico de Contratos,
  Projeção) deixam escolher o que entra no PDF antes de gerar. Uma seção
  vinda da aba Detalhado (Pagamentos/Contratos/Projeção) some sozinha do PDF
  se não tiver nenhuma linha de dado, mesmo com o checkbox marcado.
- **Fluxo de duas etapas**: arrastar/selecionar o arquivo só marca o
  `dropzone` como carregado (`.dropzone.loaded`) e habilita o botão "Gerar
  PDF"; o processamento de verdade só roda no clique do botão, não no
  drop. Pedido do usuário pra poder ajustar as seções antes de gerar.
- **Botão "Baixar planilha modelo"**: link estático (`<a href="Modelo_
  Chamada_de_Aporte_Semanal.xlsx" download>`) pro arquivo `Outros/Chamada de
  Aporte Semanal/Modelo_Chamada_de_Aporte_Semanal.xlsx`, que **é** commitado
  no repositório. Isso mudou de ideia em relação à primeira versão (que
  gerava o modelo em memória com ExcelJS, igual ao site de referência
  `mateusm23.github.io/Relatorios`) porque o usuário quis que o modelo fosse
  **exatamente** o arquivo real (mesma formatação, fórmulas, células
  mescladas, comentário de instrução na célula do saldo bancário etc.) - uma
  réplica gerada via ExcelJS nunca ia ficar 100% igual (não recria fórmulas,
  formatação condicional, comentários de célula).
  - **Como o arquivo real virou modelo público sem expor dado de cliente**:
    o arquivo original (planilha de uma obra real, cliente "SAGA DENZA" /
    "SAGA SHENZHEN") foi clonado e sanitizado por **edição cirúrgica do XML
    dentro do .xlsx** (script Python usando só `zipfile` + `re`, não
    `openpyxl`) - troca de texto em `sharedStrings.xml` (nome de
    empresa/fornecedor por genérico, incluindo referências à marca do
    cliente escondidas dentro de descrições de item, ex. "CABO HOMOLOGADO
    DENZA"), remoção do bloco `<mc:AlternateContent>` do `workbook.xml` (o
    Excel grava ali o caminho do SharePoint de onde salvou por último, que
    incluía o nome do cliente na URL), e remoção do anchor da logo do
    cliente em `drawing1.xml` (mantendo só a logo da própria Trinus).
  - **Cuidado importante, já vivido aqui**: **não usar `openpyxl` pra esse
    tipo de sanitização** (carregar com `load_workbook` e salvar de novo).
    Ele preserva a fórmula (`cell.value` volta a string `"=..."`), mas
    **descarta o valor em cache** de toda célula com fórmula ao salvar -
    testado e confirmado lendo o resultado com SheetJS depois: toda célula
    com `<f>` voltava `undefined`. Como esta ferramenta (e o Excel, até
    reabrir e recalcular) lê o valor em cache (`cell.w`/`cell.v`), isso
    quebraria o "parece preenchido" do modelo. A edição direta do XML não
    toca em nenhum `<f>`, só no `<v>` das células-alvo, preservando o cache
    de tudo que não foi tocado.
  - **Zerar a folha não zera o total sozinho**: os valores reais de orçamento
    (292 itens-folha da EAP, nível 4) e de pagamento/contrato (Detalhado)
    foram zerados no `<v>` literal, mas as fórmulas de agregação (SUMIFS por
    nível na EAP, SUM nos totais do Detalhado, cálculo do "Valor do Aporte
    Necessário" no Resumo) continuavam com o **cache antigo** mostrando o
    total real, porque ninguém reabriu no Excel pra recalcular. Precisou
    zerar o `<v>` em cache de toda a coluna ORÇADO/INCORRIDO/SALDO da EAP
    (linhas 6-332, não só as linhas-folha) e das células de fórmula do
    Resumo/Detalhado também - ao abrir esse arquivo em Excel de verdade, ele
    recalcula normal a partir das fórmulas intactas.
  - Duas células (`I106`/`I107` na EAP) vieram *self-closing* (`<c .../>`,
    sem `<v>` nenhum) em vez do padrão `<c>...<v>N</v></c>` - regex que
    assume a segunda forma decodifica errado sobre uma célula vazia e acaba
    consumindo a célula vizinha seguinte. Detectado só porque a checagem
    "essa célula não devia ser fórmula" disparou num caso real.
  - Validado gerando o PDF de verdade a partir do arquivo sanitizado e
    varrendo **todo** o `.xlsx` (todo arquivo XML dentro do zip, não só as
    3 abas usadas pela ferramenta) por nome do cliente, nomes de fornecedor
    reais e trecho da URL do SharePoint, pra garantir que nada sensível
    sobrou em aba oculta (CONFIG), metadado ou `customXml`.

## Como testar localmente

Servidor HTTP simples na raiz + Playwright (Python) pra simular upload de
arquivo e ler valores reais do DOM — sem isso, "deveria funcionar" e "funciona"
são coisas diferentes. Sempre limpar scripts/screenshots de teste ao final
(`rm -f _verify_*.py _shot_*.png`) e confirmar com `git status --short` que só
os arquivos de código relevantes mudaram.

## Deploy

`git push origin main` publica direto no GitHub Pages — sem etapa de build.
Propagação normalmente leva até ~2 min; smoke test com bypass de cache
(`curl` com query string `?cb=<timestamp>` + checar se o marcador esperado
está presente no HTML servido) confirma no ar sem depender de "parece certo".
