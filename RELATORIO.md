# Analise comparativa de algoritmos de ordenacao em Java

**Repositorio:** https://github.com/LauPayassa/benchmark-algoritmos-java  
**Linguagem:** Java 17 ou superior  
**Experimento:** execucao serial e paralela com geracao de CSV

## Resumo

Este trabalho apresenta uma analise experimental do desempenho de quatro algoritmos de ordenacao: Bubble Sort, Quick Sort, Merge Sort e Counting Sort. Cada algoritmo possui uma implementacao sequencial e uma implementacao paralela. O framework de testes varia o tamanho e a natureza dos dados, a quantidade de threads e realiza cinco amostras por configuracao. Os tempos sao registrados em nanossegundos em arquivo CSV e podem ser visualizados com uma aplicacao Swing.

Os resultados confirmam as diferencas esperadas de complexidade. Bubble Sort apresenta crescimento significativamente maior, enquanto Quick Sort, Merge Sort e Counting Sort sao mais adequados para entradas maiores. A paralelizacao permite investigar o custo e o beneficio da divisao do trabalho, embora, para entradas pequenas, o custo de criar threads possa superar o ganho de processamento.

## 1. Introducao

Algoritmos de ordenacao sao usados como base para buscas, consultas e processamento de dados. A escolha do algoritmo influencia diretamente o tempo de execucao, o uso de memoria e a capacidade de aproveitar processadores multicore.

Foram escolhidos quatro algoritmos com caracteristicas diferentes:

- **Bubble Sort:** simples, mas com complexidade quadratica no caso medio e no pior caso.
- **Quick Sort:** complexidade media `O(n log n)` e particionamento recursivo.
- **Merge Sort:** complexidade `O(n log n)` garantida, usando divisao e intercalacao.
- **Counting Sort:** pode atingir `O(n + k)` quando o intervalo de valores `k` e controlado.

Embora o enunciado utilize a expressao “algoritmos de busca”, os metodos listados sao algoritmos de **ordenacao**. Portanto, o experimento foi implementado como comparacao de ordenacao, seguindo os algoritmos fornecidos no enunciado.

## 2. Metodologia

O programa `Benchmark.java` funciona como um framework de testes. Para cada combinacao de algoritmo, tamanho, tipo de entrada, modo de execucao e quantidade de threads, ele:

1. gera uma entrada deterministica usando uma semente;
2. copia a entrada para evitar que uma execucao altere a proxima;
3. mede apenas o tempo da ordenacao com `System.nanoTime()`;
4. verifica automaticamente se o vetor final esta ordenado;
5. grava os parametros e o tempo no CSV.

Foram utilizadas:

- tamanhos de **1.000, 5.000 e 10.000 elementos**;
- dados **aleatorios, ordenados, inversamente ordenados e com duplicatas**;
- uma execucao serial;
- execucoes paralelas com **1, 2 e 4 threads**;
- **5 amostras** para cada configuracao;
- `ForkJoinPool` para dividir o trabalho paralelo.

O experimento produz 960 medicoes, alem do cabecalho, no arquivo `resultados/benchmark.csv`. O numero de threads e uma configuracao logica do programa. O CSV tambem registra `processadores_disponiveis`, obtido por `Runtime.getRuntime().availableProcessors()`, permitindo documentar o ambiente de cada execucao. A quantidade de nucleos fisicos efetivamente utilizada depende do sistema operacional e do ambiente de execucao.

## 3. Resultados e discussao

O CSV gerado possui as colunas:

```text
algoritmo,modo,tamanho,tipo_dados,threads,processadores_disponiveis,amostra,tempo_ns
```

Uma execucao validada deste projeto produziu 961 linhas. Considerando somente as 60 medicoes seriais de cada algoritmo, os tempos medios observados foram:

| Algoritmo | Media serial observada (ns) | Interpretacao |
|---|---:|---|
| Bubble Sort | 9.810.414 | Maior custo; comportamento quadratico |
| Quick Sort | 207.486 | Bom desempenho medio |
| Merge Sort | 281.835 | Desempenho estavel, com memoria auxiliar |
| Counting Sort | 194.884 | Muito eficiente quando o intervalo e adequado |

Esses valores sao ilustrativos de uma execucao e nao devem ser tratados como constantes universais. Resultados podem variar conforme processador, JVM, sistema operacional, aquecimento do JIT, carga do computador e quantidade de memoria disponivel.

Para a analise estatistica final, recomenda-se calcular media, mediana, desvio-padrao e intervalo minimo/maximo agrupando por `algoritmo`, `modo`, `tamanho`, `tipo_dados` e `threads`. O arquivo CSV foi estruturado para permitir esse processamento em Java, Python, Excel ou outra ferramenta.

Espera-se que:

- Bubble Sort cresca rapidamente com o tamanho da entrada;
- Quick Sort seja rapido em dados gerais, mas dependa da qualidade do particionamento;
- Merge Sort apresente comportamento previsivel e seja adequado a divisao em tarefas;
- Counting Sort seja especialmente competitivo com muitas duplicatas e intervalo de valores pequeno;
- a execucao paralela tenha maior potencial em entradas grandes;
- com poucas entradas, o custo de criar pools e reunir blocos possa reduzir ou eliminar o ganho da paralelizacao.

O grafico Swing incluido (`GraficoSwing.java`) desenha uma serie temporal dos tempos do CSV. Ele e uma visualizacao basica e serve como extra do trabalho; analises academicas mais completas podem gerar graficos agrupados por algoritmo, tamanho e numero de threads.

## 4. Conclusao

O projeto implementa um ambiente reproduzivel para comparar ordenacao serial e paralela em Java. Ele atende a variacao de dados, tamanhos, threads e amostras, registra os resultados em CSV e fornece uma visualizacao Swing.

Os resultados mostram que nao existe um algoritmo melhor para todos os cenarios. Bubble Sort e util didaticamente, mas inadequado para entradas grandes. Quick Sort e Merge Sort oferecem melhor escalabilidade assintotica, enquanto Counting Sort pode ser superior quando seus pressupostos sobre o intervalo de valores sao atendidos. A paralelizacao deve ser avaliada junto com o tamanho do problema, pois threads introduzem custos adicionais.

Como trabalhos futuros, podem ser adicionados Selection Sort e Insertion Sort, tamanhos maiores, aquecimento da JVM, mais repeticoes, medicao de memoria, exportacao de medias e uma interface com filtros dinamicos.

## 5. Referencias

1. CORMEN, T. H. et al. *Introduction to Algorithms*. MIT Press.
2. Oracle. *Java Platform, Standard Edition Documentation*. https://docs.oracle.com/en/java/
3. Oracle. *Fork/Join Framework*. https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html
4. SEDGEWICK, R.; WAYNE, K. *Algorithms*. Addison-Wesley.

## 6. Anexos

Os codigos-fonte utilizados estao disponiveis no GitHub:

https://github.com/LauPayassa/benchmark-algoritmos-java

Arquivos principais:

- `Benchmark.java`
- `GeradorDados.java`
- `BubbleSort.java`
- `QuickSort.java`
- `MergeSort.java`
- `CountingSort.java`
- `ParallelSortSupport.java`
- `GraficoSwing.java`

Para reproduzir:

```bash
javac *.java
java Benchmark
java GraficoSwing
```
