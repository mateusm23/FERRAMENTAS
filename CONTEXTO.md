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
   - Informações Gerais (KPIs — sempre do projeto inteiro, sem filtro)
   - Curva S
   - Previsto x Realizado
   - Datas Marcos *(nova — usa os 3 pares de data)*
   - Desvios *(nova — ranking por maior atraso)*
   - Resumo Semanal *(nova, estimativa — ver método abaixo)*
   - Exportar Resumo Geral
   - Toda a trilha "Sintético" antiga foi removida (a trilha "Analítico" com
     filtro vazio já produzia o mesmo resultado — confirmado por teste).

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
