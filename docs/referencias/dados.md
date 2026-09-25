# Referência: dados

[← Índice](../README.md)

**Fonte:** [Portal de Dados Abertos de Goiás](https://dadosabertos.go.gov.br/pt_BR/dataset/transparencia-regulacao-indicadores) (SES-GO)

Três CSVs em `data/raw/`, separados por `;`, com indicadores mensais das filas de regulação:

| Arquivo | Linhas (aprox.) | Granularidade |
|---|---|---|
| `indicadores-de-cirurgia.csv` | 6,6 mil | Mês × Especialidade |
| `indicadores-de-consulta.csv` | 17 mil | Mês × Central × Unidade × Especialidade × Subespecialidade |
| `indicadores-de-exames.csv` | 9 mil | Mês × Central × Unidade × Grupo de exames |

> A granularidade acima veio só da leitura dos cabeçalhos. Confirmar no [Módulo 1](../modulos/01-eda.md).

**Dicionário oficial:** [dicionario-oficial.md](dicionario-oficial.md). Lista as colunas de cada conjunto, mas sem tipos reais nem descrições.
