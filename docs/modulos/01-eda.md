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
- [x] `Perda primária` e `Perda secundária` são contagens ou percentuais? Como descobrir só olhando os dados?
  - Só existem em consultas e exames, não em cirurgia.
  - **Consultas:** percentuais. A primária é a parcela das vagas ofertadas que não foi preenchida; a secundária é a parcela das vagas agendadas que não foi usada (ver [descobertas de consulta](#consulta)).
  - **Exames:** percentuais, com as fórmulas conferidas em 100% das linhas (ver [descobertas de exames](#exames)).
- [x] Qual período os dados cobrem? Algum mês está faltando? Algum parece incompleto?
  - O período não precisa ser levantado na EDA: o mês inicial e o final são dinâmicos e nada fica fixo no código. Ele aparece naturalmente no painel.
  - Não dá para afirmar que algum mês está completo. Os dados são mensais (mês/ano) e uma especialidade ou unidade pode simplesmente não ter registro em algum mês. Ausência de linha não é zero e não deve ser preenchida: dado que não existe não se inventa.
  - Os casos conhecidos de mês parcial são o mês corrente ([regra em decisões](../decisoes.md)) e os meses futuros de exames. Ver [descobertas de cirurgia](#cirurgia) e [de exames](#exames).

- [x] O [dicionário oficial](../referencias/dicionario-oficial.md) diz que tudo é `text` e não descreve nenhuma coluna. Qual é o tipo real e o significado de cada uma? Onde a SES-GO define termos como "perda primária"?
  - Os tipos reais estão nas descobertas de cada arquivo. A SES-GO não define os termos em lugar nenhum; os nomes das colunas são autoexplicativos. As perdas, que eram o ponto menos óbvio, estão definidas nas descobertas de [consulta](#consulta) e [exames](#exames).

## Descobertas por arquivo

### Cirurgia

*Notebook: [01-eda-cirurgia.ipynb](../../notebooks/01-eda-cirurgia.ipynb). Concluído.*

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
  - números (`/1` a `/9` em ginecologia): provavelmente lotes liberados aos poucos. É uma hipótese, sem confirmação nem documentação oficial;
  - procedimentos (`/HISTERECTOMIA`);
  - campanhas (`/MUTIRÃO`);
  - `/GERAL`.
- **Para a limpeza (Módulo 2):**
  - a convenção de nome não é consistente: `ORTOPEDIA MUTIRÃO` usa espaço, e `GINECOLOGIA/MUTIRÃO` usa `/`. Separar família pela barra transforma a primeira numa família à parte;
  - `Tempo médio de espera` pode ser recalculado em vez de armazenado.
- **Produção de oftalmologia só existe em 6 meses.** As filhas de oftalmologia têm fila em todos os meses, mas, como em toda família, nunca têm procedimentos. A linha mãe `OFTALMOLOGIA`, a única que poderia ter, só aparece em 6 meses entre 2024/02 e 2024/10 (~1.500 procedimentos por mês, fila de 1 a 15). Nos outros meses não há registro de procedimentos de oftalmologia: é ausência de dado, não zero.

### Consulta

*Notebook: [02-eda-consultas.ipynb](../../notebooks/02-eda-consultas.ipynb). Concluído.*

- **Tipos reais:** os inferidos pelo pandas estão corretos. As 7 colunas de dimensão são texto (`Mês`, `Central Regulação`, `Unidade de saúde`, `Unidade de saúde/Região`, `Especialidade`, `Sub-Especialidade`, `Complexidade`). Entre as métricas:
  - inteiros: `Agendamentos cancelados pelo solicitante ou rotina automática` e `Compareceu`;
  - decimais: todas as outras, inclusive contagens como `Vagas ofertadas`, `Agendamentos` e `Faltantes`, que vêm com `.0` (ex.: `8.0`).
  - Isso contradiz o dicionário oficial (tudo `text`).
- **`Perda primária` e `Perda secundária` são percentuais.**
  - Perda primária: vagas que não chegaram a ser preenchidas, em relação às vagas ofertadas. Exemplo da primeira linha (oncologia clínica, 2023/10): 8 vagas ofertadas e 8 não preenchidas dão 100%.
  - Perda secundária: vagas que foram agendadas, mas não foram usadas. Por exemplo, a pessoa confirmou a consulta e não compareceu.
- **Granularidade confirmada:** Mês × Central × Unidade × Especialidade × Subespecialidade identifica cada linha.

### Exames

*Notebook: [03-eda-exames.ipynb](../../notebooks/03-eda-exames.ipynb). Concluído. Números do download de 2026-10-06.*

- **Tipos reais:** os inferidos pelo pandas estão corretos. As dimensões são texto; `Agendamentos cancelados pelo solicitante ou rotina automática` e `Compareceu` são inteiros; as demais métricas são decimais. Isso contradiz o dicionário oficial (tudo `text`).
- **Granularidade confirmada:** Mês × Central × Unidade × Grupo de exames identifica cada linha.
- **Não há dado de fila.** Ao contrário de cirurgia, exames não tem `Em fila` nem `Dias em fila`. O `Tempo médio de espera` equivale ao de cirurgia (a espera média, em dias) e pode ser usado como indicador, mas o tamanho da fila não pode ser obtido nem estimado a partir deste arquivo.
- **As perdas são percentuais derivados**, arredondados em 2 casas, e as fórmulas batem em 100% das linhas (quando o denominador não é zero):
  - `Perda primária = Vagas não preenchidas / Vagas ofertadas`;
  - `Perda primária sem cancelamentos = (Vagas não preenchidas − Agendamentos cancelados) / Vagas ofertadas`;
  - `Perda secundária = Faltantes / Agendamentos`.
- **Os cancelados fazem parte das vagas não preenchidas.** `Vagas não preenchidas` nunca é menor que `Agendamentos cancelados…`. Em cerca de metade das linhas com vaga não preenchida, todas são cancelamentos (2026/08: 123 de 245; 2026/09: 150 de 279), e nesses casos a perda primária sem cancelamentos é 0%. Exemplo: polissonografia em Rio Verde, 2026/09, com 4 não preenchidas e 4 canceladas.
- **`Agendamentos cancelados…` é sempre zero antes de 2025/01.** Em 2024/11 e 2024/12, nenhuma linha tem cancelamento. Provavelmente a coluna ainda não era preenchida. Até 2024/12, a perda primária sem cancelamentos é igual à perda primária e não se compara com os meses seguintes.
- **O arquivo tem meses futuros.** No download de 2026-10-06, vai até 2027/08, com poucas linhas a partir de 2026/11 (dezenas em novembro e dezembro, de 1 a 5 por mês em 2027), provavelmente vagas já abertas na agenda. Por isso, em exames, o último mês do arquivo não é o mês corrente. A [regra do mês corrente](../decisoes.md) precisa ser aplicada pela data do download, e os meses futuros também ficam fora dos cálculos.

## Entregável

Um notebook de EDA **por arquivo** (cirurgia, consulta, exames), com as descobertas **escritas em texto neste documento**, na seção [Descobertas por arquivo](#descobertas-por-arquivo).
