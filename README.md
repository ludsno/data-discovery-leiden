# Data Lake Discovery: Leiden vs. SCD

Implementação e estudo experimental baseados no artigo *"Self-supervised data lakes discovery"* (Putrama & Martinek, 2024).

Este repositório reproduz o pipeline de ingestão de metadados e construção de grafos de similaridade proposto no estado da arte, introduzindo uma alteração na etapa de **Detecção de Comunidades** para testar uma hipótese de otimização via modularidade.

## O Experimento
O trabalho original utiliza o algoritmo **SCD (Silhouette Community Detection)** como motor de agrupamento.
Este projeto implementou e testou a substituição do SCD pelo algoritmo de **Leiden** (evolução do Louvain), com o objetivo de verificar se a maximização da **modularidade** produziria clusters de tabelas semanticamente mais coerentes do que a otimização baseada em silhueta.

## Stack e Conceitos
* **Linguagem:** Python 3.x (Gerenciamento via Conda)
* **Graph Mining:** `NetworkX`, `CDlib` (Community Discovery Library)
* **Algoritmos:**
    * **Indexação:** LSH (Locality Sensitive Hashing)
    * **Similaridade:** J-Maxsym (Weighted Jaccard)
    * **Clustering:** Leiden vs. SCD
* **Análise:** `Pandas`, `Numpy`, `Scikit-learn` (Métricas de Classificação)

## Resultados e Análise Técnica

A validação comparou os clusters gerados pelo método proposto (Leiden) contra o baseline (SCD) utilizando métricas supervisionadas (Acurácia, Precision, Recall) em datasets rotulados.

### Observação
A aplicação do algoritmo de Leiden manteve as métricas de performance em patamares similares ao baseline, sem o ganho estatístico esperado, apesar da robustez teórica do Leiden sobre o Louvain/SCD em redes complexas.

### Conclusão do Benchmark
A análise dos resultados sugere uma dependência forte entre a métrica de similaridade e o algoritmo de corte:
1.  **O Algoritmo Original (SCD)** otimiza o *Silhouette Coefficient*, que prioriza a **separação** e distância entre grupos.
2.  **O Algoritmo Testado (Leiden)** otimiza a *Modularidade*, que prioriza a **densidade** de conexões internas.

**Veredito:** A topologia de grafo gerada pela métrica *J-Maxsym* tenderia a criar estruturas esparsas (favorecendo o SCD). O grafo não apresentou a densidade de conexões necessária para que algoritmos baseados em modularidade (como o Leiden) superassem o método original. O experimento valida a escolha do SCD para este tipo específico de representação de metadados.

## Estrutura do Repositório

* `Discovery.LSH.Networkx...ipynb`: Pipeline principal (Ingestão -> LSH -> Construção do Grafo -> Validação). Contém a lógica de comparação.
* Função `get_leiden_communities()`: Implementada no notebook [Discovery.LSH.Networkx.Module.ipynb](Discovery.LSH.Networkx.Module.ipynb), utilizando `leidenalg`/`igraph` para particionamento por modularidade. Esta função substitui o SCD no benchmark proposto.

## Créditos e Autoria

- Código base e ideia experimental: derivados do trabalho de I Made Putrama & Tomáš Martinek (2024) – "Self-supervised data lakes discovery" (licença MIT). Ver o arquivo [LICENSE](LICENSE) para os termos.
- Contribuições deste repositório: implementação da função `get_leiden_communities()` (Leiden), pequenos ajustes no pipeline e parâmetros (p.ex., `n_init` do MiniBatchKMeans no SCD, tratamento de exceções em `infer_types`, logs de execução), documentados em [mudancas_minhas.md](mudancas_minhas.md).
- A estrutura e os notebooks foram adaptados para permitir a comparação direta entre SCD e Leiden no grafo de metadados construído via LSH/J‑Maxsym.

---
*Projeto desenvolvido no contexto de Iniciação Científica em Data Lakes e Ciência de Dados, sob a orientação de Danilo Fernandes e Tamer Cavalcante, @ Laboratório Orion, UFAL.*
