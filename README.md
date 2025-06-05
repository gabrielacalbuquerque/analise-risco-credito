# Ficha Técnica: Projeto de Análise de Dados - Risco de Inadimplência

Este projeto tem como objetivo principal identificar o perfil de clientes com risco de inadimplência, desenvolver uma pontuação de crédito baseada em análise de dados e avaliar o risco relativo. O resultado permite classificar clientes atuais e futuros em diferentes categorias de risco, conforme a probabilidade de inadimplência[cite: 1].

## 🎯 Objetivo do Projeto

- Identificar o perfil de clientes com risco de inadimplência.
- Criar um score de crédito através da análise de dados.
- Avaliar o risco relativo para classificar clientes em diferentes categorias de risco de inadimplência[cite: 1].

## 👥 Equipe

- O trabalho inicial foi realizado individualmente[cite: 2].

## 🛠️ Ferramentas e Tecnologias

As seguintes ferramentas e tecnologias foram utilizadas no desenvolvimento do projeto:

- **Google BigQuery**: Data warehouse para processamento de grandes volumes de dados[cite: 2].
- **Google Colab**: Plataforma para desenvolvimento em Python utilizando Notebooks[cite: 3].
- **Google Looker Studio**: Ferramenta para criação de painéis e relatórios de dados[cite: 5].
- **Apresentações Google**: Utilizada para criação e edição de apresentações[cite: 4].

## 🌐 Linguagens

- **SQL**: Utilizado no Google BigQuery[cite: 6].
- **Python**: Utilizado no Google Colab[cite: 6].

## 📊 Processamento e Análises

### 1. Contexto e Problema de Negócio

O problema de negócio central é prever a probabilidade de inadimplência de clientes de crédito com base em:
- Dados demográficos (idade, gênero, dependentes).
- Dados financeiros (renda, endividamento, histórico de pagamentos).
- Comportamento de crédito (utilização de limite, atrasos)[cite: 6].

### 2. Fontes de Dados

As análises foram realizadas com base nas seguintes tabelas:

| Tabela                       | Descrição                                  |
| :--------------------------- | :----------------------------------------- |
| `tabela_final_normalizada`   | Dados brutos tratados (clientes + empréstimos) [cite: 7] |
| `tabela_unificada_enriquecida` | Dados segmentados e derivados (ex: faixas de renda) [cite: 7] |

### 3. Transformação e Limpeza dos Dados

#### Problemas Resolvidos:
- **Valores Nulos e Inconsistências**:
    - IDs ausentes foram substituídos por códigos negativos (`999` para empréstimos, `998` para usuários)[cite: 8].
    - Tipos de empréstimo foram padronizados (ex: "Pessoal", "Consignado")[cite: 9].

#### Criação de Variáveis Derivadas:
Variáveis como `faixa_etaria`, `renda_per_capita` e `nivel_endividamento` foram criadas. Por exemplo:
- `faixa_etaria`: Segmentação por idade (ex: '18-24', '25-34', '65+')[cite: 11].
- `renda_per_capita`: `salario / (dependentes + 1)`[cite: 11].
- `nivel_endividamento`: Baseado no `indice_divida` (ex: 'Saudável' <=30%, 'Crítico' >50%)[cite: 11].

### 4. Estrutura Final da Tabela

A tabela final inclui as seguintes colunas principais:

| Coluna             | Tipo   | Descrição                                 |
| :----------------- | :----- | :---------------------------------------- |
| `Id_usuario`       | STRING | Identificador único (valores negativos = nulos) [cite: 12] |
| `faixa_etaria`     | STRING | Idade segmentada [cite: 12]               |
| `status_pagamento` | STRING | Adimplente / Inadimplente [cite: 12]      |
| `credito_utilizado`| FLOAT  | Valor do crédito não garantido [cite: 12] |
| `risco_credito`    | STRING | Classificação manual (Baixo, Médio, Alto) [cite: 12] |

### 5. Análise Exploratória Inicial e Decisões Técnicas

