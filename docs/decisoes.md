# Registro de decisões

[← Índice](README.md)

Uma linha por decisão. Se o motivo precisar de mais que duas frases, ele vira um arquivo em `decisoes/` com um link a partir daqui.

| Data | Decisão | Motivo |
|---|---|---|
| 2026-09-25 | Painel em **Streamlit**, publicado no **Community Cloud** | Portfólio acessível por link, sem nada técnico para o visitante |
| 2026-09-25 | **DuckDB versionado no repositório**: o pipeline gera o banco, que é commitado, e o app só lê. Depois, um **GitHub Action** passa a rodar o pipeline e commitar o banco atualizado | Dados pequenos e atualização eventual; DuckDB é embarcado, então o arquivo vai junto com o app |
| 2026-09-25 | Consulta individual via API como aba separada | Diferencial de portfólio; exige cuidado com LGPD |
| 2026-09-25 | Ambiente com **uv** | Lockfile automático e multiplataforma (deploy e Actions rodam Linux); garante a mesma versão do DuckDB que gerou o banco. Contrapontos: ferramenta jovem, mantida por empresa (Astral) |
| 2026-09-25 | **Python 3.13** | Versão já instalada; trocar se algum pacote não suportar |
| 2026-09-25 | **Idioma:** pastas e código em inglês (`data/raw`, `src/`…); termos de domínio em português (`cirurgia`, `fila`…); documentação em português, incluindo os nomes das pastas dentro de `docs/` | Convenção do ecossistema para a estrutura; termos do SUS não ganham clareza traduzidos; o público é brasileiro |
| 2026-09-25 | **Pastas criadas sob demanda**, não antecipadas | Evita pastas vazias e forçar o código num molde que pode não servir |
| 2026-09-25 | Documentação com **progressive disclosure** | Índices curtos que apontam para arquivos menores; evita documentos gigantes que ocupam contexto |
