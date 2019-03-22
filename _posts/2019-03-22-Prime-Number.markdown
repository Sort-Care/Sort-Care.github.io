---
layout: single
title: Number Theory
date: 2019-03-22 10:47 +0800
mathjax: true

excerpt: "Prime Numbers, Factorial"

header:
  teaser: /assets/images/circle.png
  overlay_image: /assets/images/circle.png
  overlay_filter: 0.4
---
{% include toc title="Contents" %}

# Generating Prime Numbers
The following program generats prime numbers under a upperbound.
The key structure maintained is the `bitset<10000010> bs`, where
`bs[i] == 1` means number $i$ is a prime number.
The algorithm's idea is to do an up-going scan and cross out all multiples of
a prime number that are smaller than the upperbound.

Also a good enough algorithm for determining if a number is prime is also listed. For small prime numbers, with the help of the bitset we maintained, we can decide the answer in constant time by checking the corresponding bit in `bs`.

```cpp
/**
   prime.cpp
   Purpose: Generating prime numbers and more.

   @author hji
   @version 1.0, 03/22/2019
*/

#include <bitset>
#include <vector>
#include <iostream>

long long _sieve_size;
std::bitset<10000010> bs;
std::vector<int> primes;

void sieve(long long upperbound){
  _sieve_size = upperbound+1;
  bs.set();
  bs[0] = bs[1] = 0;
  for (long long i = 2; i <= _sieve_size; ++i){
    if (bs[i]){
      // cross out multiples of i starting from i*i
      for (long long j = i * i; j <= _sieve_size; j+= i) bs[j] = 0;
      primes.push_back((int)i); // add this prime to the list
    }
  }
}

bool isPrime(long long N){
  if (N <= _sieve_size) return bs[N];
  for (int i = 0; i < (int)primes.size(); i++){
    if (N % primes[i] == 0) return false;
  }
  return true;
}

int main(int argc, char *argv[]){

  sieve(10000000);
  // print out the first 52 prime numbers
  for (int i = 0; i < 52; i++){
    std::cout << primes[i] << std::endl;
  }

  // testing is prime number:
  std::cout << isPrime(2147483647) << std::endl;
  std::cout << isPrime(136117223861LL) << std::endl;

  return 0;
}
```

# Refresher of GCD and LCM
Greatest common divisor and least common multiple are two commonly seen problems that has one-line solution.

```cpp
int gcd(int a, int b) { return b == 0 ? a : gcd(b, a%b); }
int lcm(int a, int b) { return a * (b / gcd(a, b)); }
```

# Factorial

In the following code, the main idea is Divide and Conquer but with an iterative approach(using a while loop). An integer $N$ can be expressed as:

$$
N = PF \times N^{\prime}
$$

where $PF$ is a prime factor and $N^{\prime}$ is another number which is $N/PF$. Thus the whole algorithm is reducing the size of N by taking out its prime factor PF. Keep doing it until eventually $N^{\prime}$ is 1. And to speed up the process, we stop when $PF$ is greater than $\sqrt{N}$.

```cpp
std::vector<int> primeFactors(long long N){
  std::vector<int> factors;
  long long PF_idx = 0, PF = primes[PF_idx]; // using primes produced by sieve
  while (PF * PF <= N){
    while (N % PF == 0){ N /= PF; factors.push_back(PF); } // remove PF
    PF = primes[++PF_idx];
  }
  if (N != 1) factors.push_back(N); // special case if N is prime itself
  return factors;
}

// following is inside main(), assuming sieve(10000000) has been called before
// factorials
  std::vector<int> r = primeFactors(2147483647);
  std::cout << "2147483647 = " ;
  for (int i : r){
    std::cout << i << " x ";
  }
  std::cout << std::endl;

  r = primeFactors(136117223861LL); // slow (104729 * 1299709)
  std::cout << "136117223861LL = ";
  for (int i : r){
    std::cout << i << " x ";
  }
  std::cout << std::endl;

  r = primeFactors(142391208960LL); // faster
  std::cout << "142391208960LL = ";
  for (int i : r){
    std::cout << i << " x ";
  }
  std::cout << std::endl;

```
