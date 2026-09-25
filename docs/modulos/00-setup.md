# Módulo 0: Setup do projeto

[← Índice](../README.md)

## Checklist

- [x] Escolher o gerenciador de ambiente → **uv** ([decisão](../decisoes.md))
- [x] Repositório git vinculado ao GitHub
- [ ] Projeto `uv` (Python 3.13)
- [ ] Estrutura de pastas ([convenção de idioma](../decisoes.md))

## Perguntas para investigar

- [ ] O Streamlit Community Cloud lê o `uv.lock` direto, ou precisa de um `requirements.txt`?
- [ ] `uv init`, `uv add`, `uv sync`, `uv run`: o que cada um faz, e o que aparece na pasta depois de cada comando?
- [ ] O que vai para o git: o `.venv`? O `uv.lock`? O `.python-version`?
- [ ] Comparar um `pip freeze` com o `uv.lock`: tamanho e o que cada um registra
