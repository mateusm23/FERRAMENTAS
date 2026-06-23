# Contexto do repositório FERRAMENTAS

> Registro do estado do projeto e das decisões tomadas, pra retomar o trabalho em
> sessões futuras sem precisar reconstruir o raciocínio do zero.

## O que é este repositório

Portal estático (`index.html`) de ferramentas de obra/planejamento, sem build nem
dependências — cada ferramenta é um arquivo HTML standalone (HTML+CSS+JS inline).
Publicado via GitHub Pages em `mateusm23/FERRAMENTAS` (repositório público).

## Estrutura de pastas

```
ferramentas/
├── index.html                              ← portal, lista os cards de cada ferramenta
├── Agilean/
│   ├── Programação Semanal/
│   ├── Análise de Peso Financeiro/
│   └── Análise Física do Projeto/
│       ├── ANALISE FISICA DO PROJETO.html      (v1, backup, não linkada no portal)
│       ├── ANALISE FISICA DO PROJETO v2.html   (publicada, link atual do portal)
│       └── ANALISE FISICA DO PROJETO v3.html   (em reformulação — ver abaixo)
└── Prevision/
    └── Programação Semanal/
```

Cada ferramenta nova ganhou sua própria subpasta (uma por ferramenta) pra facilitar
manutenção. O `index.html` aponta pra v2 até a v3 ser aprovada e substituir o link.

## Reformulação da Análise Física do Projeto (v3)

A v2 está publicada e **intocada** — toda a reformulação acontece isolada na v3.

### O que já foi feito

1. **Base de dados** — o parser agora detecta colunas de data por texto de
   cabeçalho (fuzzy, tolerante a variação de nome), não por posição fixa:
   - 3 pares de data por etapa: **Planejado Base**, **Planejado** (reprog atual),
     **Real** (`iniPlanBase/fimPlanBase`, `iniPlan/fimPlan`, `iniReal/fimReal`).
   - Corrigida uma fragilidade pré-existente: as colunas de % (Contribuição e
     Avanço) também passaram a ser detectadas por cabeçalho, não por posição —
     o export real do Agilean insere as colunas de data **antes** das de %,
     o que quebraria a leitura por posição fixa.
   - Tudo é opcional/degrada graciosamente: arquivo sem colunas de data continua
     funcionando exatamente como antes (`S.hasDatas` controla a UI).
2. **Tela de introdução** — substitui o card de upload + caixa de instruções por
   uma tela única: ícone, descrição, timeline horizontal com 6 passos (ícones
   88px, quadrado arredondado, branco com borda+sombra) ensinando a exportar do
   Agilean, zona de arraste, rodapé com autor. Some após o upload; tem botão
   "↺ Trocar arquivo" pra voltar.
3. **Navegação lateral unificada** — substituiu o seletor Sintético/Analítico
   e o sistema de abas por um menu lateral fixo com 7 seções, filtro
   (Unidade/Local/Macro/Etapa) e nível de agrupamento agora **globais**:
   - Informações Gerais (KPIs — respeita o filtro global, igual às demais)
   - Curva S
   - Previsto x Realizado
   - Datas Marcos *(nova — usa os 3 pares de data)*
   - Desvios *(nova — ranking por maior atraso)*
   - Resumo Semanal *(nova, estimativa — ver método abaixo)*
   - Exportar Resumo Geral
   - Toda a trilha "Sintético" antiga foi removida (a trilha "Analítico" com
     filtro vazio já produzia o mesmo resultado — confirmado por teste).
4. **Filtros fixos na sidebar (Unidade/Local/Macro Etapa/Etapa)** — antes
   ficavam numa barra repetida no topo de cada seção (Tom Select, biblioteca
   externa); agora vivem na própria barra lateral, abaixo de um divisor
   "Filtros" + botão "Limpar" geral, e nunca são recriados ao trocar de
   seção (só o conteúdo central re-renderiza).
   - Componente custom (sem dependência externa): botão com ícone de funil
     + mini-chips dos valores escolhidos (com elipse e tooltip pra nomes
     longos de Etapa) + painel de checkboxes com busca-livre/"Selecionar
     todas"/"Limpar" por campo. Estado continua em `_ANA_F` (arrays por
     campo; array vazio = sem filtro).
   - O painel é anexado direto no `<body>` (não dentro de `.side-nav`) e
     posicionado via JS com `position:fixed` — **importante**: `.side-nav`
     tem `overflow-y:auto`, e por regra do CSS isso faz o eixo X também
     virar `auto` implicitamente, clipando/scrollando qualquer elemento
     absoluto largo que more dentro dela. Não reintroduzir um painel
     `position:absolute` como filho direto do `.side-nav`.
   - **Informações Gerais agora respeita o filtro global** (antes era
     proposital sempre o projeto inteiro). `projecaoTermino()` foi corrigido
     pra usar o `rMes` já calculado em `curveData` (que reflete o filtro/
     renormalização) em vez de recalcular direto de `S.rows` sem filtro —
     senão a "Projeção de Término" ficaria com ritmo médio sempre global,
     inconsistente com os outros KPIs filtrados.
   - `--sidenav-w` foi de 184px pra 230px pra caber os campos de filtro.
5. **Repaginação de cores** — `--blue-light: #e8f1fc` virou variável no
   `:root` (antes era hex repetido em 3-4 lugares); nav ativo, sidenav-corte
   e estado "tem seleção" dos filtros usam essa variável; raio dos cards de
   KPI subiu de 10px pra 12px pra combinar com o painel novo de filtro.

### Resumo Semanal — método (importante lembrar)

A base do Agilean só tem granularidade de **mês**. A semana é estimada por
rateio: `taxaDiária = contribuição do mês ÷ dias do mês`, e cada semana soma
`taxaDiária × dias da linha que caem naquela semana` (uma semana pode cruzar
2 meses). Validado à mão com arquivo sintético antes de confiar no resto.
Sempre rotulado como estimativa na tela — não é avanço real reportado por semana.

### Pendente / próximos passos

- Gráficos (Chart.js) dentro de Curva S e Previsto×Real — ainda não implementado,
  ficou de fora do recorte da navegação lateral.
- Decidir se "Datas Marcos"/"Desvios" também devem ganhar gráfico.
- O painel de checkbox do filtro de Etapa não tem busca-livre (campo de texto)
  — só "Selecionar todas"/"Limpar". Com poucas etapas é suficiente, mas se a
  base tiver muitas etapas distintas, vale adicionar um campo de busca dentro
  do painel (como a variação "B" testada no protótipo de seletores).
- `EXEMPLO DE BASE DE DADOS.xlsx` (dados reais de um projeto, usado pra testar
  com dados reais) está no `.gitignore` — fica só local, nunca sobe pro GitHub
  (repositório público, dados sensíveis: nomes de obra, datas, % financeiro).
- Quando a v3 estiver pronta, atualizar o link do portal (`index.html`) de v2
  para v3.

### Como testar localmente

Servidor HTTP simples na raiz + Playwright (Python) pra simular upload de
arquivo e ler valores reais do DOM — sem isso, "deveria funcionar" e "funciona"
são coisas diferentes. Sempre limpar scripts/screenshots de teste ao final
(`rm -f _verify_*.py _shot_*.png`) e confirmar com `git status --short` que só
os arquivos de código relevantes mudaram.
