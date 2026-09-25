# Módulo 1: Conhecer os dados (EDA)

[← Índice](../README.md) · [Referência dos dados](../referencias/dados.md)

É o módulo mais importante: erros aqui contaminam tudo o que vem depois.

## Perguntas para investigar

- [x] Qual é o separador e qual é o encoding? Os acentos aparecem certos?
  - Separador `;`. Encoding **UTF-8 sem BOM** nos três arquivos (todos decodificam sem erro, com milhares de caracteres acentuados). Quebra de linha CRLF.
- [x] A coluna `Mês` vem como `2025/12`. Que tipo o pandas atribui a ela, e que tipo eu *quero* que ela tenha?
  - O pandas infere `str`. Na EDA, fica como **Period mensal** (`pd.to_datetime(..., format='%Y/%m').dt.to_period('M')`). O DuckDB não tem tipo Period, então o tipo no banco (provavelmente `DATE` com dia 1) fica para o [Módulo 2](02-pipeline-banco.md). O formato mês/ano é só de exibição.
- [ ] Em cirurgias existem `GINECOLOGIA`, `GINECOLOGIA/GERAL`, `GINECOLOGIA/2`… **Essas linhas são independentes ou algumas são totais de outras?** Risco de dupla contagem!
  - *Em andamento:* ver [descobertas de cirurgia](#cirurgia)
- [ ] Existe relação matemática entre `Dias em fila`, `Em fila` e `Tempo médio de espera`?
- [ ] `Perda primária` e `Perda secundária` são contagens ou percentuais? Como descobrir só olhando os dados?
- [ ] Qual período os dados cobrem? Algum mês está faltando? Algum parece incompleto?

- [ ] O [dicionário oficial](../referencias/dicionario-oficial.md) diz que tudo é `text` e não descreve nenhuma coluna. Qual é o tipo real e o significado de cada uma? Onde a SES-GO define termos como "perda primária"?

## Descobertas por arquivo

### Cirurgia

*Notebook: [01-eda-cirurgia.ipynb](../../notebooks/01-eda-cirurgia.ipynb). Em andamento.*

- **Tipos reais:** só `Mês` e `Especialidade` são texto. `Dias em fila`, `Em fila` e `Procedimentos realizados` são inteiros; `Tempo médio de espera (Dias)` é decimal. Isso contradiz o dicionário oficial (tudo `text`).
- **Período:** começa em 2023-01.
- **Procedimentos realizados:** só 877 de 6.603 linhas (~13%) têm valor maior que zero. O zero é a regra.
  - Hipótese descartada: presença de `/` no nome define quem tem procedimento.
  - Hipótese descartada (parcialmente): a coluna começou a ser preenchida depois. Os procedimentos existem desde o primeiro mês, mas ainda falta verificar se há meses sem nenhum.
- **Especialidades têm formato `MÃE/FILHA`**, por exemplo `GINECOLOGIA/2` ou `ONCOLOGIA/GINECOLOGIA`. A parte filha mistura categorias diferentes:
  - números (`/1` a `/9` em ginecologia): significado ainda desconhecido (lote? região? prioridade?);
  - procedimentos (`/HISTERECTOMIA`);
  - campanhas (`/MUTIRÃO`);
  - `/GERAL`.

## Entregável

Um notebook de EDA **por arquivo** (cirurgia, consulta, exames), com as descobertas **escritas em texto**, não só código.
