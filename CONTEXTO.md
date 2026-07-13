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
└── Prevision/
    └── Programação Semanal/PROGRAMACAO SEMANAL PREVISION.html (ativa, card visível)
```

## Cards do portal (`index.html`) — o que está visível/oculto e por quê

- **Visíveis**: Programação Semanal (Agilean v2), Análise de Peso Financeiro,
  Análise Física do Projeto (v4), Relatório Semanal de Obra (Agilean e MS
  Project), Gerador de Linha de Balanço, Programação Semanal (Prevision).
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
