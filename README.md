# Arquitetura_Medalhao_BOI_GORDO

# Pipeline ETL – Medalhão IPCA x Boi Gordo

## 📌 Objetivo
Pipeline de dados em arquitetura medalhão (Bronze, Silver e Gold) que cruza a
variação do IPCA com a variação do preço do boi gordo, gerando insights sobre
como o preço da carne pode impactar a inflação no Brasil.

⚠️ Projeto de estudo/portfólio — parte dos dados/regras é ilustrativa.

## 📚 Origem do projeto
Este projeto teve como base um estudo desenvolvido durante o curso de pós-graduação
em Engenharia de Dados e IA (Anhanguera). A fonte de dados (API do Banco Central e
scraping da CEPEA) e a estrutura de arquitetura medalhão seguem o que foi ensinado
no curso.

As seguintes lógicas foram desenvolvidas por conta própria, além do conteúdo do curso:
- Cálculo de correlação global entre `variacao_ipca` e `variacao_boi` (`corr()`)
- Cálculo de médias globais de IPCA e boi gordo (`AVG()`)
- Classificação de **destaque** e **classe de impacto econômico**, com limiar
  dinâmico calculado pela mediana da distribuição

## 🏗️ Arquitetura

### Bronze
- `bronze_economia.ipca`: dados extraídos via API do Banco Central (série 433 – IPCA)
- `bronze_economia.boi_gordo`: dados extraídos via web scraping do site da CEPEA
- Ambas as tabelas recebem uma coluna `data_coleta` (timestamp da ingestão)

### Silver
- Padronização de tipos (datas convertidas para `yyyy-MM`, valores numéricos para `double`)
- Join entre as séries de IPCA e boi gordo por data
- Tabela final: `silver_economia.economia`

### Gold
- Cálculo de variação percentual mês a mês (`variacao_ipca`, `variacao_boi`) usando
  `Window` + `F.lag()` no PySpark
- Tabela final: `gold_economia.insights`, exposta pela view `vw_gold_dashboard`
- Classificação de **destaque** (qual variável cresceu mais) e **classe de impacto**
  (alta/baixa divergência), calculada via SQL com limiar dinâmico baseado na mediana
  da distribuição (sem valor fixo arbitrário)

## 📊 Análises geradas
- Correlação global entre `variacao_ipca` e `variacao_boi`
- Médias globais de IPCA e boi gordo
- Classificação de destaque/divergência por período
- Dashboard interativo criado no Databricks (AI/BI Dashboards), com gráfico de linha
  das variações, card de correlação e tabela de classificação de impacto

## 🛠️ Tecnologias
Databricks, PySpark, Delta Lake, SQL, API do Banco Central, Web Scraping (CEPEA)

## 📁 Estrutura do repositório
```
0.Config/     → configuração de catálogo e schemas
1.Bronze/     → ingestão do IPCA e do boi gordo
2.Silver/     → tratamento e join das fontes
3.Gold/       → variações, correlação e classificação de impacto
```


## 📸 Dashboard

![Dashboard IPCA x Boi Gordo](dashboard.png)

Dashboard interativo criado no Databricks, exibindo a evolução das variações de
IPCA e boi gordo, a correlação entre as duas variáveis e a classificação de
destaque/impacto econômico por período.
