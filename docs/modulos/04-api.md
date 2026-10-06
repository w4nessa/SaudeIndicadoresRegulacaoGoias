# Módulo 4: Consulta individual via API

[← Índice](../README.md) · [Referência da API](../referencias/api-regulacao.md)

## Checklist

- [ ] **LGPD:** o que é registrado em log? O que acontece se você usar `st.cache_data` numa função que recebe CPF: onde o dado fica, e por quanto tempo?
- [ ] Robustez: timeout, API fora do ar, paciente não encontrado, CPF inválido
- [ ] Validar o dígito verificador do CPF antes de chamar a API
- [ ] Aviso ao usuário: o dado vem da SES-GO e o app não armazena nada
- [ ] Nunca colocar dados reais em commits, prints ou testes → pesquisar *mock* de requisições HTTP

## Ideias para retomar

- **Vagas previstas × realizadas em exames.** O CSV de exames traz meses futuros, e os números deles mudam a cada download (2026/10: 64.674 vagas ofertadas em 25/09 e 69.239 em 06/10). Comparar downloads mostra quantas vagas estavam previstas e quantas foram realmente ofertadas. Para isso, cada download precisa ficar guardado com a sua data (ver [descobertas de exames](01-eda.md#exames)).
