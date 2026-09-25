# Referência: dicionário de dados oficial

[← Dados](dados.md)

Transcrito do [Portal de Dados Abertos de Goiás](https://dadosabertos.go.gov.br/pt_BR/dataset/transparencia-regulacao-indicadores).

## O que o portal informa

- **Tipo:** todas as colunas aparecem como `text`
- **Rótulo** e **Descrição:** vazios em todas as colunas
- **Nomes:** aparecem sem acento no portal (`Mes`, `Perda primaria`); nos CSVs, os cabeçalhos têm acento (`Mês`, `Perda primária`)

## Cirurgia

| Coluna | Tipo |
|---|---|
| Mes | text |
| Especialidade | text |
| Dias em fila | text |
| Em fila | text |
| Tempo medio de espera (Dias) | text |
| Procedimentos realizados | text |

## Consulta

| Coluna | Tipo |
|---|---|
| Mes | text |
| Central Regulacao | text |
| Unidade de saude | text |
| Unidade de saude/Regiao | text |
| Especialidade | text |
| Sub-Especialidade | text |
| Complexidade | text |
| Vagas ofertadas | text |
| Vagas nao preenchidas | text |
| Perda primaria | text |
| Agendamentos cancelados pelo solicitante ou rotina automatica | text |
| Perda primaria sem cancelamentos | text |
| Agendamentos | text |
| Compareceu | text |
| Faltantes | text |
| Perda secundaria | text |
| Tempo medio de espera (Dias) | text |

## Exames

| Coluna | Tipo |
|---|---|
| Mes | text |
| Central Regulacao | text |
| Unidade de saude | text |
| Unidade de saude/Regiao | text |
| Grupo de exames | text |
| Vagas ofertadas | text |
| Vagas nao preenchidas | text |
| Perda primaria | text |
| Agendamentos cancelados pelo solicitante ou rotina automatica | text |
| Perda primaria sem cancelamentos | text |
| Agendamentos | text |
| Compareceu | text |
| Faltantes | text |
| Perda secundaria | text |
| Tempo medio de espera (Dias) | text |

## Comparativo entre conjuntos

Útil para identificar colunas compartilhadas (por exemplo, dimensões comuns na modelagem).

| Coluna | Cirurgia | Consulta | Exames |
|---|:-:|:-:|:-:|
| Mes | ✓ | ✓ | ✓ |
| Tempo medio de espera (Dias) | ✓ | ✓ | ✓ |
| Especialidade | ✓ | ✓ | |
| Central Regulacao | | ✓ | ✓ |
| Unidade de saude | | ✓ | ✓ |
| Unidade de saude/Regiao | | ✓ | ✓ |
| Vagas ofertadas | | ✓ | ✓ |
| Vagas nao preenchidas | | ✓ | ✓ |
| Perda primaria | | ✓ | ✓ |
| Agendamentos cancelados pelo solicitante ou rotina automatica | | ✓ | ✓ |
| Perda primaria sem cancelamentos | | ✓ | ✓ |
| Agendamentos | | ✓ | ✓ |
| Compareceu | | ✓ | ✓ |
| Faltantes | | ✓ | ✓ |
| Perda secundaria | | ✓ | ✓ |
| Sub-Especialidade | | ✓ | |
| Complexidade | | ✓ | |
| Grupo de exames | | | ✓ |
| Dias em fila | ✓ | | |
| Em fila | ✓ | | |
| Procedimentos realizados | ✓ | | |
