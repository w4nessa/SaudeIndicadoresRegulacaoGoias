# Módulo 0: Setup do projeto

[← Índice](../README.md)

## Checklist

- [x] Escolher o gerenciador de ambiente → **uv** ([decisão](../decisoes.md))
- [x] Repositório git vinculado ao GitHub
- [x] Projeto `uv` (Python 3.13)
- [x] Estrutura de pastas: criada conforme a necessidade de cada módulo, seguindo a [convenção de idioma](../decisoes.md)

## Perguntas para investigar

- [x] O Streamlit Community Cloud lê o `uv.lock` direto, ou precisa de um `requirements.txt`?
  - **Lê, e com a maior prioridade.** Ordem: `uv.lock` → `Pipfile` → `environment.yml` → `requirements.txt` → `pyproject.toml` (tratado como Poetry). A busca olha primeiro a pasta do entrypoint e depois a raiz ([documentação](https://docs.streamlit.io/deploy/streamlit-community-cloud/deploy-your-app/app-dependencies)).
  - Consequências: o `uv.lock` precisa estar no git; um arquivo de dependências na pasta do app ganharia do lock da raiz.
  - Em aberto: de onde o deploy tira a versão do Python (ver no [Módulo 5](05-portfolio.md)).
- [x] `uv init`, `uv add`, `uv sync`, `uv run`: o que cada um faz, e o que aparece na pasta depois de cada comando?
  - `uv init`: cria os arquivos do projeto (`pyproject.toml`, `.python-version`…), sem o `.venv`
  - `uv add`: acrescenta a dependência ao `pyproject.toml`, atualiza o `uv.lock` e instala no `.venv` (criando o `.venv`, se ainda não existir)
  - `uv sync`: deixa o `.venv` exatamente igual ao `uv.lock`, instalando o que falta e removendo o que sobra
  - `uv run`: roda um comando ou script dentro do ambiente, sincronizando antes se precisar
- [x] O que vai para o git: o `.venv`? O `uv.lock`? O `.python-version`?
  - `uv.lock` e `.python-version`: **sim**. Garantem que qualquer máquina (inclusive o deploy) tenha o mesmo Python e as mesmas versões de pacotes.
  - `.venv`: **não**. Ele é recriado a partir desses arquivos com `uv sync`.
- [x] Comparar um `pip freeze` com o `uv.lock`: tamanho e o que cada um registra
  - `pip freeze`: lista plana `nome==versão` do que está instalado **nesta máquina**. Sem hashes, sem origem, sem distinguir o que foi pedido do que veio de carona.
  - `uv.lock`: grafo completo de dependências (quem depende de quem), com origem e hashes de cada arquivo, resolvido **para todos os sistemas**.
  - Neste projeto (Windows): freeze com 62 pacotes (~1 KB) vs lock com 68 pacotes (~258 KB). A diferença inclui pacotes que só existem em outros sistemas, como `appnope` (macOS) e `pexpect`/`ptyprocess` (Linux/macOS). Um `requirements.txt` gerado aqui não teria esses pacotes, e o deploy roda em Linux.
