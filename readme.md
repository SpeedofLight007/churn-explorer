# 🚀 MVP Análise de Dados e Boas Práticas // Churn Explorer

**Desenvolvido por:** Marcelo Santos Araujo

**Matrícula:** 4052024002227

---

## 💔 O Churn: A Dor da Despedida e o Cuidado com o Cliente

Imagine que, para uma empresa de telecomunicações, cada cliente é mais do que um número; é um **relacionamento** construído com tempo e confiança. Quando um cliente decide cancelar seu serviço – o que chamamos de **"churn"** – não é apenas uma assinatura a menos; é como uma **"despedida" dolorosa** para a empresa.

Para essas organizações, o *churn* é um dos indicadores mais importantes e temidos, tocando diretamente o coração do negócio por várias razões:

* **Impacto Financeiro Direto:** Cada cliente que se vai leva consigo a receita mensal, e a reconquista ou atração de um novo cliente custa muito mais do que manter um cliente feliz. É como se fosse mais caro fazer novos amigos do que cuidar dos que já se tem.
* **Erosão da Base:** Se a "porta de saída" fica muito aberta, a base de clientes encolhe, perdendo a força e a capacidade de crescimento da empresa no mercado.
* **Sinal de Alerta:** Um *churn* elevado é um grito silencioso de que algo não vai bem. Pode ser a qualidade do serviço, o preço, o atendimento, ou até mesmo cobranças inesperadas que geram insatisfação. A empresa vê isso como um termômetro de sua própria saúde.

É por isso que este projeto aborda um **problema de Classificação Supervisionada** de extrema importância: nosso objetivo é **identificar proativamente quais clientes estão mais propensos a "se despedir"** antes que eles de fato o façam. Ao prever quem está em risco, a empresa pode estender a mão a tempo, oferecer soluções personalizadas e, acima de tudo, **mostrar que se importa em manter aquele relacionamento**, transformando uma possível "despedida" em uma história de sucesso e fidelidade.

## 🎯 As Perguntas que Guiam Nossa Análise

As hipóteses que traçamos para desvendar o churn são:

* A probabilidade de *churn* de um cliente pode ser prevista com base em seu histórico de interação, consumo, dados demográficos, e padrões de cobrança e satisfação?
* Existe uma correlação entre o tempo de relacionamento do cliente (`Tenure in Months`) e a probabilidade de *churn*?
* Clientes com baixo engajamento, histórico de problemas ou que recebem cobranças extras sem necessidade tendem a ter maior probabilidade de *churn*?
* Como o **Nível de Satisfação do Cliente (`Satisfaction Score`)** e as **discrepâncias de cobrança** influenciam diretamente a decisão de cancelar o serviço?

## 📊 A Base de Dados: Nossos Clientes "Digitais"

O coração da nossa análise é o dataset **Telco Customer Churn**, um conjunto de dados anonimizados de clientes de serviços de telecomunicações. Ele foi obtido diretamente da **Kaggle**, uma das maiores plataformas de dados do mundo. Escolhemos esta base por sua relevância e riqueza de atributos para o contexto de *churn*.

* **Dataset Original:** Encontrado em Kaggle, via `https://www.kaggle.com/datasets/alfathterry/telco-customer-churn-11-1-3`
* **Link da Base de Dados:** `https://raw.githubusercontent.com/SpeedofLight007/churn-explorer/main/telco_dataset.csv`
* **Tamanho da Base:** Contém **7.043 amostras** (registros de clientes), com **1.869 casos de churn** e **5.174 casos de não churn**.

### **Principais Atributos do Dataset (e o que eles nos contam):**

O dataset possui um total de 50 colunas, e as mais relevantes para nossa análise incluem:

* `Customer ID`: O RG do nosso cliente no sistema.
* `Gender` / `Age` / `Under 30` / `Senior Citizen` / `Married` / `Dependents` / `Number of Dependents`: Quem é o cliente (gênero, idade, se é idoso, casado, se tem dependentes e quantos).
* `Country` / `State` / `City` / `Zip Code` / `Latitude` / `Longitude` / `Population`: Informações geográficas e demográficas da localização do cliente.
* `Quarter` / `Referred a Friend` / `Number of Referrals`: Dados sobre o trimestre de aquisição e referências.
* `Tenure in Months`: Há quantos meses o cliente está conosco – um indicador chave de lealdade.
* `Offer`: Tipo de oferta que o cliente possui.
* `Phone Service` / `Multiple Lines` / `Internet Service` / `Internet Type` / `Avg Monthly GB Download` / `Online Security` / `Online Backup` / `Device Protection Plan` / `Premium Tech Support` / `Streaming TV` / `Streaming Movies` / `Streaming Music` / `Unlimited Data`: Quais serviços o cliente tem, tipo de internet, uso de dados e serviços adicionais.
* `Contract`: O tipo de contrato do cliente (mês-a-mês, anual, bienal).
* `Paperless Billing` / `Payment Method`: Preferências de faturamento e método de pagamento.
* `Monthly Charge` / `Total Charges` / `Total Refunds` / `Total Extra Data Charges` / `Total Long Distance Charges` / `Total Revenue`: Detalhes financeiros, incluindo cobranças e receita total.
* **`Satisfaction Score`**: Um **dado crucial** que indica a percepção de satisfação do cliente (escala de 1 a 5).
* `Customer Status`: Status atual do cliente.
* `Churn Label`: A nossa **Variável Alvo** (Sim/Não) – o que queremos prever: se o cliente cancelou ou não.
* `Churn Score` / `CLTV`: Pontuação de churn e valor de vida útil do cliente.
* `Churn Category` / `Churn Reason`: (Disponíveis para clientes que cancelaram) O motivo categorizado e a razão textual do cancelamento.

