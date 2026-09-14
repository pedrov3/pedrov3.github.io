---
title: AtCoder ABC 355 E - Guess the Sum
date: 2024-05-31 21:33:37
tags: 
- BFS
- Soma de prefixo
---

O texto a seguir é uma tradução para português (com adições)  do [editorial](https://atcoder.jp/contests/abc355/editorial/10079) do problema [ABC355 - E - Guess the sum](https://atcoder.jp/contests/abc355/tasks/abc355_e) que até o momento não foi traduzido.

<!-- more -->

Inicialmente se você já tiver visto o problema [D - Divide Interval - ABC349](https://atcoder.jp/contests/abc349/tasks/abc349_d) pode notar sua semelhança, principalmente pois o intervalo em que você pode fazer perguntas é igual uma árvore de segmentos. A diferença deste problema é que você pode subtrair, o que permite dividir o intervalo de consulta em um número menor de sub-intervalos. Por exemplo, para $N=3, L=1,R=7$, se você dividir o intervalo de perguntas usando a solução do problema [ABC349D](https://atcoder.jp/contests/abc349/tasks/abc349_d), precisará fazer três perguntas: $A_1$, $A_2 + A_3$ e $A_4 + A_5 + A_6 + A_7$ . Por outro lado, poderíamos com apenas duas consultas obter a resposta, primeiro fazemos $A_0 + A_1 + A_2 + A_3 + A_4 + A_5 + A_6 + A_7$ e depois subtraímos $A_0$ para obter a resposta.

Deste modo, neste problema precisamos construir uma outra solução. A solução descrita a seguir é baseada no vetor de soma acumulada (soma de prefixo).

Para $l, r$ $(0 \le l, r \le 2^N)$ inteiros, defina $S(l,r)$​​​​ como:



$S(l, r) = \begin{cases} 0, & (l = r)\\\\ \sum\limits_{i=x}^{y-1}A_i, & (l < r)\\\\ -S(r, l) & (l > r) \end{cases}$



O que queremos é $S(L,R+1)$, restringido a realizar perguntas para $S(l, r)$ que seguem o formato $S(2^ij, 2^i(j + 1))$. Note que como pela definição anterior $S(l, r)$ está definido mesmo quando $l > r$, temos que $S(2^i(j+1), 2^ij) = -S(2^ij, 2^i(j + 1))$, como esta última é uma consulta válida, podemos assumir que $S(2^i(j + 1), 2 ^i j )$ também é uma pergunta válida.

A partir da definição de $S$, é possível verificar que $S(x, y)$ possui a propriedade transitiva, ou seja, se $S(x, y)$ e $S(y, z)$ são conhecidos, então $S(x, z)$ pode ser encontrado como $S(x, y) + S(y, z)$​.

Fazendo uso dessa propriedade podemos pensar em um grafo definido a partir de $S$. Note que um caminho de $u, v$ neste grafo irá representar $S(u, v)$. Formalmente,  seja $G$ um grafo direcionado onde $V(G) = \{0, 1, \dots, 2^N\}$ e existe uma aresta $(u, v)$ se é possível realizar uma consulta de $u$ pra $v$ ou de $v$ para $u$.

Agora, considere dois vértices $u, v$ cuja distância mínima em $G$ é 2, $S(u, v)$ pode ser obtido através de duas perguntas. Seja $(u, w, v)$ um caminho mínimo de $u$ para $v$,  $S(u, v) = S(u, w) + S(w, v)$, note que como existe uma aresta de $(u, w)$ e de $(w, v)$, ambas $S(u, w)$ e $S(w, v)$ são consultas válidas.

De forma geral, dados dois vértices $u, v$ cuja distância mínima em $G$ é igual a $d$, $S(u, v)$ pode ser encontrado fazendo exatamente $d$ perguntas.

Portanto, é suficiente encontrar o caminho mais curto de $L$ a $R$ no grafo G (com restauração). Como o número de vértices do grafo $G$ é $2^N + 1$ e o número de arestas é $2^{N + 1} - 1$, o caminho mais curto pode ser encontrado com uma complexidade computacional de $O (2^{N})$ usando BFS .

## Referências

- [E - Guess the Sum Editorial by toam](https://atcoder.jp/contests/abc355/editorial/10079)



