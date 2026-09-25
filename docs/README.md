# Documentação do projeto

> Índice principal. Ele fica **curto** de propósito: cada item tem um resumo de uma linha e um link para o detalhe.

## Regras do jogo

- **Curso guiado.** O Claude sugere, tira dúvidas e dá pistas. **Não escreve código, notebooks nem análises.**
- O Claude pode editar a documentação (`docs/`, `README.md`). Código e notebooks são feitos por mim.
- Respostas começam pela frase que resolve a dúvida. Lacunas em documentos são avisadas numa lista de pendências.

## Módulos

| # | Módulo | Status | Resumo |
|---|---|---|---|
| 0 | [Setup](modulos/00-setup.md) | 🟡 em andamento | uv + Python 3.13, git, estrutura de pastas |
| 1 | [EDA](modulos/01-eda.md) | ⚪ não iniciado | Entender granularidade, tipos e armadilhas dos dados |
| 2 | [Pipeline e banco](modulos/02-pipeline-banco.md) | ⚪ não iniciado | Do CSV bruto ao DuckDB, com validação |
| 3 | [Painel Streamlit](modulos/03-painel.md) | ⚪ não iniciado | Perguntas de gestão → gráficos e filtros |
| 4 | [Consulta via API](modulos/04-api.md) | ⚪ não iniciado | Posição individual na fila, com cuidado de LGPD |
| 5 | [Portfólio](modulos/05-portfolio.md) | ⚪ não iniciado | Deploy, testes, automação com GitHub Actions |

## Registros

- [Decisões](decisoes.md): o que foi decidido, quando e por quê

## Referências

- [Dados](referencias/dados.md): os três CSVs, a fonte e a granularidade
- [API de Regulação](referencias/api-regulacao.md): endpoints, entrada e pontos de atenção

## Convenções desta pasta

- Índices (este arquivo) têm só resumo + link. O detalhe vai no arquivo filho.
- Se um arquivo crescer demais, vira pasta com o próprio `README.md` de índice.
- Status: ⚪ não iniciado · 🟡 em andamento · 🟢 concluído
