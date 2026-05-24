# Medidas DAX (Português)

Cole cada bloco como medida no modelo (associada à tabela `fVendas` quando aplicável).

-- Receita Líquida
Receita Líquida = SUM(fVendas[valor_liquido_brl])

-- Receita Bruta
Receita Bruta = SUM(fVendas[valor_bruto_brl])

-- Custo Total
Custo Total = SUM(fVendas[custo_total_brl])

-- Lucro Bruto
Lucro Bruto = [Receita Líquida] - [Custo Total]

-- Margem Bruta %
Margem Bruta % = DIVIDE([Lucro Bruto], [Receita Líquida], 0)

-- Quantidade Total
Quantidade Total = SUM(fVendas[quantidade])

-- Preço Médio Unitário
Preço Médio Unitário = AVERAGE(fVendas[preco_unitario_brl])

-- Desconto Médio %
Desconto Médio % = AVERAGE(fVendas[desconto_pct])

-- Valor Médio Desconto
Valor Médio Desconto = AVERAGE(fVendas[valor_desconto_brl])

-- Ticket Médio (AOV)
Ticket Médio = DIVIDE([Receita Líquida], DISTINCTCOUNT(fVendas[id_pedido]), 0)

-- Clientes Ativos
Clientes Ativos = DISTINCTCOUNT(fVendas[id_cliente])

-- Taxa de Repetição
Taxa de Repetição =
VAR Clientes = SUMMARIZE(fVendas, fVendas[id_cliente], "Pedidos", DISTINCTCOUNT(fVendas[id_pedido]))
VAR ClientesRepetidos = COUNTROWS(FILTER(Clientes, [Pedidos] > 1))
RETURN DIVIDE(ClientesRepetidos, COUNTROWS(Clientes), 0)

-- Vendas Ano Anterior
Vendas Ano Anterior = CALCULATE([Receita Líquida], SAMEPERIODLASTYEAR(dCalendario[Data]))

-- Crescimento YoY %
Crescimento YoY % = DIVIDE([Receita Líquida] - [Vendas Ano Anterior], [Vendas Ano Anterior], 0)

-- Vendas Rolling 12M
Vendas 12M = CALCULATE([Receita Líquida], DATESINPERIOD(dCalendario[Data], MAX(dCalendario[Data]), -12, MONTH))

-- Receita por Unidade
Receita por Unidade = DIVIDE([Receita Líquida], [Quantidade Total], 0)

-- % Pedidos com Desconto
% Pedidos com Desconto = DIVIDE(CALCULATE(DISTINCTCOUNT(fVendas[id_pedido]), fVendas[desconto_pct] > 0), DISTINCTCOUNT(fVendas[id_pedido]), 0)

-- Contador de Anomalias
Contador Anomalias = COUNTROWS(fVendas_Anomalias)

-- Correlação Quantidade x Preço (Pearson) — usar com cuidado em grandes volumes
Corr_Qtd_Preco =
VAR T = FILTER(ALL(fVendas), NOT(ISBLANK(fVendas[quantidade])) && NOT(ISBLANK(fVendas[preco_unitario_brl])))
VAR N = COUNTROWS(T)
VAR MeanQ = AVERAGEX(T, fVendas[quantidade])
VAR MeanP = AVERAGEX(T, fVendas[preco_unitario_brl])
VAR Cov = SUMX(T, (fVendas[quantidade] - MeanQ) * (fVendas[preco_unitario_brl] - MeanP)) / N
VAR StdevQ = SQRT(SUMX(T, POWER(fVendas[quantidade] - MeanQ, 2)) / N)
VAR StdevP = SQRT(SUMX(T, POWER(fVendas[preco_unitario_brl] - MeanP, 2)) / N)
RETURN DIVIDE(Cov, StdevQ * StdevP, BLANK())
