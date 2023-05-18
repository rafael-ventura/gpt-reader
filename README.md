
Análise Algorítmica do problema da Árvore Geradora Mínima














Grupo A4
Elmo Sanches, Hernan Matiello, Rafael Cantanhede, Santiago Pacheco e Vitor Indio







Introdução

O Algoritmo de Prim é um algoritmo que pertence à teoria dos grafos e é usado para encontrar uma árvore geradora mínima em um grafo conexo com pesos. Uma árvore geradora mínima de um grafo é uma árvore que conecta todos os vértices do grafo e tem o menor peso total possível, ou seja, a soma dos pesos de todas as arestas da árvore é a menor possível.

Explicação do problema

A árvore geradora mínima de um grafo é uma subárvore que conecta todos os vértices do grafo e tem o menor peso total possível. O problema é encontrar essa árvore geradora mínima. Esse problema é relevante em muitas aplicações da vida real, como a construção de redes de telecomunicações ou a distribuição de energia elétrica, onde se deseja conectar todos os pontos com o menor custo possível.



Definição formal do problema

Dado um grafo conexo e não-direcionado G = (V, E) com peso atribuído a cada aresta e , o problema é encontrar uma árvore geradora T de G tal que a soma dos pesos das arestas de T seja a menor possível.

Entrada: Um grafo conexo e não direcionado G com um peso atribuído a cada aresta.
Questão: Encontrar uma árvore geradora T de G tal que a soma dos pesos das arestas de T seja a menor possível.
Saída: A árvore geradora mínima T de G.



Exemplos de instâncias do problema

Exemplo 1: 
Um grafo com 5 vértices e as seguintes arestas e pesos: (A-B, 1), (A-C, 3), (B-C, 4), (B-D, 2), (C-D, 5), (C-E, 6), (D-E, 7).
Exemplo 2: 
Um grafo com 4 vértices e as seguintes arestas e pesos: (A-B, 5), (B-C, 10), (C-D, 5), (D-A, 10).
Exemplo 3: 
Um grafo com 3 vértices e as seguintes arestas e pesos: (A-B, 2), (B-C, 3), (C-A, 1).

Algoritmo
Descrição esquemática do algoritmo

O Algoritmo de Prim opera por meio da construção progressiva da árvore geradora mínima, incluindo um vértice de cada vez. O processo tem início a partir de um vértice escolhido aleatoriamente, e prossegue sempre selecionando a aresta de menor peso que estabelece uma conexão entre um vértice já incorporado à árvore e um vértice que ainda não foi incluído.

Descrição formal do algoritmo

Começamos escolhendo um vértice, ou ponto, do nosso gráfico para ser o ponto de partida. Podemos escolher qualquer vértice que quisermos - é arbitrário.

Em seguida, procuramos todas as arestas, ou linhas, que se conectam a esse vértice e escolhemos a de menor peso. O peso aqui pode representar muitas coisas diferentes dependendo do que o gráfico está modelando, mas você pode pensar nele como o custo ou a distância.

Adicionamos essa aresta de menor peso à nossa "árvore", que é apenas o conjunto de vértices e arestas que escolhemos até agora.

Agora, repetimos os passos 2 e 3, mas sempre considerando os vértices que já estão na nossa árvore. Continuamos fazendo isso até que todos os vértices estejam na árvore.


Algoritmo em Javascript


/**
 * Implementação do algoritmo de Prim em JavaScript.
 *
 * @param {Object} grafo - O grafo onde o algoritmo de Prim será aplicado. Deve ser um objeto onde as chaves são os vértices e os valores são objetos que representam as arestas conectadas a cada vértice, com os pesos das arestas como valores.
 * @param {string} inicio - O vértice de onde o algoritmo de Prim deve começar.
 * @returns {Array} A árvore geradora mínima do grafo, representada como uma lista de arestas. Cada aresta é um array de três elementos: o vértice de origem, o vértice de destino e o peso da aresta.
 */
function prim(grafo, inicio) {
    // Inicializa a árvore geradora mínima como uma lista vazia.
    const arvore = [];


    // Inicializa o conjunto de vértices visitados como um conjunto vazio.
    const visitados = new Set();


    // Inicializa a lista de arestas como uma lista vazia.
    let arestas = [];


    // Para cada vértice adjacente ao vértice inicial
    for (let para in grafo[inicio]) {
        // Obtemos o custo da aresta
        let custo = grafo[inicio][para];


        // Adicionamos a aresta na lista de arestas a serem processadas
        arestas.push([custo, inicio, para]);
    }


    while (arestas.length > 0) {
        // Ordenamos as arestas para simular uma heap de mínimos
        arestas.sort((a, b) => a[0] - b[0]);


        // Extraímos a aresta de menor custo
        const [custo, de, para] = arestas.shift();


        // Se o vértice de destino da aresta não foi visitado ainda
        if (!visitados.has(para)) {
            // Marcamos o vértice como visitado
            visitados.add(para);


            // Adicionamos a aresta à árvore geradora mínima
            arvore.push([de, para, custo]);


            // Para cada vértice adjacente ao vértice que acabamos de visitar
            for (let vizinho in grafo[para]) {
                // Se o vértice adjacente não foi visitado ainda
                if (!visitados.has(vizinho)) {
                    // Obtemos o custo da aresta
                    let custoVizinho = grafo[para][vizinho];


                    // Adicionamos a aresta na lista de arestas a serem processadas
                    arestas.push([custoVizinho, para, vizinho]);
                }
            }
        }
    }


    // Retornamos a árvore geradora mínima
    return arvore;
}