### **Nossas Features Criadas (Engenharia de Atributos):**

Para ir além, criamos novas características a partir dos dados existentes:

* **`EffectiveMonthlyCharge`:** O custo mensal médio real do cliente, considerando o total que ele pagou ao longo do tempo.
* **`BillingDiscrepancy`:** A diferença entre a `Monthly Charge` atual e a `EffectiveMonthlyCharge`. Se for positiva, pode indicar uma **cobrança extra inesperada** (ou um novo serviço adicionado).
* **`HasUnexpectedExtraCharge`:** Um indicador binário (0 ou 1) que sinaliza se houve essa `BillingDiscrepancy` positiva (com um limiar de 0.1).
* **`DiscrepancySeverity`:** A "gravidade" da `BillingDiscrepancy` ajustada pelo tempo de contrato (`Tenure in Months`). Uma mesma discrepância é mais "dolorosa" para clientes novos do que para clientes fiéis, pois o impacto é ponderado pelo tempo de relacionamento.

## 🧠 Como Nosso "Detetive de Churn" Funciona (Metodologia)

Nosso projeto utiliza um modelo de **Classificação Supervisionada** para prever o churn. Ele funciona como um "detetive" que aprende com o passado para prever o futuro:

1.  **Aprende com o Histórico:** O modelo analisa milhares de clientes que já passaram pela empresa, observando todas as suas características e se eles cancelaram ou não.
2.  **Entende as Relações:** Ele identifica padrões complexos – quais combinações de serviços, tempo de contrato, valores de fatura e, crucialmente, o `Satisfaction Score` e as discrepâncias de cobrança, levam mais ao churn.
3.  **Prevê a Probabilidade (Nossa Escolha):** Para um cliente atual, o modelo não diz apenas "sim" ou "não" para o churn. Ele entrega uma **probabilidade** (por exemplo, "75% de chance de churn"). Essa probabilidade é muito mais útil para a empresa, pois permite ações graduadas: clientes com 80% de chance de sair recebem uma atenção diferente de clientes com 51% de chance. Essa flexibilidade é vital para as estratégias de retenção.
4.  **Modelo Escolhido:** Utilizamos o **Random Forest Classifier**, um algoritmo robusto e eficaz para problemas de classificação.

## 🛠️ Tecnologias Utilizadas

* **Python:** A linguagem central do projeto.
* **Pandas:** Para organizar e manipular nossos dados.
* **NumPy:** Para cálculos numéricos complexos.
* **Matplotlib e Seaborn:** Para criar visualizações claras e impactantes.
* **Scikit-learn:** A biblioteca que nos permite construir e treinar o modelo de Machine Learning.

## 🚀 Como Explorar o Projeto (Execução)

Para você rodar e ver o "Detetive de Churn" em ação:

1.  **Pré-requisitos:**
    * Certifique-se de ter o Python (versão 3.x) instalado.
    * Instale as bibliotecas necessárias via `pip`:
        ```bash
        pip install pandas numpy matplotlib seaborn scikit-learn
        ```
2.  **Obter o Código:**
    * O código Python completo (`mvp-analise-de-dados-e-boas-praticas-churn-explorer-msa.ipynb`) está disponível no Google Colab. Você pode abri-lo diretamente clicando no botão "Open In Colab" no topo do seu notebook ou acessando o link:
        `https://colab.research.google.com/gist/SpeedofLight007/d4c363cf955b6aaa87dfa2d2b6b250aa/mvp-analise-de-dados-e-boas-praticas-churn-explorer-msa.ipynb`
3.  **Dados:**
    * O dataset (`telco_dataset.csv`) será carregado automaticamente do GitHub a partir do script.
4.  **Executar:**
    * No ambiente do Google Colab, execute as células sequencialmente. O script irá carregar os dados, processá-los, treinar o modelo, gerar todos os gráficos e imprimir os insights diretamente no console/saída das células.

## 📈 Nossos Insights e Gráficos Principais

