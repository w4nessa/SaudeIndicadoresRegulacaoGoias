# Referência: API de Regulação (SES-GO)

[← Índice](../README.md) · [Módulo 4](../modulos/04-api.md)

- **Swagger:** <https://api.regulacao-transparencia.saude.go.gov.br/>
- **Schema OpenAPI:** `/api/schema/`
- **Autenticação:** nenhuma

## Endpoints (todos `POST`)

| Endpoint | Fila |
|---|---|
| `/consultar/cirurgia/` | Cirurgias |
| `/consultar/consulta/` | Consultas |
| `/consultar/exame/` | Exames |
| `/consultar/internacao/` | Internações (**não tem CSV**, só existe via API) |

## Entrada

CNS (Cartão SUS) **ou** CPF, mais a data de nascimento. Aceita JSON ou `multipart/form-data`.

## Saída

A posição individual do paciente na fila. Não serve para indicadores agregados.