Exemplos da solução algorítmica

Exemplo 1

Vamos começar com um grafo contendo cinco vértices, representados pelas letras A, B, C, D e E. As arestas entre os vértices e seus respectivos pesos são dados da seguinte maneira:

(A, B): peso 2
(A, C): peso 3
(B, C): peso 1
(B, D): peso 1
(C, D): peso 4
(C, E): peso 5
(D, E): peso 2


Primeiro, vamos começar com o vértice A. Entre as arestas que saem do vértice A, a aresta (A, B) tem o menor peso, que é 2. Portanto, selecionamos essa aresta e adicionamos à nossa árvore em crescimento.

Agora, temos dois vértices em nossa árvore, A e B. As arestas que saem desses vértices e que levam a vértices que ainda não estão em nossa árvore são (A, C), (B, C) e (B, D). Entre essas, a aresta (B, D) tem o menor peso, que é 1. Portanto, selecionamos essa aresta e a adicionamos à nossa árvore.

Em seguida, temos três vértices em nossa árvore, A, B e D. As arestas que saem desses vértices e que levam a vértices que ainda não estão em nossa árvore são (A, C), (B, C), (D, E) e (C, D). Entre essas, a aresta (D, E) tem o menor peso, que é 2. Portanto, selecionamos essa aresta e a adicionamos à nossa árvore.

Finalmente, temos quatro vértices em nossa árvore, A, B, D e E. As arestas que saem desses vértices e que levam a vértices que ainda não estão em nossa árvore são (A, C), (B, C) e (C, D). Entre essas, a aresta (B, C) tem o menor peso, que é 1. Portanto, selecionamos essa aresta e a adicionamos à nossa árvore.

Agora, todos os vértices estão em nossa árvore e formamos uma árvore geradora mínima. As arestas na árvore geradora mínima são (A, B), (B, D), (D, E) e (B, C), e o peso total da árvore é 6.


Portanto, a árvore geradora mínima para esse grafo é composta pelas arestas (A, B): peso 2, (B, D): peso 1, (D, E): peso 2 e (B, C): peso 1, com um peso total de 6.



Exemplo 2

Agora no segundo exemplo, veja a imagem do grafo a seguir:





Temos 6 vértices, que resultará num resultado de 5 arestas para a árvore. 

Primeiro passo é arbitrariamente escolher um vértice. Imagine que escolhemos o vértice 1.

Depois seguindo o algoritmo, escolhemos o menor caminho, que é a aresta que liga até o vértice 2, com peso 2.

Depois, escolhemos o menor caminho para um vértice que não está conectado, e este será o vértice 0.

O próximo passo será do vértice 2 ao 3, com a aresta de valor 2.

