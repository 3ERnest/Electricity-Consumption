# Guia de construção — Relatório Power BI: Consumo de Eletricidade em Tetuão (Marrocos)

Não tenho como abrir o Power BI Desktop diretamente a partir daqui (é uma
aplicação gráfica), por isso preparei tudo o que precisas para montares o
relatório em ~15 minutos, e já fiz toda a análise por ti (ver secção
"Resultados da análise" e o dashboard interativo enviado no chat, que podes
usar já hoje sem esperar pelo Power BI).

## Ficheiros preparados nesta pasta

- `powerconsumption_PowerBI_ready.csv` — dataset original + colunas de apoio
  (Hour, DayName, MonthName, Week, IsWeekend, Season, TotalConsumption).
  Usa este como tabela principal.
- `powerconsumption_PowerBI_long.csv` — mesma informação em formato "long"
  (uma linha por zona/timestamp, coluna `Zone` + `PowerConsumption_kWh`).
  Mais fácil para comparar as 3 zonas no mesmo gráfico com uma legenda.
- `DAX_measures.txt` — medidas prontas a colar.
- `hourly_pattern.csv`, `weekly_pattern.csv`, `monthly_pattern.csv`,
  `correlations.csv`, `season_hour_heat_byzone.csv` — tabelas já agregadas,
  caso prefiras carregá-las diretamente em vez de agregar no Power BI.

## Passo a passo

1. Abre o Power BI Desktop → **Obter Dados > Texto/CSV** → seleciona
   `powerconsumption_PowerBI_long.csv` (recomendado como tabela principal,
   é a que dá mais flexibilidade para comparar zonas).
2. No Power Query, confirma os tipos: `Datetime` = Data/Hora, `Season`,
   `DayName`, `MonthName`, `Zone` = Texto, `IsWeekend` = Verdadeiro/Falso.
3. Cria uma tabela de datas dedicada: **Modelagem > Nova Tabela**:
   ```
   DimDate = CALENDAR(DATE(2017,1,1), DATE(2017,12,30))
   ```
   Marca-a como "Tabela de datas" (Modelagem > Marcar como tabela de datas)
   e relaciona `DimDate[Date]` com `powerconsumption_PowerBI_long[Date]`.
4. Cola as medidas de `DAX_measures.txt` numa tabela nova "_Medidas"
   (Modelagem > Nova Medida, ou cria uma tabela vazia primeiro).
5. Constrói as páginas abaixo.

## Páginas recomendadas

**Página 1 — Visão Geral**
- 3 cartões KPI: Total Consumption, Avg Temperature, Zona com maior consumo.
- Gráfico de área/linha: `TotalConsumption` médio por `Datetime` (série
  temporal completa do ano).
- 3 cartões de percentual: Zone1/2/3 Share %.

**Página 2 — Padrões Sazonais (horário, semanal, anual)**
- Gráfico de linhas: consumo médio por `Hour` (0–23), uma linha por `Zone`
  → mostra o padrão diário (pico ~20h, vale ~06h).
- Gráfico de colunas: consumo médio por `DayName` (ordenado Seg→Dom).
- Gráfico de linhas: consumo médio por `MonthName` (ordenado Jan→Dez) →
  mostra o padrão anual (pico no Verão, jul/ago).
- Matriz/heatmap: `Season` (linhas) x `Hour` (colunas), valores =
  `TotalConsumption` médio — usa o ficheiro `season_hour_heat_byzone.csv`
  ou o visual "Matrix" com formatação condicional.

**Página 3 — Clima vs Consumo**
- Gráfico de dispersão (scatter): `Temperature` (eixo X) vs
  `TotalConsumption` (eixo Y), com `Zone` como legenda — a nuvem de pontos
  mostra a relação positiva.
- Cartões com os coeficientes de correlação (usa `correlations.csv` ou a
  medida `Correlation Temp vs Total`).
- Gráfico de dispersão secundário: `Humidity` vs `TotalConsumption`.
- Slicer de `Season` para filtrar todos os visuais desta página.

**Página 4 — Comparação entre Zonas**
- Gráfico de colunas empilhadas 100%: contribuição de cada zona por mês.
- Gráfico de linhas: as 3 zonas ao longo do tempo (usa a versão long,
  `Zone` como legenda).
- Tabela: consumo médio, mínimo, máximo por zona e por estação.

## Filtros/Slicers globais sugeridos
`Season`, `DayName`, `Zone`, e um slicer de intervalo de datas para
`Datetime`.

## Resultados da análise (para validares os visuais e escreveres os
insights nas caixas de texto do relatório)

### 1. Padrões sazonais
- **Diário**: consumo mínimo por volta das 06h–07h (~50 000 kWh totais),
  sobe ao longo da manhã, mantém um patamar elevado à tarde e atinge o
  pico às **20h** (~98 000 kWh) — consistente com iluminação e climatização
  ao final do dia. Padrão idêntico nas 3 zonas.
- **Semanal**: consumo bastante estável de segunda a sábado
  (71 000–72 500 kWh em média), com queda ao **domingo** (~67 700 kWh,
  cerca de -5%) — indício de atividade comercial/industrial mais baixa.
- **Anual**: consumo mais baixo em nov/dez (~63 800–65 100 kWh) e claramente
  mais alto em **julho/agosto** (~85 700–88 200 kWh, pico do Verão), refletindo
  o uso intensivo de ar condicionado no calor marroquino.

### 2. Correlação com fatores ambientais
| Variável | Zona 1 | Zona 2 | Zona 3 | Total |
|---|---|---|---|---|
| Temperatura | 0.44 | 0.38 | 0.49 | **0.49** |
| Humidade | -0.29 | -0.30 | -0.23 | -0.30 |
| Vel. do vento | 0.17 | 0.15 | 0.28 | 0.22 |

A **temperatura tem a correlação mais forte** com o consumo em todas as
zonas (positiva: mais calor → mais consumo, provavelmente ar condicionado).
A humidade tem correlação negativa moderada (dias mais húmidos/nublados
tendem a ter menos consumo — possivelmente ligado a temperaturas mais
amenas). A velocidade do vento tem a correlação mais fraca das três,
embora seja a mais forte para a Zona 3 (0.28).

### 3. Diferenças entre zonas
- **Zona 1** domina o consumo total (**45.4%** do total), seguida da
  **Zona 2 (29.5%)** e **Zona 3 (25.0%)**.
- A **Zona 3** é a mais sazonal/sensível ao clima: em dezembro consome em
  média ~11 000 kWh, mas em julho sobe para ~28 200 kWh (quase **2.5x**) —
  o maior salto sazonal das três zonas. Isto sugere uma zona com forte
  carga de arrefecimento (ex.: comercial/climatizada) mais do que
  aquecimento.
- A **Zona 1** é a mais estável ao longo do ano (varia entre ~29 000 e
  ~36 400 kWh) e tem a maior carga base, sugerindo um perfil misto
  residencial/urbano de maior escala.
- A **Zona 2** tem um comportamento intermédio, com ligeira subida no
  Inverno em comparação com a Zona 3 (pode incluir aquecimento).
- As três zonas estão fortemente correlacionadas entre si (Zona1↔Zona2:
  0.84; Zona1↔Zona3: 0.75; Zona2↔Zona3: 0.57) — sobem e descem juntas ao
  longo do dia, mas a Zona 3 é a que mais diverge das outras duas.

## Bónus entregue já
Além destes ficheiros, publiquei um dashboard interativo (artifact) com
todos estes gráficos já construídos e navegáveis, para veres os resultados
imediatamente sem precisares de abrir o Power BI.