O projeto gera uma série de visualizações para desvendar os mistérios do *churn*, com uma paleta de cores em tons de roxo e amarelo para uma experiência visual agradável:

* **Distribuição da Variável Alvo (Churn):** O ponto de partida para entender o equilíbrio entre clientes que ficam e os que se vão.
* **Tempo de Contrato (Tenure) por Status de Churn:** Um boxplot que compara o tempo de permanência de clientes que deram churn vs. os que ficaram, revelando a importância da fidelização inicial.
* **Churn por Tipo de Serviço de Internet (Contagem e Porcentagem):** Mostra como diferentes tipos de internet (DSL, Fibra Óptica, Sem Serviço) influenciam o número e a taxa de churn, destacando cenários onde o tipo de serviço pode ser um ofensor.
* **Impacto de Cobranças Extras Inesperadas no Churn:** Analisa se o fato de ter uma cobrança "extra" (`HasUnexpectedExtraCharge`) se relaciona com o churn. Curiosamente, nossa análise mostrou que, para este dataset, clientes com `BillingDiscrepancy` positiva podem não estar cancelando devido a isso, sugerindo que essas "cobranças extras" podem ser por serviços adicionais que o cliente desejava e está satisfeito.
* **Churn por Tipo de Contrato:** Ilustra a relação entre o tipo de contrato (mês a mês, anual, bienal) e o churn, revelando a alta vulnerabilidade dos contratos de curto prazo.
* **Churn por Método de Pagamento:** Avalia se o método de pagamento preferido do cliente tem impacto no churn.
* **Churn por Idade (Senioridade) e por Dependentes:** Cenários que exploram se o perfil demográfico (idoso, com dependentes) afeta o churn.
* **Churn por Status de Casamento:** Investiga a relação entre o estado civil e o churn.
* **Taxa de Churn por Tempo de Contrato (Tenure):** Um gráfico de **tendência** crucial, mostrando como a taxa de churn evolui ao longo dos meses de contrato do cliente, com **destaques para o pico de churn nos primeiros meses e a maior queda**, indicando os períodos mais críticos para a retenção.
* **Taxa de Churn por Categoria de Discrepância de Cobrança:** Detalha a taxa de churn em porcentagem para clientes que pagam menos, o esperado ou mais (com cobrança extra), revelando cenários reais de impacto da cobrança.
* **Matriz de Confusão e Curva ROC:** Gráficos que avaliam a precisão e a capacidade de distinção do nosso modelo, mostrando o quão bem ele acerta as previsões.
* **Top 15 Atributos Mais Importantes na Previsão de Churn (Principais Ofensores):** Um gráfico que revela quais características dos clientes (como `Monthly Charge`, `Tenure in Months`, `Contract_Month-to-month`, e o **`Satisfaction Score`**) mais influenciam a decisão de cancelar o serviço.

## 🎯 Nosso Indicador Final de Churn e Seus Principais "Ofensores"

Ao final da execução, o script apresenta um resumo impactante:

* A **Taxa de Churn Geral** da sua base de clientes.
* Uma estimativa clara: aproximadamente **[X] a cada 100 clientes** da sua base podem cancelar.
* E o mais importante: os **[3 principais] fatores** que mais contribuem para esses cancelamentos, baseados na inteligência do nosso modelo, que são os verdadeiros "ofensores" do churn.

## 🚀 Desenvolvimentos Futuros e o Poder dos Dados

Este MVP é apenas o começo de uma jornada fascinante! Aliado a métricas como o **NPS (Net Promoter Score)**, a análise e o script apresentados podem ser expandidos para:

* **Popular datasets:** Integrar mais dados sobre o cliente para ter uma visão 360º e entender cada vez mais suas necessidades e dores.
* **Habilitar tomada de decisão baseada em dados:** Medir a satisfação do cliente com os serviços prestados de forma contínua e proativa.
* **Identificar Consumidores Fiéis:** Usar a probabilidade de churn e o `Tenure in Months` (tempo de relacionamento) para identificar clientes super fiéis. Podemos aplicar análises de RFM (Recência, Frequência, Valor Monetário) para oferecer-lhes novos produtos e serviços, deixando-os ainda mais felizes e "amarrando-os" à empresa, o que indiretamente reduzirá o churn devido à alta concentração de serviços e à satisfação crescente.

Visto que o sentimento humano é uma variável abstrata, traduzi-la em números é um desafio fascinante. Por isso, a maior quantidade de dados tratados, cruzados e visualizados com clareza não apenas norteia, mas **convida os stakeholders a tomarem decisões mais assertivas e conectadas com os valores e a estratégia do negócio.**

---

## 👨‍💻 Autor

* Marcelo Santos Araujo - [Linkedin: (https://www.linkedin.com/in/s-a-marcelo/) GitHub: (https://github.com/SpeedofLight007)]

---