O penúltimo passo será do vértice 2 ao 5, com a aresta de valor 4(note que a aresta que liga o vértice 3 ao 5 também vale 4, então não importará qual o caminho seguir.

E por último, o caminho a seguir será a aresta que liga o 5 ao 4, com valor 3.


Note que o resultado final, a árvore geradora mínima, está sublinhada em laranja.

Análise do algoritmo

O algoritmo de Prim é um algoritmo guloso, o que significa que ele faz a escolha localmente ótima em cada etapa com a esperança de que essas escolhas locais levarão a uma solução globalmente ótima. O algoritmo começa com um vértice arbitrário e seleciona a aresta de menor peso conectada a ele. Em seguida, adiciona essa aresta à árvore e repete o processo para os vértices adjacentes, sempre escolhendo a aresta de menor peso que conecta um vértice dentro da árvore a um vértice fora da árvore.


Complexidade de Tempo

No caso do algoritmo de Prim, quando usamos uma estrutura de dados chamada "heap de Fibonacci", a complexidade do tempo é O(E log V). Aqui, E é o número de arestas e V é o número de vértices no gráfico. Então, basicamente, estamos dizendo que o tempo que leva para executar o algoritmo é proporcional ao número de arestas vezes o logaritmo do número de vértices.

Inicialmente, criamos uma fila de prioridade com todos os vértices. Isso leva tempo proporcional ao número de vértices, ou O(V).

Em seguida, temos um loop que continua até que todos os vértices estejam na nossa árvore. Em cada iteração do loop, removemos o vértice com o menor peso da fila de prioridade, o que leva tempo logarítmico, ou O(log V). Como a altura de uma heap é logarítmica no número de elementos (porque é uma árvore binária completa), a remoção do elemento mínimo de uma heap tem complexidade de tempo O(log V), onde V é o número de vértices do grafo.

Após remover um vértice, atualizamos os pesos dos vértices adjacentes na fila de prioridade. Isso leva tempo proporcional ao número de arestas, ou O(E), e temos que fazer isso para cada vértice, então acabamos com O(E log V) para essa parte

Portanto, somando tudo, obtemos uma complexidade de tempo total de O(V + E log V). Mas, uma vez que geralmente temos mais arestas do que vértices (pelo menos em gráficos conectados), simplificamos isso para O(E log V).



Prova de corretude do algoritmo

A corretude do algoritmo de Prim é baseada na propriedade de corte da árvore geradora mínima. Esta propriedade diz que para qualquer corte de um grafo ponderado e conectado, a aresta de menor peso que atravessa o corte está na árvore geradora mínima. O algoritmo de Prim opera construindo uma árvore geradora mínima, um vértice de cada vez, e em cada etapa escolhe a aresta de menor peso que liga a árvore parcialmente construída ao restante do grafo. Assim, a propriedade do corte é mantida em todas as etapas, o que nos permite concluir que o resultado final é de fato uma árvore geradora mínima.

Base: Iniciamos com um vértice aleatório como a árvore geradora mínima parcial. Neste caso, a propriedade do corte é trivialmente verdadeira, pois não há corte para considerar.


Passo de indução: Suponha que a propriedade do corte seja verdadeira para a árvore parcialmente construída. Quando adicionamos uma aresta, escolhemos a de menor peso que liga a árvore ao restante do grafo. Assim, a propriedade do corte é mantida.

Portanto, a árvore final construída pelo algoritmo de Prim é uma árvore geradora mínima.
Descrição Formal da Prova por Indução 

Base: A árvore começa com um único vértice e sem arestas, portanto, é trivialmente uma AGM para o subgrafo induzido pelo vértice inicial.

Passo de indução: Suponha que, após k passos, temos uma AGM parcial T para um subgrafo induzido pelos vértices V(T). Considere adicionar uma nova aresta e(v,u) ao conjunto T, onde v pertence a V(T) e u não pertence a V(T). Se e é a aresta de menor peso que conecta V(T) a um vértice fora de V(T), então T' = T U {e} também é uma AGM parcial.

Para provar isso, suponha que T' não seja uma AGM parcial e exista outra árvore T'' que seja uma AGM parcial com peso menor que T'. Removendo a aresta e de T'', teríamos uma árvore geradora que contém T, o que significa que existe uma aresta e' que conecta V(T) a um vértice fora de V(T) e tem peso menor que e. Mas isso contradiz nossa suposição de que e é a aresta de menor peso conectando V(T) a um vértice fora de V(T). Portanto, T' deve ser uma AGM parcial.

Conclusão e Discussões

O algoritmo de Prim é eficiente para grafos densos, pois a complexidade do tempo é proporcional ao número de vértices. No entanto, para grafos esparsos, o algoritmo de Kruskal pode ser uma escolha melhor, pois sua complexidade de tempo é proporcional ao número de arestas.

Além disso, o algoritmo de Prim só funciona para grafos conectados. Se o grafo não for conectado, o algoritmo falhará, pois não será capaz de alcançar todos os vértices.

Uma vantagem do algoritmo de Prim é que ele é mais fácil de implementar para grafos representados como matrizes de adjacência em vez de listas de adjacência, ao contrário do algoritmo de Kruskal.

O algoritmo de Prim é uma ferramenta importante na teoria dos grafos e tem aplicações em muitos campos, como redes de computadores, circuitos elétricos e planejamento de rotas. Ele permite encontrar a árvore geradora mínima de um grafo conectado, proporcionando uma maneira eficiente de minimizar o peso total das arestas.



Estrutura dos Slides

Slide 1 - Colocar o titulo do trabalho (Análise Algorítmica do problema da Árvore Geradora Mínima) e o nome dos integrantes (Elmo Sanches, Hernan Matiello, Rafael Cantanhede, Santiago Pacheco E Vitor Índio)

Slide 2 - Qual problema da Árvore Geradora Mínima?
(Slide somente com o titulo acim)

Slide 3 - Explicação do problema
Aqui eu quero que voce coloque um texto simples baseado no meu trabalho

Slide 4 - Definição formal do problema
Colocar texto baseado no trabalho

Slide 5 - Exemplos de instâncias
 Colocar os exemplos da instancia

Slide 6 - Descrição esquemática do algoritmo
Baseie-se no trabalho

Slide 7 - Descrição esquemática do algoritmo
Baseie-se no trabalho

Slide 8 - Algoritmo De Prim - Solução Algorítmica
Colocar o Codigo de uma maneira enxuta e simples para que a gente possa mostrar. pode ser em pseudocodigo e tem que ser em pt-br

Slide 9 a 12 - Solução Algorítmica
Use esse slide para colocar a resolucao do exemplo mais simples envolvendo algoritmo de prim e usando os passos do trabalho

Slide 13 a 16 - Complexidade do Algoritmo e prova
Foque em mostrar a complexidade de espaco, como chegamos ao seu calculo e reseve o slide 16 para falar rapidamente da corretude.
Slide 17 - Conclusoes
Se baseie no trabalho








