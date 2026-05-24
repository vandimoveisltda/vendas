# Atualizacao dos dados

O modelo usa o parametro Power Query `CaminhoBaseDados` para localizar os CSVs de entrada.

Antes de atualizar o Power BI Desktop, cada pessoa da equipe deve ajustar esse parametro para a pasta local que contem:

- `dim_cliente_raw.csv`
- `dim_produto_raw.csv`
- `fato_vendas_raw.csv`

Exemplo de valor:

```text
C:\Grupo6-Power-Skills\materiais\raw\
```

Mantenha a barra final no caminho. Nao substitua esse parametro por caminhos pessoais como `C:\Users\<usuario>\...` nos arquivos `.tmdl`, para evitar conflitos de sincronizacao e pull requests quebrados.