- **Distribuição de Idade vs. Inadimplência**: Observou-se uma taxa de inadimplência de 12.5% para a faixa de 18-24 anos e 7.8% para 25-34 anos[cite: 16].
- **Tratamento de Dados**: Valores nulos foram substituídos pela mediana (numéricos) ou "Não informado" (categóricos)[cite: 17].
- **Variáveis Selecionadas para Modelagem**: `faixa_etaria`, `numero_dependentes`, `renda_per_capita`, `indice_divida`, `status_pagamento`, `credito_utilizado`[cite: 18].
- **Cálculo de Correlações**: A análise de correlação entre variáveis numéricas e inadimplência não mostrou relação linear significativa, com coeficientes próximos de zero[cite: 19, 20]. Por isso, foram usadas técnicas como agrupamento por quartis, score composto e risco relativo[cite: 21].

### 6. Análise de Risco Relativo por Quartis

Foi realizada uma análise de risco relativo por quartis de idade, faixa salarial e renda familiar.
- **Risco Relativo de Idade**: O Quartil 1 (grupo exposto) apresentou um risco 2.36 vezes maior de inadimplência em comparação com os outros quartis[cite: 32, 33].
- **Risco Relativo de Salário**: O Quartil 1 (grupo exposto) mostrou um risco 0.18 vezes menor (ou seja, 0.18x menos arriscado) em relação aos outros quartis[cite: 34, 35].

### 7. Aplicação de Segmentação por Score e Validação

Foi desenvolvido um score de risco e uma classificação de risco (`bom pagador` ou `mau pagador`)[cite: 36, 38].

#### Tabela de Score de Risco e Recomendações Estratégicas:

| Score | Perfil de Risco | Classificação | Recomendações Estratégicas                                                                                   |
| :---- | :-------------- | :------------ | :----------------------------------------------------------------------------------------------------------- |
| 3     | Muito baixo     | Bom pagador   | Oferecer crédito com melhores condições, aumentar limite de crédito, propor serviços premium [cite: 41]          |
| 4-5   | Baixo           | Bom pagador   | Manter relacionamento com benefícios, oferecer upgrades condicionados a bom histórico, monitoramento leve [cite: 43] |
| 6-8   | Médio           | Bom pagador   | Acompanhar com mais atenção, solicitar comprovação de renda, evitar concessões automáticas, possível inclusão em programas educativos [cite: 43] |
| 9-10  | Alto            | Mau pagador   | Restringir aumento de crédito, aplicar políticas de garantias, comunicação preventiva e frequente, revisar dívidas [cite: 43] |
| 11-12 | Muito alto      | Mau pagador   | Bloqueio de crédito automático, solicitação de garantias reais, encaminhamento para análise manual, oferecer renegociação [cite: 43] |

#### Matriz de Confusão para Validação do Modelo:

| Real (status_pagamento) | Previsto (classificacao_risco) | Total    | Interpretação          |
| :---------------------- | :----------------------------- | :------- | :--------------------- |
| Adimplente              | bom pagador                    | 24.528   | Verdadeiro Positivo (VP) [cite: 47] |
| Adimplente              | mau pagador                    | 10.425   | X Falso Negativo (FN) [cite: 47]  |
| Inadimplente            | bom pagador                    | 540      | X Falso Positivo (FP) [cite: 47]  |
| Inadimplente            | mau pagador                    | 82       | ✔ Verdadeiro Negativo (VN) [cite: 47] |

#### Métricas de Avaliação (considerando "mau pagador" como classe positiva):

- **Acurácia**: $\approx 69,4\%$[cite: 48]. Parece boa, mas... [cite: 50]
- **Precisão**: $\approx 0,78\%$[cite: 49]. Muito baixa para identificar inadimplentes[cite: 50].
- **Recall / Sensibilidade**: $\approx 13,2\%$[cite: 49]. Muito baixo para identificar inadimplentes[cite: 50].

**Interpretação**: O modelo acerta bem os bons pagadores, mas tem dificuldade em prever inadimplentes. Isso pode ser devido a um desequilíbrio de classes (mais adimplentes que inadimplentes)[cite: 51, 52].

