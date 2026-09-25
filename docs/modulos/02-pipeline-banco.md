# Módulo 2: Pipeline e banco de dados

[← Índice](../README.md)

## Checklist

- [ ] Carregar em **DuckDB** ([ver decisão](../decisoes.md))
- [ ] Pensar em camadas: *raw → limpo → agregado* (pesquisar: arquitetura medalhão, bronze/silver/gold)
- [ ] Padronizar nomes de colunas (`snake_case`, sem acento?)
- [ ] Validação de dados: pesquisar **pandera** ou **Great Expectations**. Que regras esses dados deveriam obedecer?
- [ ] Pipeline como **comando único, que roda do zero sem intervenção manual** (pré-requisito da automação no [Módulo 5](05-portfolio.md))

## Perguntas para investigar

- [ ] Um `.duckdb` gerado numa versão do DuckDB abre em outra? O que isso implica no arquivo de dependências?
- [ ] Como o DuckDB lida com várias sessões simultâneas? (`read_only`, `st.cache_resource` vs `st.cache_data`)
- [ ] `.duckdb` vs Parquet no git: qual versiona melhor? Qual é mais portável?
- [ ] OLTP vs OLAP, armazenamento por linha vs por coluna: por que DuckDB e não SQLite? (ótimo para o README)
- [ ] De onde vêm os CSVs? Existe URL fixa que permita download automático?
  - 💡 *Pista guardada:* o dadosabertos.go.gov.br roda **CKAN**, que tem API própria. Pesquisar "CKAN API package_show". É a base da automação do Módulo 5.

## Entregável

`python -m src.pipeline` (ou similar), que vai do CSV bruto ao banco pronto.
