# Módulo 1: Conhecer os dados (EDA)

[← Índice](../README.md) · [Referência dos dados](../referencias/dados.md)

É o módulo mais importante: erros aqui contaminam tudo o que vem depois.

## Perguntas para investigar

- [x] Qual é o separador e qual é o encoding? Os acentos aparecem certos?
  - Separador `;`. Encoding **UTF-8 sem BOM** nos três arquivos (todos decodificam sem erro, com milhares de caracteres acentuados). Quebra de linha CRLF.
- [x] A coluna `Mês` vem como `2025/12`. Que tipo o pandas atribui a ela, e que tipo eu *quero* que ela tenha?
  - O pandas infere `str`. Na EDA, fica como **Period mensal** (`pd.to_datetime(..., format='%Y/%m').dt.to_period('M')`). O DuckDB não tem tipo Period, então o tipo no banco (provavelmente `DATE` com dia 1) fica para o [Módulo 2](02-pipeline-banco.md). O formato mês/ano é só de exibição.
- [x] Em cirurgias existem `GINECOLOGIA`, `GINECOLOGIA/GERAL`, `GINECOLOGIA/2`… **Essas linhas são independentes ou algumas são totais de outras?** Risco de dupla contagem!
  - **Independentes.** A linha mãe não é a soma das filhas (ginecologia em 2026/03: filhas somam 72 na fila, a mãe tem 486). Detalhes em [descobertas de cirurgia](#cirurgia).
- [x] Existe relação matemática entre `Dias em fila`, `Em fila` e `Tempo médio de espera`?
  - `Tempo médio de espera = Dias em fila / Em fila`, arredondado em 2 casas, em 100% das linhas de cirurgia (`Em fila` nunca é zero). É coluna derivada, recalculável.
- [ ] `Perda primária` e `Perda secundária` são contagens ou percentuais? Como descobrir só olhando os dados?
  - Só existem em consultas e exames, não em cirurgia. Fica para as EDAs desses arquivos.
- [ ] Qual período os dados cobrem? Algum mês está faltando? Algum parece incompleto?
  - **Cirurgia:** 2023/01 a 2026/09, 45 meses, nenhum faltando. O último mês é sempre o corrente, com dados parciais (ver [descobertas de cirurgia](#cirurgia)). Falta responder para consultas e exames.

- [ ] O [dicionário oficial](../referencias/dicionario-oficial.md) diz que tudo é `text` e não descreve nenhuma coluna. Qual é o tipo real e o significado de cada uma? Onde a SES-GO define termos como "perda primária"?

## Descobertas por arquivo

### Cirurgia

*Notebook: [01-eda-cirurgia.ipynb](../../notebooks/01-eda-cirurgia.ipynb). Em andamento.*

- **Tipos reais:** só `Mês` e `Especialidade` são texto. `Dias em fila`, `Em fila` e `Procedimentos realizados` são inteiros; `Tempo médio de espera (Dias)` é decimal. Isso contradiz o dicionário oficial (tudo `text`).
- **Período:** 2023/01 a 2026/09, 45 meses, sem buracos. Todo mês tem procedimentos.
- **O último mês do arquivo é o mês corrente, com dados parciais.** No download de 2026-09-30, é 2026/09. Ele fica fora dos cálculos enquanto não fechar; no próximo download, entra normalmente (ver [decisões](../decisoes.md)).
- **Setembro/2026 marca uma quebra de série.** A fila total salta de ~7 mil (2026/08) para ~30,6 mil, em todas as especialidades ao mesmo tempo (mediana de 4,3×), e o tempo médio de espera sobe junto. Causa: mudança na abrangência da regulação estadual, com municípios saindo do SISREG e entrando na fila estadual. A SES-GO não tem documentação oficial sobre essa mudança. Consequências:
  - fila antes e depois da migração cobre populações diferentes e não se compara direto;
  - como a migração é gradual, os meses seguintes podem continuar subindo.
- **Procedimentos realizados só existem nas linhas sem `/` (linha mãe).** Nenhuma das 5.714 linhas com `/` tem procedimento; das 889 sem `/`, 877 têm. Isso vale para todas as famílias.
  - *Revisto em 2026-09-30:* a hipótese "presença de `/` define quem tem procedimento", antes marcada como descartada, se confirmou.
  - As 12 linhas mãe com zero são pontuais (ex.: `CIRURGIA METABÓLICA`, 3 linhas, nunca teve).
- **Linha mãe e filhas são independentes**, mas a relação entre `Em fila` e `Procedimentos realizados` muda de família para família. Nos meses fechados:
  - ginecologia: a fila **da mãe** anda colada nos procedimentos (2026/07: 452 e 452);
  - urologia: a **soma das filhas** anda colada nos procedimentos (2025/07: 507 e 500; a mãe tem 95).
  - Consequência: somar linhas para obter "fila total" de uma família não tem significado garantido.
- **Especialidades têm formato `MÃE/FILHA`**, por exemplo `GINECOLOGIA/2` ou `ONCOLOGIA/GINECOLOGIA`. A parte filha mistura categorias diferentes:
  - números (`/1` a `/9` em ginecologia): significado ainda desconhecido (lote? região? prioridade?);
  - procedimentos (`/HISTERECTOMIA`);
  - campanhas (`/MUTIRÃO`);
  - `/GERAL`.
- **Para a limpeza (Módulo 2):**
  - a convenção de nome não é consistente: `ORTOPEDIA MUTIRÃO` usa espaço, e `GINECOLOGIA/MUTIRÃO` usa `/`. Separar família pela barra transforma a primeira numa família à parte;
  - `Tempo médio de espera` pode ser recalculado em vez de armazenado.
- **Lacunas anotadas, sem investigar:** a linha mãe `OFTALMOLOGIA` só aparece em 6 meses (entre 2024/02 e 2024/10), com ~1.500 procedimentos por mês e fila de 1 a 15. Fora disso, a oftalmologia só tem filhas, sem produção. A série de produção de oftalmologia é incompleta.

## Entregável

Um notebook de EDA **por arquivo** (cirurgia, consulta, exames), com as descobertas **escritas em texto**, não só código.