## 📈 Resultados e Conclusões

### Principais Resultados e Fatores de Risco:

1.  **Impacto do Endividamento**: Clientes com índice de dívida superior a 50% (Alto Endividamento) possuem um score de risco elevado (0,9), indicando alto potencial de inadimplência e a necessidade de monitoramento rigoroso[cite: 53, 54].
2.  **Idade e Risco**: A mediana de idade é de 53 anos. As faixas etárias mais jovens (18-24, 25-34, 35-44 e 45-54) apresentam scores de risco mais altos (0,5 ou 0,4), sugerindo maior probabilidade de inadimplência neste segmento[cite: 55, 56].
3.  **Renda (Salário e Familiar) e Risco**:
    -   **Menor Renda Salarial**: Salários de até R\$ 2.000 demonstram o maior score de risco (1,4)[cite: 58].
    -   **Menor Renda Familiar Per Capita**: Categoria "Abaixo de R\$ 1.000 per capita" possui o score de risco mais elevado (1,9), confirmando a alta vulnerabilidade[cite: 59].

### Conclusões:

-   Existe uma clara correlação entre alto endividamento, faixas etárias mais jovens e baixa renda com um maior risco de inadimplência[cite: 60].
-   É fundamental revisar e ajustar as políticas de concessão de crédito considerando esses fatores para mitigar perdas[cite: 61].
-   O monitoramento contínuo e a implementação de programas de educação financeira para grupos de alto risco são essenciais[cite: 62].
-   A qualidade e validação dos dados são cruciais para a precisão das futuras análises[cite: 63].

## 🚧 Limitações e Próximos Passos

### Limitações da Análise:

-   **Validação de Dados**: Necessidade de validação e correção de dados pontuais, como a quantidade de inadimplentes para o quartil de menor salário, que apresentou um risco relativo contraintuitivo[cite: 64]. A acurácia total dos dados é essencial[cite: 65].
-   **Acurácia e Recall do Modelo**: Embora a precisão do modelo para identificar adimplentes seja alta (97,85%), o recall (70,17%) indica que há espaço para aprimorar a identificação de inadimplentes[cite: 66].
-   **Fatores Não Incluídos**: Fatores externos (macroeconomia, mudanças regulatórias) ou outros dados comportamentais do cliente (histórico de crédito externo, frequência de transações) não foram contemplados[cite: 67, 68].

### Próximos Passos:

1.  **Validação e Enriquecimento de Dados**:
    -   Priorizar a revisão e correção das inconsistências de dados, especialmente as que impactam os cálculos de risco[cite: 70].
    -   Explorar a integração de fontes de dados adicionais para enriquecer o perfil do cliente[cite: 71].
2.  **Otimização do Modelo de Risco**:
    -   Melhorar continuamente o algoritmo para aumentar o recall e a capacidade preditiva em relação aos clientes inadimplentes[cite: 72].
    -   Testar diferentes abordagens e variáveis para refinar o score de risco[cite: 73].
3.  **Implementação de Recomendações Estratégicas**:
    -   Colaborar com as áreas responsáveis para ajustar as políticas de concessão de crédito com base nos insights de risco[cite: 74].
    -   Avaliar a viabilidade de programas de educação financeira para clientes de alto risco[cite: 75].
4.  **Monitoramento Contínuo**:
    -   Estabelecer um processo de monitoramento periódico dos indicadores de risco e da performance do portfólio[cite: 76].

## 🔗 Links de Interesse

-   [Planilha com tabelas feitas a partir de algumas consultas do BigQuery](https://docs.google.com/spreadsheets/d/13nZq8zMcP7Q2hWg9JrECD7fRC1C1Fg1x7BuOjGL7xF8/edit?gid=1256417496#gid=1256417496) 
-   [Dashboard](https://lookerstudio.google.com/u/0/reporting/5334aeb8-ba68-494b-b94b-b405d2918681) 
-   [Apresentação da análise dos resultados](https://www.loom.com/share/dc41a33255954a168d3ac20b70584a49) 
