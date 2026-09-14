---
title: dp-digito
date: 2025-06-28 22:07:45
tags:
---

PD de Dígito

Requisitos 

- Programação dinâmica
- Funções recursivas

Motivação

Problema geral

Dado dois inteiros $l, r$  $(0 \le l \le r \le 10^{18})$, o objetivo é descobrir quantos inteiros $x$, tal que $l \le x \le r$ e x satistaz um predicado $p(x)$, onde $p(x)$ é uma propriedade sobre os dígitos de $x$.

Exemplos de $p(x)$:

- x termina com o dígito 0
- x não possui o dígito 4
- a metade dos dígitos x são iguais
- a soma dos dígitos de x é um primo
- x não tem o 4 e 11 como subsequência dos seus dígitos
  

Exemplo 1 (Posição) 

![ex1](/home/pedro/codigos/clube-de-programacao-utfpr/oficinas/pd-digito/ex1.svg)



```c++
int dp(vector<int> &digitos, int i = 0) {
  if (i == digitos.size())
    return 1;
  int ans = 0;
  for (int d = 0; d <= 9; d++)
    ans += dp(digitos, i + 1);
  return ans;
}
```

Exemplo 2 (Prefixo empatado)

![ex2](/home/pedro/codigos/clube-de-programacao-utfpr/oficinas/pd-digito/ex2.svg)

```C++
int dp(vector<int> &digitos, int i = 0, bool empatado = true) {
  if (i == digitos.size())
    return 1;
  int ans = 0;
  int r = empatado ? digitos[i] : 9;
  for (int d = 0; d <= r; d++){
    bool new_empatado = empatado && (d == r);
    ans += dp(digitos, i + 1, new_empatado);
  }
  return ans;
}
```



Exemplo 3 ($p(x)$)

![ex2](/home/pedro/codigos/clube-de-programacao-utfpr/oficinas/pd-digito/ex2.svg)

```c++
int dp(vector<int> &digitos, int i = 0, bool empatado = true, bool px = false) {
  if (i == digitos.size()) {
    return px;
  }
  int ans = 0;
  int r = empatado ? digitos[i] : 9;
  for (int d = 0; d <= r; d++){
    bool new_empatado = empatado && (d == r);
    ans += dp(digitos, i + 1, new_empatado, px || d == 5);
  }
  return ans;
}
```

Observações

- Em geral esses três parâmetros sempre serão necessários
- Novos parâmetros podem ser adicionados, por exemplo, se $p(x)$ é sensível a zeros à esquerda (e.g. $p(x) := \mbox{o primeiro dígito de $x$ é ímpar}$)
- Subproblemas repetidos irão aparecer frequentemente

Exemplo 4 (Memoização)

```c++
int dp(vector<int> &digitos, int i = 0, bool empatado = true, bool px = false) {
  if (i == digitos.size())
    return px;
  int &ans = memo[i][empatado][px];
  if (ans != -1)
    return ans;
  ans = 0;
  int r = empatado ? digitos[i] : 9;
  for (int d = 0; d <= r; d++){
    bool new_empatado = empatado && (d == r);
    ans += dp(digitos, i + 1, new_empatado, px || d == 5);
  }
  return ans;
}
```



```c++
int solve(int n){
    vi digitos = num2v(n);
    memset(memo, -1, sizeof(memo));
    return dp(digitos);
}
```

Exemplo 5 ([l, r])

```c++
int solve(int l, int r){
    return solve(r) - solve(l-1);
}
```

Problema: [BEE 2736 - Números de Avalon - Resumo](https://www.beecrowd.com.br/judge/pt/problems/view/2736)

Números de Avalon := são números naturais que não tem $m$ e $y$ como subsequência de seus dígitos, e $m$ tem apenas 1 dígito, e $y$ tem exatamente 2 dígitos.

Objetivo: Dados $m$ e $y$, descobrir qual é o $k$-ésimo número de Avalon.



Exemplo $y = 12$ e $m = 3$



![avalon-1](/home/pedro/codigos/clube-de-programacao-utfpr/oficinas/pd-digito/avalon-1.svg)

16 primeiros números naturais



![avalon-3](/home/pedro/codigos/clube-de-programacao-utfpr/oficinas/pd-digito/avalon-3.svg)

Números de avalan destacados





![avalon-4](/home/pedro/codigos/clube-de-programacao-utfpr/oficinas/pd-digito/avalon-4.svg)

quantidade de números de avalon



Subproblemas

1. Contar quantos números de avalon existem entre os $n$ primeiros números naturais.
2. Descobrir o primeiro número onde a quantidade de números de avalon até ele é igual a k.





Lista de exercícios

- Problemas iniciais:

[Elevador Espacial - Sub 2012](https://www.beecrowd.com.br/judge/pt/problems/view/1226)

[GONE - G-One Numbers](https://www.spoj.com/problems/GONE/en/)

[C. Classy Numbers](https://codeforces.com/problemset/problem/1036/C)

[Count The Bits](https://vjudge.net/problem/Gym-101982D)

[E. Living Sequence](https://codeforces.com/problemset/problem/1811/E)

- Problemas interessantes:

[Enigma - Final 2017](https://www.beecrowd.com.br/judge/pt/problems/view/2699)

[O Problema do 7 x 1](https://www.beecrowd.com.br/judge/pt/problems/view/3099)

[Contagem de Dígitos](https://www.beecrowd.com.br/judge/pt/problems/view/1138)

[C. Unlucky Numbers](https://codeforces.com/contest/1808/problem/C)

[B. Fun with Stones Sub 2022](https://codeforces.com/gym/103960/problem/B)



Referências 

- [Digit DP. Disponível em: codeforces.com/blog/entry/53960](https://codeforces.com/blog/entry/53960)
-  [PROTIVAUNESP. Digit DP. Disponível em: https://youtu.be/Ls57WZHu1BE](https://www.youtube.com/watch?v=Ls57WZHu1BE&ab_channel=PROTIVAUNESP)
