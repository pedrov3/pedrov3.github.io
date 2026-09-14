---
title: Deslocamento de Taylor
date: 2024-06-04 20:04:19
tags:
- FFT
- Matemática
---


Este artigo é uma breve explicação de uma técnica conhecida como Deslocamento de Taylor (*Polynomial Taylor Shift*), esta técnica é usada para essencialmente resolver o problema de dado um polinômio $f(x)$ e um inteiro $c$, encontrar um novo polinômio $f(x + c)$.

<!-- more -->

## [Polynomial Taylor Shift](https://judge.yosupo.jp/problem/polynomial_taylor_shift)

### Problema

Dada uma sequência $a = (a_0, a_1, \cdots, a_{N-1})$ de comprimento $N$ e um inteiro $c$.

Vamos definir um polinômio $f(x) = \sum\limits_{i=0}^{N-1} a_i x^i$ de grau $N-1$.

O objetivo é encontrar uma sequência $b = (b_0, b_1, \cdots, b_{N-1})$ que satisfaz $f(x+c) = \sum\limits_{i=0}^{N-1} b_i x^i$, como os números dessa sequência podem ser grandes, imprima cada um deles módulo $998244353$.

Limites: $1 \leq N \leq 2^{19} = 524288; 0 \leq c,a_i < 998244353$

### Solução

Resumo: ideias diretas como usar a interpolação de Lagrange podem ser otimizadas para resolver esse problema em uma complexidade de tempo $\mathcal{O}(N^2)$, que ainda é insuficiente para os limites estabelecidos. A solução esperada para este problema tem complexidade de tempo $\mathcal{O}(N \log N)$ e consiste na expansão de $f(x+c)$, a partir de $f(x) = \sum_{i=0}^{N} a_i x^i$, em seguida através de várias manipulações de somatório é possível deixá-la em uma forma computável através de uma convolução entre duas sequências. Uma descrição mais formal é dada em seguida.

Pelo teorema binomial:

$(a + b)^n  = \sum\limits_{k=0}^n\binom{n}{k}a^kb^{n-k}$,

e pela definição de $f$ podemos expandir $f(x + c)$ no seguinte:

$f(x+c) = \sum\limits_{i=0}^{N-1} a_i (x+c)^i = \sum\limits_{i=0}^{N-1} a_i  \Big( \sum\limits_ { j=0}^{i} \binom{i}{j} x^j c^{i-j} \Big) = \sum\limits_{i=0}^{N-1} \sum\limits_ { j=0}^{i} a_i \binom{i}{j} x^j c^{i-j}=$

$ = \sum\limits_{i=0}^{N-1}\sum\limits_{j=0}^{i}a_i \dfrac{i! x^j c^{i-j}}{(i-j)!j!} = $

$ = \sum\limits_{j=0}^{N-1}\sum\limits_{i=j}^{N-1}a_i \dfrac{i! x^j c^{i-j}}{(i-j)!j!} = $ (Justificativa ilustrada nas Figuras 1 e 2).

$ = \sum\limits_{j=0}^{N-1} \dfrac{x^j}{j!} \sum\limits_{i=j}^{N-1}a_i \dfrac{i! c^{i - j}}{(i-j)!}$

Este somatório interno pode ser resolvido usando FFT, a explicação segue:

Queremos encontrar $C_j = \sum\limits_{i=j}^{N-1}a_i \dfrac{i! c^{i - j}}{(i-j)!}$ para  $0 \le j \le N-1$.

Para isso seja $A_i = a_i \cdot i!$, $B_i = \dfrac{c^i}{i!}$

Podemos reescrever $C_j$ como:

$ C_j = \sum\limits_{i=j}^{N-1}a_i \dfrac{i! c^{i - j}}{(i-j)!} = \sum\limits_{i=j}^{N-1} A_i B_{i-j} = \sum\limits_{i=j}^{N-1} \overline{A}‎_{N-i-1} B_{i-j} = (\overline{A} * B)‎_{N - j - 1} $

$\overline{A}$ denota o vetor $A$ revertido (de trás para frente).

Desse modo, podemos computar o vetor $C$ através de uma convolução entre o vetor $\overline{A}$ e $B$ em $O(N \log N)$ usando FFT e depois usar o vetor $C$ para encontrar a sequência $b$, já que $b_j = \frac{C_j}{j!} $​​​.

|                         Figura 1                          |                         Figura 2                          |
| :-------------------------------------------------------: | :-------------------------------------------------------: |
| ![figura2](../figuras/taylor1.jpg) | ![figura1](../figuras/taylor2.jpg) |



### Código

Abaixo a implementação do algoritmo. Este código faz uso da implementação de [**convolução da Biblioteca do Atcoder**](https://github.com/atcoder/ac-library/tree/master).

```cpp
#include <bits/stdc++.h>
#include <atcoder/all>

#define int long long
#define all(x) x.begin(), x.end()

using namespace std; using namespace atcoder;

using mint = modint998244353;

vector<mint> taylor_shift(vector<mint>& a, int c) {
  int n = a.size();
  vector<mint> fat(n);
  fat[0] = 1;
  for (int i = 1; i < n; i++) {
    fat[i] = fat[i - 1] * i;
  }
  vector<mint> A(n), B(n);
  for (int i = 0; i < n; i++) {
    A[i] = a[i] * fat[i];
    B[i] = mint(c).pow(i) / fat[i];
  }
  reverse(all(A));
  auto retval = convolution(A, B);
  vector<mint> b(n);
  for (int i = 0; i < n; i++) {
    b[i] = retval[n - i - 1] / fat[i];
  }
  return b;
}

signed main() {
  cin.tie(0)->sync_with_stdio(0);

  int n, c;
  cin >> n >> c;
  vector<mint> a(n);
  for (int i = 0; i < n; i++) {
    int x;
    cin >> x;
    a[i] = x;
  }
  for (auto& x : taylor_shift(a, c)) {
    cout << x.val() << " ";
  }
  cout << "\n";
}
```

# Exemplo de aplicação

## [2023-2024 ICPC Brazil Subregional Programming Contest K - K for More, K for Less](https://codeforces.com/gym/104555/problem/K)

### Problema

Dados dois polinômios $t(x)$ e $p(x)$, o problema é encontrar o polinômio $q(x)$ tal que $q(x) = t(x+K) + p(x-K)$ .

### Explicação

Este problema pode ser resolvido diretamente aplicando deslocamento de Taylor para encontrar $t$ e $p$.

### Código

O código excluindo a função `taylor_shift` é o seguinte.

```cpp
using mint = modint998244353;

signed main(){
    cin.tie(0)->sync_with_stdio(0);

    int n, k;
    cin >> n >> k;
    vector<mint> t(n + 1), p(n + 1);
    for (int i=0; i < n + 1; i++){
        int x;
        cin >> x, t[i] = x;
    }
    for (int i=0; i < n + 1; i++){
        int x;
        cin >> x, p[i] = x;
    }
    f = taylor_shift(t, k);
    p = taylor_shift(p, -k);
    for (int i=0; i < n + 1; i++){
        cout << (f[i] + p[i]).val() << " ";
    }
    cout << "\n";
}
```



# Referências

- [Taylor Shift, Sampling Points Shift](https://infossm.github.io/blog/2023/11/26/taylor-shift/)
