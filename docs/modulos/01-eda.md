# Módulo 1: Conhecer os dados (EDA)

[← Índice](../README.md) · [Referência dos dados](../referencias/dados.md)

É o módulo mais importante: erros aqui contaminam tudo o que vem depois.

## Perguntas para investigar

- [x] Qual é o separador e qual é o encoding? Os acentos aparecem certos?
  - Separador `;`. Encoding **UTF-8 sem BOM** nos três arquivos (todos decodificam sem erro, com milhares de caracteres acentuados). Quebra de linha CRLF.
- [ ] A coluna `Mês` vem como `2025/12`. Que tipo o pandas atribui a ela, e que tipo eu *quero* que ela tenha?
- [ ] Em cirurgias existem `GINECOLOGIA`, `GINECOLOGIA/GERAL`, `GINECOLOGIA/2`… **Essas linhas são independentes ou algumas são totais de outras?** Risco de dupla contagem!
- [ ] Existe relação matemática entre `Dias em fila`, `Em fila` e `Tempo médio de espera`?
- [ ] `Perda primária` e `Perda secundária` são contagens ou percentuais? Como descobrir só olhando os dados?
- [ ] Qual período os dados cobrem? Algum mês está faltando? Algum parece incompleto?

- [ ] O [dicionário oficial](../referencias/dicionario-oficial.md) diz que tudo é `text` e não descreve nenhuma coluna. Qual é o tipo real e o significado de cada uma? Onde a SES-GO define termos como "perda primária"?

## Entregável

Um notebook de EDA com as descobertas **escritas em texto**, não só código.
