# Power System Reliability Analyzer - v2.0

Aplicação desktop desenvolvida em Python (PyQt6, Pandas, Matplotlib) para extração, consolidação e visualização de dados de saída do simulador RevEla. O software opera como um parser de arquivos em lote, processando métricas de confiabilidade elétrica (globais, por tipo de falha, por região e por barramento).

---

## Funcionalidades

### 1. Processamento de Dados (Lote / Multicasos)
* **Varredura de Diretórios (*Crawler*):** O algoritmo utiliza as bibliotecas `os` e `re` para mapear subdiretórios a partir de um *path* raiz. A identificação de pastas válidas utiliza a expressão regular `^C[\d\.\_]+[sS]`.
* **Extração Topológica:** Executa o *parsing* do arquivo `Template System.csv` para mapear a variável "Carga Ativa (MW)" e o metadado "Região" de cada barramento do sistema elétrico analisado.
* **Consolidação de Métricas:** Varre o diretório de resultados padronizado (`Results_STA_STR_1%`) extraindo indicadores teóricos do arquivo `Final Reliability Indices.csv`. Extrai e concatena o Tempo de Simulação e a base de Anos Simulados por caso.

### 2. Módulos de Visualização GUI
* **Visão Global:** Renderiza gráficos de barras comparando os valores de um indicador selecionado entre todos os casos lidos. Inclui módulo QDialog para exibição em matriz tabular dos limites de confiança (Inferior/Superior a 95%) calculados na simulação.
* **Análise Detalhada (Barra/Região):** Instancia componentes `QTableView` com aplicação de mapeamento de cor (gradiente RGB) via `ItemDataRole.BackgroundRole`. Plota gráficos de distribuição de Pareto com controle binário (on/off) para plotagem da curva acumulada de 80%.
* **Comparação Cruzada:** Rotina de agrupamento via `pd.merge(how='outer')` para comparar até 3 casos simultâneos. Gera figuras de barras agrupadas (`numpy.arange` offset) em 3 níveis:
  * **Por Tipo:** Compara variáveis de Geração, Transmissão e Geração+Transmissão.
  * **Por Região:** Agrega valores conforme subdivisão topológica.
  * **Custom View Mode (Barras):** Interface de filtro cruzado. Permite queries de busca por string (ID/Nome do barramento), filtro por variável "Região" e ordenação (alfabética ou por valor acumulado total no conjunto de casos selecionados).

### 3. I/O e Exportação
* **Imagem:** Exporta a `matplotlib.figure.Figure` instanciada em tela para formato `.png` com resolução de 300 DPI e formatação geométrica `bbox_inches='tight'`.
* **Planilha:** Exporta o `pandas.DataFrame` ativo na memória para arquivo `.csv` utilizando codificação padrão e separador de ponto e vírgula (`;`).

---

## Deploy e Execução

A aplicação é distribuída como um binário executável autônomo (compilado via Nuitka), não exigindo instalação de interpretador Python ou bibliotecas no host de execução.

### Setup (config.txt)
O executável requer um arquivo plano de texto chamado `config.txt` alocado no mesmo diretório de execução. O arquivo deve conter uma única linha especificando o *path* raiz e a constante inteira de regiões estruturais, separados por ponto e vírgula:

```text
C:\Caminho\Ate\Diretorio\Raiz_Dos_Casos;7
