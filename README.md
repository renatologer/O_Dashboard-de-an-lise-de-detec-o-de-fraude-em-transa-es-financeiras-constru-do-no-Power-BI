# Pipeline de dados e modelo

O projeto segue a arquitetura **medalhão** (bronze / silver / gold) no Databricks:

- **Bronze:** dataset original de fraude em cartão de crédito (Kaggle, `mlg-ulb/creditcardfraud`) carregado como tabela Delta
- **Silver:** remoção de duplicatas reais, criação de identificador único por transação
- **Gold:** features numéricas normalizadas (Amount e Time escalados), prontas para o modelo

### Modelo de Machine Learning

- **Algoritmo:** Random Forest Classifier (PySpark MLlib)
- **Features:** variáveis V1–V28 (componentes PCA do dataset original) + Amount e Time escalados
- **Split:** 80% treino / 20% teste
- **Rastreamento:** MLflow, com registro de versões no Unity Catalog
- **Critério de promoção:** o modelo só é promovido para produção se atingir **AUC-PR ≥ 0.80**

### Camadas finais para o dashboard

- `dim_tempo`: dimensão de tempo com período do dia (madrugada, manhã, tarde, noite)
- `fato_transacoes`: fatos de transações com indicador de fraude (otimizada com Z-Order por `is_fraude`)

O Power BI se conecta a essas tabelas finais no Databricks para alimentar os visuais.
