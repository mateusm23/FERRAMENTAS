# Portal de Ferramentas

Ferramentas de planejamento e controle de obra que rodam no navegador, uma por arquivo HTML, sem instalação e sem build.

**Portal publicado:** https://mateusm23.github.io/FERRAMENTAS/

Cada ferramenta recebe um arquivo que você já tem (cronograma exportado, planilha, relatório ou PDF) e devolve o resultado pronto para usar: Excel, PDF ou tabela para copiar e colar.

## Como funciona

- Cada ferramenta é um HTML autocontido, com o código e o estilo no próprio arquivo.
- O arquivo que você sobe é lido pelo navegador da sua máquina. Nenhuma ferramenta envia o arquivo para um servidor.
- O portal é publicado por GitHub Pages a partir da branch `main`, sem etapa de build.
- Algumas ferramentas carregam bibliotecas de leitura de planilha e de geração de PDF por CDN, então a primeira abertura pede internet.

## Ferramentas

### Agilean

Leem o cronograma exportado do Agilean, ou a tabela do plugin Agilean colada no Excel.

- **[Programação Semanal](https://mateusm23.github.io/FERRAMENTAS/Agilean/Programa%C3%A7%C3%A3o%20Semanal/MONTAGEM%20DE%20PROGRAMA%C3%87%C3%83O%20SEMANAL%20v2.html)** (v2 · 02/07/2026)  
  Monta a programação semanal a partir do cronograma do Agilean, com Gantt por local, peso de cada atividade e filtros cascateados (torre/pavimento/etapa), pronta pra exportar em Excel.
- **[Análise de Peso Financeiro](https://mateusm23.github.io/FERRAMENTAS/Agilean/An%C3%A1lise%20de%20Peso%20Financeiro/ANALISE%20DE%20PESO%20FINANCEIRO.html)** (v1 · 23/06/2026)  
  Cola a tabela "Base" do plugin Agilean no Excel e compara previsto x realizado, com desvio ponderado por macro etapa.
- **[Análise Física do Projeto](https://mateusm23.github.io/FERRAMENTAS/Agilean/An%C3%A1lise%20F%C3%ADsica%20do%20Projeto/ANALISE%20FISICA%20DO%20PROJETO%20v4.html)** (v4 · 24/06/2026)  
  Sobe o cronograma exportado do Agilean e gera Curva S, avanço mensal em gráfico, peso financeiro e desvio por etapa, tudo interativo.
- **[Relatório Semanal de Obra (Cronograma Agilean)](https://mateusm23.github.io/FERRAMENTAS/Agilean/Relat%C3%B3rio%20Semanal/RELATORIO%20SEMANAL%20AGILEAN.html)** (v1 · 13/07/2026)  
  Compara o R00 (antes de medir) com o R01 (depois de medir) do mesmo cronograma, sempre com base no método e na coluna de avanço do Agilean, com Local por atividade, calcula desvios de prazo e monta o relatório completo: justificativas, fotos e PDF pronto pra envio.

### MS Project

Leem o cronograma do Microsoft Project, pelas colunas nativas, sem depender do plugin Agilean.

- **[Relatório Semanal de Obra (Cronograma MS Project)](https://mateusm23.github.io/FERRAMENTAS/Microsoft%20Project/Relat%C3%B3rio%20Semanal/RELATORIO%20SEMANAL.html)** (v1 · 02/07/2026)  
  Compara o R00 (antes de medir) com o R01 (depois de medir) do mesmo cronograma, usando as colunas nativas do MS Project, calcula desvios de prazo e monta o relatório completo: justificativas, fotos e PDF pronto pra envio.
- **[Gerador de Linha de Balanço](https://mateusm23.github.io/FERRAMENTAS/Microsoft%20Project/Linha%20de%20Balan%C3%A7o/gerador-linha-de-balanco.html)** (v1 · 16/07/2026)  
  Classifica pavimentos e serviços a partir do XML do MS Project e monta o gráfico de fluxo de produção, com marcos, feriados e exportação para Excel.

### Prevision

Lê a exportação do Prevision.

- **[Programação Semanal](https://mateusm23.github.io/FERRAMENTAS/Prevision/Programa%C3%A7%C3%A3o%20Semanal/PROGRAMACAO%20SEMANAL%20PREVISION.html)** (v1 · 19/06/2026)  
  Monta a programação semanal a partir da exportação do Prevision, com o mesmo padrão de Gantt e filtros da versão Agilean.

### Outros

Não dependem de um sistema de cronograma. Cada uma lê a planilha, o PDF ou o relatório indicado na descrição.

- **[Efetivo de Obra](https://mateusm23.github.io/FERRAMENTAS/Outros/Efetivo%20de%20Obra/EFETIVO%20DE%20OBRA.html)** (v1 · 01/08/2026)  
  Refino dos relatórios gerados pelo sistema Secullum, cruza o Cadastro de Pessoas com o relatório de Acesso por Equipamento (catraca/facial) e calcula quantas pessoas trabalharam em cada obra, por dia e por empreiteiro.
- **[Chamada de Aporte Semanal](https://mateusm23.github.io/FERRAMENTAS/Outros/Chamada%20de%20Aporte%20Semanal/CHAMADA%20DE%20APORTE%20SEMANAL.html)** (v1 e v2 beta · 14/11/2026)  
  Sobe a planilha do modelo de Chamada de Aporte Semanal e gera um PDF no mesmo padrão visual do Excel: Resumo Gerencial, Controle de EAP (sintético e analítico) e histórico de pagamentos e contratos, com seções configuráveis. Abre na escolha entre a versão em uso e a versão 2, em teste.
- **[Conversor de Verbas Rescisórias](https://mateusm23.github.io/FERRAMENTAS/Outros/Conversor%20de%20Verbas%20Rescis%C3%B3rias/CONVERSOR%20DE%20VERBAS%20RESCISORIAS.html)** (v1 · 25/09/2026)  
  Sobe o PDF "Simulação de rescisão de contrato de trabalho" do UAU! e exporta um Excel tabulado, uma linha por funcionário, com conferência automática contra os totais do PDF e abas por empresa ou por obra, se preferir.
- **[Relatório Diário de Obra](https://mateusm23.github.io/FERRAMENTAS/Outros/Relat%C3%B3rio%20Di%C3%A1rio%20de%20Obra/RELATORIO%20DIARIO%20DE%20OBRA.html)** (v1 · 10/10/2026)  
  Marca num predinho, frente por frente e pavimento por pavimento, o que roda no período e o que está previsto para o próximo, com empreiteiro e efetivo, e gera o PDF para o gestor com quadro de efetivo e fotos. Situações, empreiteiros, frentes e pavimentos são configuráveis.
- **[Planejamento Trimestral](https://mateusm23.github.io/FERRAMENTAS/Outros/Planejamento%20Trimestral/PLANEJAMENTO%20TRIMESTRAL.html)** (v1 · 13/10/2026)  
  Método de reunião semanal de 30 minutos para restrições, previsto x realizado, previsibilidade e planos de ação. Reúne o treinamento, a base de dados (exemplo e em branco) e o painel que se monta ao arrastar a base preenchida.
- **[Conciliação UAU x My Saga](https://mateusm23.github.io/FERRAMENTAS/Outros/Concilia%C3%A7%C3%A3o%20UAU%20x%20My%20Saga/CONCILIACAO%20UAU%20X%20MY%20SAGA.html)** (v1 · 29/10/2026)  
  Sobe o Excel de processos de pagamento do UAU e o CSV de notas de despesa do My Saga, cruza por nota fiscal e fornecedor e lista o que não foi encontrado no My Saga e o que existe só nele. Exporta o resultado em Excel e em PDF.
- **[Extrator de Vistorias](https://mateusm23.github.io/Compilador-Vistorias/)** (v2.1 · 21/07/2026) (repositório próprio)  
  Sobe o lote de laudos PDF de vistoria e gera uma planilha Excel com farol de controle e fotos de cada não conformidade, além de um PDF único consolidado com mapa clicável do empreendimento.
- **[Relatório Semanal de Obra (Base Semanal)](https://mateusm23.github.io/FERRAMENTAS/Outros/Relat%C3%B3rio%20Semanal%20de%20Obra/RELATORIO%20SEMANAL%20DE%20OBRA.html)** (v1 · 06/11/2026)  
  Sobe a base semanal preenchida e monta o relatório completo de obra, com checklist, fotos e exportação em Excel e PDF prontos para envio.
- **[Análise de Emissão de NF](https://mateusm23.github.io/FERRAMENTAS/Outros/An%C3%A1lise%20de%20Emiss%C3%A3o%20de%20NF/ANALISE%20DE%20EMISSAO%20DE%20NF.html)** (v1 · 22/10/2026)  
  Sobe o relatório com as bases 1067 (CAPs) e 260 (pago e a pagar), escolhe os CAPs elegíveis e o período de emissão, e gera a tabela do memorando com PAGO, A PAGAR ou EMISSÃO, soma por NF, pronta para copiar e colar.

## Rodar na sua máquina

Baixe o repositório e suba um servidor local na raiz. Abrir o `index.html` direto do disco mostra o portal, mas as ferramentas que leem planilha falham, porque o navegador bloqueia leitura de arquivo em `file://`:

```bash
npx serve .
```

Depois abra o endereço que o comando mostrar.

## Dados

Este repositório é público. Nada de dado real de obra, de cliente ou de funcionário entra aqui, só código e exemplos fictícios. As bases, os relatórios e os PDFs reais usados em teste ficam fora do versionamento, pelas regras do `.gitignore`.

## Histórico de decisões

O `CONTEXTO.md` registra o estado de cada ferramenta, as regras de leitura de cada base e o motivo das decisões tomadas, para retomar o trabalho sem reconstruir o raciocínio.

## Autor

Mateus Monteiro
