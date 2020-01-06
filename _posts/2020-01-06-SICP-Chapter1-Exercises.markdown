---
layout: single
title:  "SICP Chapter 1 Exercises"
date:   2020-01-06 09:08:55 +0800
#categories: iOS
mathjax: true

excerpt: "Structures and Interpretation of Computer Programs Exercises: Chapter 1"
header:
  teaser: /assets/images/codeback.jpg
  overlay_image: /assets/images/codeback.jpg
  overlay_filter: 0.4 # same as adding an opacity of 0.5 to a black background
  
---

{% include toc title="Contents" %}

# Exercises of Chapter 1<a id="sec-1"></a>

## 1.1<a id="sec-1-1"></a>

Omit.

## 1.2<a id="sec-1-2"></a>

```scheme
(/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5)))))
   (* 3 (- 6 2) (- 2 7)))
```

## 1.3<a id="sec-1-3"></a>

Define a procedure that take three numbers as arguments and returns the sum of the squares of the two larger numbers.

```scheme
(define (square-sum-large-two a b c)
  (define (square-sum x y)
    (+ (* x x) (* y y)))
  (cond ((and (< c a) (< c b)) (square-sum a b))
        ((and (< a b) (< a c)) (square-sum b c))
        ((and (< b c) (< b a)) (square-sum a c))
        ((= a b) (square-sum b c))
        ((= a c) (square-sum a b))
        ((= b c) (square-sum a b))
        ))

(square-sum-large-two 3 3 3)
```

## 1.4<a id="sec-1-4"></a>

Observe that our model of evaluation allows for combinations whose operators are compound expressions.

```scheme
(define (a-plus-abs-b a b)
  ((if (> b 0) + -) a b))
```

## 1.5<a id="sec-1-5"></a>

Determine whether interpreter is using applicative-order evaluation or normal-order evaluation. There are two procedures defined as follows:

```scheme
(define (p) (p))
(define (test x y)
  (if (= x 0) 0 y))
;; then evaluate the expression
(test 0 (p))
```

What behavior will be observed with an interpreter that uses applicative-order evaluation? What behavior will be observed with an interpreter that uses normal-order evaluation? (Assume that the evaluation rule for the special form `if` is the same for both cases: the predicate expression is evaluated first, and the result determines whether to evaluate the consequent or the alternative expression).

The normal-order will fully expand and notice the predicate is true and return 0; However, the applicative-order will try to evaluate `(p)` first and apply that, but it is a recursive call to `(p)` itself, so, following this rule, it ends up with an infinite loop.

## 1.6<a id="sec-1-6"></a>

Why `if` has to be a special form?

```scheme
(define (new-if predicate then-clause else-clause)
  (cond (predicate then-clause)
        (else else-clause)))
```

Delighted, Alyssa uses `new-if` to rewrite the square-root program:

```scheme
(define (sqrt-iter guess x)
  (new-if (good-enough? guess x)
          guess
          (sqrt-iter (improve guess x)
                     x)))
```

What happens when Alyssa attempts to use this to compute square roots? Explain.

Because the `new-if` is not a special form, so the subexpressions need to be evaluated before we can apply the `new-if` to the operands. However, the third formal parameter is a recursive call to `sqrt-iter`, which will lead to infinite evaluation loop for sub-expressions. A dead loop.

## 1.7<a id="sec-1-7"></a>

The `good-enough?` test used in computing square roots will not be very effective for finding the square roots of very small numbers. Also, in real computers, arithmetic operations are almost always performed with limited precision. This makes our test inadequate for very large numbers. Explain these statements, with examples showing how the test fails for small and large numbers.

An alternative strategy for implementing `good-enough?` is to watch how guess changes from one iteration to the next and to stop when the change is a very small fraction of the guess.

Design a square-root procedure that uses this kind of end test. Does this work better for small and large

A: For very small numbers: assume \\(t\\) is the tolerance we set for the `good-enough?` procedure, and \\(\varepsilon\\) is a very small number that is smaller than the tolerance we set. When the guess approaches to a point where \\(guess^2 < (t + \varepsilon)\\), than the absolute difference between \\(guess^2\\) and \\(\varepsilon\\) is always in the tolerance range but the \\(guess\\) right now is not necessary the right answer. Example (small number): Let \\(\varepsilon = 0.0000001\\), start \\(guess\\) from 1.0.

For very large numbers, what will happen if the arithmetic operations are performed with limited precision? For large numbers, when the precision digits runs out, we might get stuck at a number which is not the answer but we are not able to proceed because the `good-enough?` procedure is unable to tell the difference because of precision overflow. Then the procedure will continue forever without convergence.

### How floating-point numbers work<a id="sec-1-7-1"></a>

The idea is to compose a number of two main parts:

-   A **significand** that contains the number's digits. Negative significands represent negative numbers.
-   An **exponent** that says where the decimal (or binary) point is placed relative to the beginning of the significand. Negative exponents represent numbers that are very small (i.e. close to zero).

Such a format satisfies all the requirements:

-   It can represent numbers at wildly different magnitudes (limited by the length of the exponent)
-   It provides the same relative accuracy at all magnitudes (limited by the length of the significand)
-   It allows calculations across magnitudes: multiplying a very large and a very small number preserves the accuracy of both in the result.

Decimal floating-point numbers usually take the form of a scientific notation with an explicit point always between the 1st and 2nd digits.

Implementing it another way:

```scheme
  (define (sqrt x)
    (define tolerance 0.01) ;; when changed ratio is less than 1%
    (define (average a b) (/ (+ a b) 2))
    (define (good-enough? old-guess new-guess)
      (< (/ (abs (- old-guess new-guess)) ;; if the changed ration is smaller than tolerance
            old-guess)
         tolerance))
    (define (improve guess) ;; improve the guess
      (average (/ x guess) guess))
    (define (sqrt-iter guess) ;; iteration
      (if (good-enough? guess (improve guess)) ;; if further improving only changes a small ratio
          guess
          (sqrt-iter (improve guess))))
    (sqrt-iter 1.0))
(sqrt 3)
```

## 1.8<a id="sec-1-8"></a>

Replace the approximation of the square root procedure with: \[ \frac{x/y^2 + 2y}{3} \]

```scheme
  (define (cubert x)
    (define tolerance 0.0001)
    (define good-enough?
      (lambda (guess)
        (< (abs (- (* guess guess guess)
                   x))
           tolerance)))
    (define improve
      (lambda (y)
        (/ (+ (/ x (* y y))
              (* 2 y))
           3)))
    (define cbrt-iter
      (lambda (guess)
        (if (good-enough? guess)
            guess
            (cbrt-iter (improve guess)))))
    (cbrt-iter 1.0))

(cubert 23)
```

## 1.9<a id="sec-1-9"></a>

Considering the following two procedure of adding numbers:

```scheme
;; first procedure, recursive process
(define (+ a b)
  (if (= a 0)
      b
      (inc (+ (dec a) b))))

;; second procedure, iterative process
(define (+ a b)
  (if (= a 0)
      b
      (+ (dec a) (inc b))))
```

illustrate the process generated by each procedure in evaluating `(+ 4 5)`

```scheme
;; first one
(+ 4 5)
(inc (+ 3 5))
(inc (inc (+ 2 5)
(inc (inc (inc (+ 1 5))))
(inc (inc (inc (inc (+ 0 5)))))
(inc (inc (inc (inc 5))))
(inc (inc (inc 6)))
(inc (inc 7))
(inc 8)
9

;; second one
(+ 4 5)
(+ (dec 4) (inc 5))
(+ 3 6)
(+ (dec 3) (inc 6))
(+ 2 7)
(+ (dec 2) (inc 7))
(+ 1 8)
(+ (dec 1) (inc 8))
(+ 0 9)
9
```

As we can see, the first procedure creates a recursive shape. While the second one creates a iterative shape.

## 1.10<a id="sec-1-10"></a>

The following procedure computes a mathematical function called Armani's function:

```scheme
  (define (A x y)
    (cond ((= y 0) 0)
          ((= x 0) (* 2 y))
          ((= y 1) 2)
          (else (A (- x 1)
                   (A x (- y 1))))))
(A 2 4)
```

What are the values of the following expressions?

```scheme
(A 1 10)
(A 0 (A 1 9))
(A 0 (A 0 (A 1 8)))
(A 0 (A 0 (A 0 (A 1 7))))
;; ... 2 * 2 * 2 * ...
;; 2^10

(A 2 4)
(A 1 (A 2 3))
(A 1 (A 1 (A 2 2)))
(A 1 (A 1 (A 1 (A 2 1))))
(A 1 (A 1 (A 1 2)))
(A 1 (A 1 (A 0 (A 1 1))))
(A 1 (A 1 (A 0 2)))
(A 1 (A 1 4))
(A 1 (A 0 (A 1 3)))
(A 1 (A 0 8))
(A 1 16)
;; 2^16 = 2^{2^{4}}

(A 3 3)
;; 2^{2^{2^3}}
```

Consider the following procedures, where A is the procedure defined above:

```scheme
(define (f n) (A 0 n)) ;; 2n

(define (g n) (A 1 n)) ;; 2^n

(define (h n) (A 2 n)) ;; 2^{2^n}

(define (k n) (* 5 n n))
```

Give concise mathematical definitions for the functions computed by the procedures `f`, `g`, and `h` for positive integer values of \\(n\\). For example, `(k, n)` computes \\(5n^2\\).

## 1.11<a id="sec-1-11"></a>

A function \\(f\\) is defined as follows:

$$ 
f(n) = 

\begin{cases} 
n &\text{if } n < 3\\ 
f(n-1) + 2f(n-2) + 3f(n-3) &\text{if } n \geq 3 
\end{cases} 
$$

Write a procedure that computes \\(f\\) by means of an iterative process.

```scheme
  (define (f n)
    (cond ((< n 3) n)
          (else (f-iter 1 2 3 (- n 3)))))

  (define (f-iter 1st 2ec 3rd count)
    (cond ((= count 0) 3rd)
          (else (f-iter 2ec
                        3rd
                        (+ (* 3 1st)
                           (* 2 2ec)
                           3rd)
                        (- count 1)))))

(f 7)
;; 1 2 3 10 (10+6+6)=22 
```

## 1.12<a id="sec-1-12"></a>

The following pattern of numbers is Pascal's triangle.

$$
\begin{array}{ccccccccccc}
  &   &   &   &   & 1 &   &   &   &   &   \cr
  &   &   &   & 1 &   & 1 &   &   &   &   \cr
  &   &   & 1 &   & 2 &   & 1 &   &   &   \cr
  &   & 1 &   & 3 &   & 3 &   & 1 &   &   \cr
  & 1 &   & 4 &   & 6 &   & 4 &   & 1 &   \cr
1 &   & 5 &   & 10&   & 10&   & 5 &   & 1 \cr
\end{array}
$$

Write a procedure that computes elements of Pascal's triangle by means of a recursive process.

The goal is to write a recursive procedure `(pas-tri r c)` that gives the number located at the \\(r\\) row and \\(c\\) column in the Pascal's triangle.

```scheme
  ;; notice that there are special cases
  ;; 1. at the edge
  ;; 2. at the top
  (define (pas-tri r c)
    (cond ((or (= r c)
               (= c 0))
           1)
          (else (+ (pas-tri (- r 1) ;; the line above
                            (- c 1));; the column to the left
                   (pas-tri (- r 1) ;; the line above
                            c)))))  ;; the same column
(pas-tri 6 4)
```

## 1.13<a id="sec-1-13"></a>

Prove that \\(Fib(n)\\) is the closest integer to \\(\phi^n / \sqrt{5}\\), where \\(\phi = (1+\sqrt{5}) / 2\\) Hint: Let \\(\psi = (1 - \sqrt{5}) / 2\\). Use induction and the definition of the Fibonacci numbers to prove that \\(Fib(n) = (\phi^n - \psi^n) / \sqrt{5}\\)

1.  Prove that \\(Fib(n) = (\phi^n - \psi^n) / \sqrt{5}\\) by verifying for \\(n = 0, 1\\), and use the \\(Fib(n) = Fib(n-1) + Fib(n-2)\\) equation to further prove it. Notice that \\(\phi^2 = \frac{3 + \sqrt{5}}{2}\\) and \\(\psi^2 = \frac{3 - \sqrt{5}}{2}\\).
2.  How to prove that \\(Fib(n)\\) is the **closest** integer then? First it is an integer. And the difference between \\(Fib(n)\\) and \\(\frac{\phi^n}{\sqrt{5}}\\) is \\(\psi^n / \sqrt{5}\\)

\[ \frac{\psi^n}{\sqrt{5}} = \frac{(1-\sqrt{5})^n}{2^n\cdot\sqrt{5}} \]

Next step is to prove the equation is actually smaller than \\(1/2\\). This can be proven by two observations:

1.  \\(\psi\\) itself is smaller than \\(1/2\\). So \\(\psi^n\\) must be much smaller than \\(1/2\\).
2.  \\(\sqrt{5}\\) is bigger than 2

So dividing something that is clearly smaller than 1/2 by something that is bigger than 2 is going to produce a number that is less than 0.5.

That means the target \\(\phi^n / \sqrt{5}\\) is apart from the integer \\(Fib(n)\\) less than 0.5, making \\(Fib(n)\\) is the closest integer.

## 1.14<a id="sec-1-14"></a>

Draw the tree illustrating the process generated by the `count-change` procedure of section 1.2.2 in making change for 11 cents. What are the orders of growth of the space and number of steps used by this process as the amount to be changed increases?

```scheme
(count-change 11)
(cc 11 5)
(+ (cc 11 4)
   (cc -39 5))

(+ (+ (cc 11 3)
      (cc -14 4))     ;; 0
   0)

(+ (+ (cc 11 3)
      0)
   0)

(+ (+ (+ (cc 11 2)
         (cc 1 3)) ;; used a dime(10)
      0)
   0)

(+ (+ (+ (+ (cc 11 1)     ;; change 11 cents merely with 1 cent coins
            (cc 6 2))     ;; used a nickle, 6 cents to go with last two types
         (+ (cc 1 2)      ;; doing the rest 1 cent with two types of coins
            (cc -9 3)))   ;; 0
      0)
   0)

(+ (+ (+ (+ (+ (cc 11 0)
               (cc 10 1)) ;; used one cent
            (+ (cc 6 1)   ;; used a nickle, try no more nickles solution
               (cc 1 2))) ;; used another nickle, 1 cent left to change
         (+ (+ (cc 1 1)   ;; doing the rest 1 cent with only one type of coins
               (cc -4 2)) ;; 0
            0))
      0)
   0)

(+ (+ (+ (+ (+ (cc 11 0)
               (cc 10 1)) ;; used one cent
            (+ (cc 6 1)   ;; used a nickle, try no more nickles solution
               (cc 1 2))) ;; used another nickle, 1 cent left to change
         (+ (+ 1          ;; only one way to do 1 cent with 1 cent coins
               0)         ;; 0
            0))
      0)
   0)

(+ (+ (+ (+ (+ 0
               (+ (cc 10 0)  ;; 0
                  (cc 9 1))) ;; used the second cent
            (+ (+ (cc 6 0)   ;; 0
                  (cc 5 1))  ;; used a nickle, one cent, try more cents
               (+ (cc 1 1)   ;; one cent with one cent coins => 1
                  (cc -4 2)))) ;; used two nickles, trying third, fail
         (+ 1
            0))
      0)
   0)
```

This procedure creates a tree-recursive structure, and the branching factor is 2. The deepest branch goes \\(O(n)\\) steps down, where \\(n\\) is the money to change. So the process takes \\(O(2^n)\\) steps to finish and \\(O(n)\\) space to run.

## 1.15<a id="sec-1-15"></a>

The sine of an angle (specified in radians) can be computed by making use of the approximation \\(\sin{x}\approx x\\) if \\(x\\) is sufficiently small, and the trigonometric identity:

\[ \sin{x} = 3 \sin{\frac{x}{3}} - 4\sin^3{\frac{x}{3}} \]

to reduce the size of the argument of \\(sin\\). (For purposes of this exercise an angle is considered "sufficiently small" if its magnitude is not greater than 0.1 radians.) These ideas are incorporated in the following procedures:

```scheme
(define (cube x) (* x x x))

(define (p x) (- (* 3 x) (* 4 (cube x))))

(define (sine angle)
  (if (not (> (abs angle) 0.1))
      angle
      (p (sine (/ angle 3.0)))))
```

1.  How many times is the procedure `p` applied when `(sine 12.15)` is evaluated? \\(12.5 / 3^x \leq 0.1\\) By solving the equation above, we get \\(x\geq5\\), thus `p` is applied 5 times.

2.  What is the order of growth in space and number of steps (as a function of \\(a\\)) used by the process generated by the \\(sine\\) procedure when `(sine a)` is evaluated? The order of growth in number of steps is \\(O(\log_3a)\\). The order of growth in space is \\(O(1)\\).

## 1.16<a id="sec-1-16"></a>

Design a procedure that evolves an **iterative** exponentiation process that uses successive squaring and uses a logarithmic number of steps, as does `fast-expt`. Hint: use the observation that \\((b^{n/2})^2 = (b^2)^{n/2}\\), keep, along with the exponent \\(n\\) and the base \\(b\\), an additional state variable \\(a\\), and define the state transformation in such a way that the product \\(ab^n\\) is unchanged from state to state. At the beginning of the process \\(a\\) is taken to be 1, and the answer is given by the value of \\(a\\) at the end of the process. In general, the technique of defining an **invariant quantity** that remains unchanged from state to state is a powerful way to think about the design of iterative algorithms.

IDEA: Iteratively, take the exponent inside and replace old base with new ones, until outer exponent is 1. When the outer exponent left is even, further the process by making \\(a\Leftarrow a^2\\) and thus shrink the exponent by dividing it by 2. When the outer exponent is odd, take 1 away and make \\(a\Leftarrow ab\\), shrink the exponent by 1.

The above idea is terribly wrong! Because the case for dealing with odd exponent is problematic. The "1" taken out is not on the base of \\(b\\), but rather on the base of what is inside right now. Can we put another variable that hold what's outside?

```scheme
  (define (expt b n) ;; expect b and n both to be integers.
    (expt-iter b n b 1))

  ;; outer is for keep track of what is outside.
  (define (expt-iter b cnt a outer)
    (cond ((= cnt 0) 1)
          ((= cnt 1) (* a outer))
          ((even? cnt) (expt-iter b
                                  (/ cnt 2)  ;; divide the outside expo by 2
                                  (square a) ;; double the inside
                                  outer))    ;; outside additional remains the same
          (else (expt-iter b
                           (- cnt 1)         ;; minus 1 to make it even
                           a                 ;; inside remains the same
                           (* outer a)))))   ;; outer additional get multiplied by the base

  (define (even? x)
    (= (remainder x 2) 0))

  (define (square x) (* x x))

(expt 2 19)
```

This solution I have here does not follow the hint given in the book. I haven't figured out how to compute it with an *invariant quantity*.

## 1.17<a id="sec-1-17"></a>

The exponentiation algorithms in this section are based on performing exponentiation by means of repeated multiplication. In a similar way, one can perform integer multiplication by means of repeated addition. The following multiplication procedure (in which it is assumed that our language can only add, not multiply) is analogous to the `expt` procedure:

```scheme
(define (* a b)
  (if (= b 0)
      0
      (+ a (* a (- b 1)))))
```

This algorithm takes a number of steps that is linear in \\(b\\). Now suppose we include, together with addition, operations `double`, which doubles the integer, and `halve`, which divides an (even) integer by 2. Using these, design a multiplication procedure analogous to `fast-expt` that uses a logarithmic number of steps.

```scheme
(define (double x) (+ x x))
(define (halve x) (/ x 2))
(define (even? x)
  (= (remainder x 2)
     0))

(define (fast-mul a b)
  (cond ((= b 0) 0)
        ((even? b) (fast-mul (double a)
                             (halve b)))
        (else (+ a
                 (fast-mul a
                           (- b 1))))))
```

## 1.18<a id="sec-1-18"></a>

Using the results of exercises 1.16 and 1.17, devise a procedure that generates an iterative process for multiplying two integers in terms of adding, doubling, and halving and uses a logarithmic number of steps.

```scheme
  (define (double x) (+ x x))
  (define (halve x) (/ x 2))
  (define (even? x) (= (remainder x 2) 0))

  (define (fast-mul a b)
    (mul-iter a b 0))

  (define (mul-iter a b add)
    (cond ((= b 0) 0)
          ((= b 1) (+ a add))
          ((even? b) (mul-iter (double a)
                               (halve b) ;; logarithmic
                               add))
          (else (mul-iter a
                          (- b 1)
                          (+ add a)))))  ;; carry the information along, makes it an iterative process
(fast-mul 23 59)
```

## 1.19<a id="sec-1-19"></a>

There is a clever algorithm for computing the Fibonacci numbers in logarithmic numbers of steps. Recall the transformation of the state variables \\(a\\) and \\(b\\) in the `fib-iter` process of section 1.2.2:

$$
\begin{align*}
a &\leftarrow a + b\\
b &\leftarrow a
\end{align*}
$$

Call this transformation \\(T\\), and observe that applying \\(T\\) over and over again \\(n\\) times, starting with 1 and 0, produces the pair \\(Fib(n+1)\\) and \\(Fib(n)\\). In other words, the Fibonacci numbers are produced by \\(T^n\\), the $n$th power or the transformation \\(T\\), starting with the pair \\((1,0)\\).

Now consider the \\(T\\) to be the special case of \\(p = 0\\) and \\(q = 1\\) in a family of transformations \\(T_{pq}\\), where \\(T_{pq}\\) transforms the pair \\((a,b)\\) according to:

$$
\begin{align*}
a &\leftarrow bq + aq + ap \\
b &\leftarrow bp + aq
\end{align*}
$$

Show that if we apply such transformation \\(T_{pq}\\) twice, the effect is the same as using a single transformation \\(T_{p^\prime q^\prime}\\) of the same form, and compute \\(p^\prime\\) and \\(q^\prime\\) in terms of \\(p\\) and \\(q\\).

This gives us an explicit way to square these transformations, and thus we can compute \\(T^n\\) using successive squaring, as in the `fast-expt` procedure. Put this all together to complete the following procedure, which runs in a logarithmic number of steps:

```scheme
  (define (fib n)
    (fib-iter 1 0 0 1 n))

  (define (fib-iter a b p q count)
    (cond ((= count 0) b)
          ((even? count)
           (fib-iter a
                     b
                     (+ (square p) (square q))
                     (+ (square q) (* 2 p q))
                     (/ count 2)))
          (else (fib-iter (+ (* b q) (* a q) (* a p))
                          (+ (* b p) (* a q))
                          p
                          q
                          (- count 1)))))

  (define (even? x) (= (remainder x 2) 0))
  (define (square x) (* x x))

(fib 13)
```

## 1.20<a id="sec-1-20"></a>

The process that a procedure generates is of course independent on the rules used by the interpreter. As an example, consider the iterative `gcd` procedure given in text book. Suppose we were to interpret this procedure using normal-order evaluation, as discussed in section 1.1.5. (The normal-order-evaluation rule for `if` is described in exercise 1.5.) Using the substitution method (for normal order), illustrate the process generated in evaluating `(gcd 206 40)` and indicate the `remainder` operations that are actually performed. How many `remainder` operations are actually performed in the normal-order evaluation of `(gcd 206 40)`? In the applicative-order evaluation?

Review that applicative-order will evaluate the operands whenever possible, and then apply the operator to the operands. However, normal order will delay any evaluation to a point where something has to be evaluated to proceed. In this exercise, the normal order will only evaluate expressions either when: 1) fully expanded, 2) `if` special form's predicate needs to be evaluated to decide which clause (the consequent or the alternative) to expand next.

For applicative-order, `remainder` gets evaluated 4 times as it shows here:

```scheme
(gcd 206 40)
;;   |
;;   v
(gcd 40 (remainder 206 40)) ;; 1 (remainder eval counter)
(gcd 40 6)
;;   |
;;   v
(gcd 6 (remainder 40 6))    ;; 2
(gcd 6 4)
;;   |
;;   v
(gcd 4 (remainder 6 4))     ;; 3
(gcd 4 2)
;;   |
;;   v
(gcd 2 (remainder 4 2))     ;; 4
(gcd 2 0)                   ;; predicate is true, stop and return 2
```

For normal-order evaluation:

```scheme
(gcd 206 40)
(if (= 40 0) ...)
(gcd 40 (remainder 206 40))

(if (= (remainder 206 40) 0) ...)                               ;; 1
;; 6, not 0
(gcd (remainder 206 40)
     (remainder 40
                (remainder 206 40)))

(if (= (remainder 40                                            ;; 1 +2
                  (remainder 206 40))
       0) ...)
;;40%6 = 4, not 0

(gcd (remainder 40
                (remainder 206 40))               ;; a
     (remainder (remainder 206 40)
                (remainder 40
                           (remainder 206 40))))  ;; b

(if (= (remainder (remainder 206 40)                             ;; 1 +2 +4
                  (remainder 40
                             (remainder 206 40))) ;; b == 0?
       0) ...)
;; 2, not zero

(gcd (remainder (remainder 206 40)
                (remainder 40
                           (remainder 206 40)))   ;; a
     (remainder (remainder 40
                           (remainder 206 40))
                (remainder (remainder 206 40)
                           (remainder 40
                                      (remainder 206 40))))) ;; b
(if (= (remainder (remainder 40                                   ;; 1 +2 +4 +7 = 14
                             (remainder 206 40))
                  (remainder (remainder 206 40)
                             (remainder 40
                                        (remainder 206 40)))) ;; 4%2 = 0
       0) ...)

(remainder (remainder 206 40)
           (remainder 40
                      (remainder 206 40))) ;; fully expanded, evaluate all rest expressions
;; finally, 14 + 4 = 18
;; So the remainder get evaluated 18 times in total.
;; FORMULA?
```

Notice that, the changing of numbers of `remainder` appearing in the two operands of `gcd` obeys the following pattern:

$$
\begin{align*}
(R_a,R_b) &\rightarrow \text{Occurance of remainder procedure}\\
(0, 0)     &\rightarrow  \text{None}   \\
(0, 1)     &\rightarrow 1+(0+0)=1          \\
(1, 2)     &\rightarrow 1+(0+1)=2          \\
(2, 4)     &\rightarrow 1+(1+2)=4          \\
(4, 7)     &\rightarrow 1+(2+4)=7          \\
4          &\rightarrow \text{predicate true, evaluate }a
\end{align*}
$$

Assume our iteration starts from 0. For simplicity, we consider when applying `gcd`, the two operands \\(a\\) and \\(b\\) are generated by last iteration in the alternate clause. The 1 that appears in each line is the new `remainder` that kicks in in the alternative clause. And the addition between the parentheses means using the operand \\(a\\) and \\(b\\) from previous iteration are used as new operands for the new `remainder` procedure.

First off, we mainly evaluate `remainder` when in the `if` predicate. Each time, when we forward one iteration, the number of `remainder` in \\(b\\) from last iteration is evaluated, to determine which clause to expand next. That is: \\(0+1+2+4+7\\) in this example. Generally, the predicate evaluation will not stop until in the final iteration, where \\(b\\) equals to 0. When that happens, all the things in \\(a\\) got evaluated.

To formulate it, lets assume \\((R_a^{\langle i \rangle}, R_b^{\langle i \rangle})\\) is a pair of integers that record how many times `remainder` is invoked in \\(a\\) and \\(b\\) during \\(i\\) th iteration, starting from 0. Say \\(k\\) is the final iteration where \\(b^{\langle k-1 \rangle}=0\\). Then the total times that `remainder` gets evaluated is:

\[ T(remainder) = R_b^{\langle 0 \rangle} + R_b^{\langle 1 \rangle} + R_b^{\langle 2 \rangle} + \cdots + R_b^{\langle k-1 \rangle} + R_a^{\langle k-1 \rangle} \]

Notice that, \[ R_b^{\langle i \rangle} = R_a^{\langle i+1 \rangle} = \sum_{j = 0}^i Fib(j) \]

Thus the formula can be rewritten into:

\[ T(remainder) = \sum_{j = 0}^0 Fib(j) + \sum_{j = 0}^1 Fib(j) + \sum_{j = 0}^2 Fib(j) + \cdots + 2\sum_{j = 0}^{k-2} Fib(j) + \sum_{j = 0}^{k-1} Fib(j) \]

## 1.21<a id="sec-1-21"></a>

Use the `smallest-divisor` procedure to find the smallest divisor of each of the following numbers: 199, 1999, 199999.

Recall that the procedure ~~ is:

```scheme
(define (smallest-divisor n)
  (find-divisor n 2)) ;; find the first divisor starting from 2

(define (find-divisor n test-divisor)
  (cond ((> (square test-divisor) n) n)   ;; end test
        ((divides? test-divisor n) test-divisor)   ;; modulo test
        (else (find-divisor n (+ test-divisor 1)))))

(define (divides? a b)
  "b%a == 0?"
  (= (remainder b a)
     0))
```

Running this procedure yields:

| Number | smallest-divisor | primality |
|------ |---------------- |--------- |
| 199    | 199              | Y         |
| 1999   | 1999             | Y         |
| 19999  | 7                | N         |

## 1.22<a id="sec-1-22"></a>

Most Lisp implementations include a primitive called `runtime` that returns an integer that specifies the amount of time the system has been running (measured, for example, in microseconds). The following `timed-prime-test` procedure, when called with an integer \\(n\\), prints \\(n\\) and checks to see if \\(n\\) is prime. If it is, the procedure prints three asterisks followed by the amount of time used in performing the test.

```scheme
(define (timed-prime-test n)
  (newline)
  (display n)
  (start-prime-test n (runtime)))

(define (start-prime-test n start-time)
  (if (prime? n)
      (report-prime (- (runtime) start-time))))

(define (report-prime elapsed-time)
  (display " *** ")
  (display elapsed-time))
```

Using this procedure, write a procedure `search-for-primes` that checks the primality of consecutive odd integers in a specified range. Use your procedure to find the 3 smallest primes:

-   larger than 1000;
-   larger than 10,000;
-   larger than 100,000;
-   larger than 1,000,000.

Note the time needed to test each prime. Since the testing algorithm has order of growth \\(\Theta(\sqrt{n})\\), you should expect that testing or primes around 10,000 should take about \\(\sqrt{10}\\) times as long as testing for primes around 1000. Do your timing data bear this out? How well do the data for 100,000 and 1,000,000 support the \\(\sqrt{n}\\) prediction? Is your result compatible with the notion that programs on your machine run in time proportional to the number of steps required for the computation?

```scheme
(define (even? x) (= (remainder x 2) 0))

(define (search-for-primes start end)
  "checks the primality of consecutive odd integers in a specified range."
  (search-iter start end start))

(define (search-iter start end curr)
  (cond ((> curr end) (display "--END--"))
        ((even? curr) (search-iter start end (+ curr 1)))
        (else (timed-prime-test curr)
              (search-iter start end (+ curr 2)))))
```

| Smallest 3 Larger than | Prime Numbers | Elapsed Time |  |  |
|---------------------- |------------- |------------ |--- |--- |
| >1,000                 | 1009          | 0.           |  |  |
|                        | 1013          | 0.           |  |  |
|                        | 1019          | 0.           |  |  |
| >10,000                | 10007         | 0.           |  |  |
|                        | 10009         | 0.           |  |  |
|                        | 10037         | 0.           |  |  |
| >100,000               | 100003        | 0.           |  |  |
|                        | 100019        | 0.           |  |  |
|                        | 100043        | 0.           |  |  |
| >1,000,000             | 1000003       | 1e-4         |  |  |
|                        | 1000033       | 0.           |  |  |
|                        | 1000037       | 0.           |  |  |
| >1,000,000,000         | 1000000007    | 2e-2         |  |  |
|                        | 1000000009    | 2e-2         |  |  |
|                        | 1000000021    | 1e-2         |  |  |
| >10,000,000,000        | 10000000019   | 0.05         |  |  |
|                        | 10000000033   | 0.07         |  |  |
|                        | 10000000061   | 0.05         |  |  |

My data for required tests doesn't bear out any signs of \\(\Theta(\sqrt{n})\\) order of growth. Maybe I should try larger numbers.

After trying numbers larger than 1 billion and numbers larger than 10 billion, the relationship between them starts to reveal itself. Notice that the average running time ratio is:

\[ \frac{(0.05 + 0.07 + 0.05) / 3}{(0.02 + 0.02 + 0.01) / 3} = \frac{17}{5} = 3.4 \approx \sqrt{10} \]

## 1.23<a id="sec-1-23"></a>

The `smalles-divisor` procedure shown at the start of this section does lots of needles testing: After it checks to see if the number is divisible by 2 there is no point in checking to see if it is divisible by any larger even numbers. This suggests that the values used for `test-divisor` should not be 2,3,4,5,6&#x2026;, but rather 2,3,5,7,9&#x2026; (Not quite here, notice 9 is also a repeated useless check because we would have get the result from testing 3. Actually we should only try prime numbers here. But seems like the exercise is not aimed at that, so we will not consider this fact yet.) To implement this change, define a procedure `next` that returns 3 if its input is 2 and otherwise returns its input plus 2. Modify the `smallest-divisor` procedure to use `(next test-divisor)` instead of `(+ test-divisor 1)`. With `timed-prime-test` incorporating this modified version of `smallest-divisor`, run the test for each of the 12 primes found in exercise 1.22. Since this modification halves the number of test steps, you should expect it to run about twice as fast. Is this expectation confirmed? If not, what is the observed ratio of the speeds of the two algorithms, and how do you explain the fact that it is different from 2?

```scheme
(define (next curr)
  "return the next number as new test-divisor."
  (if (= curr 2)
      3
      (+ curr 2)))

(define (smallest-divisor n)
  "Find the smallest divisor of n that is bigger than 1."
  (find-divisor n 2))

(define (find-divisor n test-divisor)
  "Find divisor of n, starting from test-divisor."
  (cond ((> (square test-divisor) n) n)
        ((divides? test-divisor n) test-divisor)
        (else (find-divisor n
                            (next test-divisor)))))

(define (divides? a b)
  "Can b be divided by a with remainder 0."
  (= (remainder b a)
     0))
```

Because the normal 12 tests required in the textbook doesn't yield big enough elapse time on my machine, I am going to do the billion tests instead here.

| Bigger Than     | Number      | Original | Modified |  |
|--------------- |----------- |-------- |-------- |--- |
| >1,000,000,000  | 1000000007  | 2e-2     | 0.02     |  |
|                 | 1000000009  | 2e-2     | 0.02     |  |
|                 | 1000000021  | 1e-2     | 0.01     |  |
| >10,000,000,000 | 10000000019 | 0.05     | 0.05     |  |
|                 | 10000000033 | 0.07     | 0.04     |  |
|                 | 10000000061 | 0.05     | 0.04     |  |

Notice that for most numbers the result remained the same when it comes to time took. Why? Let's count how many steps we actually do in each `find-divisor` iteration in both cases:

| Step       | Original                  | Modified                |
|---------- |------------------------- |----------------------- |
| Comparison | Y                         | Y                       |
| divides?   | Y                         | Y                       |
| else       | (+ test-divisor 1)        | Equal to 2 test         |
|            | ;; forward next iteration | (most cases) (+ curr 2) |

As we can see here, although we have less iterations because we only test odd numbers now, but to generate the `next` sequence correctly, we embedded a lots of useless comparisons against 2 to handle the special starting case. Thus make the total steps needed are the same. (This answer may not be correct.)

## 1.24<a id="sec-1-24"></a>

Modify the `timed-prime-test` procedure of exercise 1.22 to use `fast-prime?` (the Fermat method), and test each of the 12 primes you found in that exercise. Since the Fermat test has \\(\Theta(\log n)\\) growth, how would you expect the time to test primes near 1,000,000 to compare with the time needed to test primes near 1000? Do your data bear this out? Can you explain any discrepancy you find?

Quick review of the code:

```scheme
(define (expmod base exp m)
  (cond ((= exp 0) 1)
        ((even? exp)
         (remainder (square (expmod base (/ exp 2) m))
                    m))
        (else
         (remainder (* base (expmod base (- exp 1) m))
                    m))))

(define (fermat-test n)
  (define (try-it a)
    (= (expmod a n n) a))
  (try-it (+ 1 (random (- n 1)))))

(define (fast-prime? n times)
  (cond ((= times 0) true)  ;; all tests passed, very likely a prime number
        ((fermat-test n) (fast-prime? n (- times 1))) ;; pass one test, continue
        (else false))) ;; failed one test, certainly not a prime number
```

Then, embedded this into our `timed-prime-test`:

```scheme
(define (timed-prime-test n)
  (newline)
  (display n)
  (start-prime-test n (runtime)))

(define (start-prime-test n start-time)
  (if (fast-prime? n 1000000) ;; replaced the prime test used here!
      (report-prime (- (runtime) start-time))))

(define (report-prime elapsed-time)
  (display " *** ")
  (display elapsed-time))
```

| Bigger Than     | Number      | Time(100) | (1000000) |  |
|--------------- |----------- |--------- |--------- |--- |
| >1,000,000,000  | 1000000007  | 0.01      | 26.23     |  |
|                 | 1000000009  | 0.00      | 25.61     |  |
|                 | 1000000021  | 0.01      | 26.14     |  |
| >10,000,000,000 | 10000000019 | 0.01      | 30.73     |  |
|                 | 10000000033 | 0.01      | 30.05     |  |
|                 | 10000000061 | 0.01      | 31.90     |  |

Right now, the results seems identical, I suppose that is because the number of times we test for each number is too small, replacing 100 with 1000,000.

The ratio: \[ \frac{30.73 + 30.05 + 31.90}{26.23 + 26.61 + 26.14} = 1.1769 \approx \frac{\log 10000000000}{\log 1000000000} = 1.1111 \]

## 1.25<a id="sec-1-25"></a>

Alyssa P. Hacker complains that we went to a lot of extra work in writing `expmod`. After all, she says, since we already know how to compute exponentials, we could have simply written:

```scheme
(define (expmod2 base exp m)
  (remainder (fast-expt base exp) m))
```

Is she correct? Would this procedure serve as well for our fast prime tester? Explain.

They both yield the same results. However, the version of `expmod` is constantly using modulo arithmetic to downsize the numbers related in computation. However, the version proposed by Alyssa, is actually computing the real result for the exponential, which can be slower when the numbers are large enough. It will still give the correct answer but slower.

## 1.26<a id="sec-1-26"></a>

Louis Reasoner is having great difficulty doing exercise 1.24. His `fast-prime?` test seems to run more slowly than his `prime?` test. Louis calls his friend Eva Lu Ator over to help. When they examine Louis's code, they find that he has rewritten the `expmod` procedure to use an explicit multiplication, rather than calling `square`:

```scheme
(define (expmod base exp m)
  (cond ((= exp 0) 1)
        ((even? exp)
         (remainder (* (expmod base (/ exp 2) m)
                       (expmod base (/ exp 2) m))
                    m))
        (else
         (remainder (* base
                       (expmod base (- exp 1) m))
                    m))))
```

"I don't see what difference that could make," says Louis. "I do." says Eva. "By writing the procedure like that, you have transformed the \\(\Theta(\log n)\\) process into a \\(\Theta(n)\\) process." Explain.

The recursive call for the even cases are now have branching factor of 2 instead of 1. So instead of a linear recursion with the depth of \\(O(\log n)\\), now the explicit multiplication version becomes a tree recursion with the depth of \\(O(\log n)\\). Thus the growth becomes \\(\Theta(n)\\)

## 1.27<a id="sec-1-27"></a>

Demonstrate that the Carmichael numbers listed in footnote 47 really do fool the Fermat test. That is, write a procedure that takes an integer \\(n\\) and tests whether \\(a^n\\) is congruent to \\(a\\) modulo \\(n\\) for every \\(a<n\\), and try your procedure on the given Carmichael numbers.

```scheme
(define (congruent-test n)
  "test every number a<n: if a^n is congruent to a modulo n"
  (congruent-iter n 1))

(define (congruent-iter n counter)
  "Counter starts from 1 and ends at n"
  (cond ((= counter n) true) ;; end test
        ;; test counter, if passed, continue
        ((single-fermat-test n counter) (congruent-iter n (+ counter 1)))
        (else false)))

(define (single-fermat-test n a)
  (= (expmod a n n) a))
```

Running it with the 6 Carmichael numbers mentioned:

| Carmichael Number | Result |
|----------------- |------ |
| 561               | t      |
| 1105              | t      |
| 1729              | t      |
| 2465              | t      |
| 2821              | t      |
| 6601              | t      |

We can see that all these numbers can fool Fermat test.

(The following section is not required by the exercise. It is out of my own curiosity.) Next, I want to find the next Carmichael number not mentioned in the book using the above procedure:

```scheme
(define (find-carmichael start end)
  "Find all carmichael numbers between start and end."
  (find-carmichael-iter start end start))

(define (find-carmichael-iter start end test)
  (cond ((= test end) (display "==END=="))
        ((and (not (prime? test)) (congruent-test test))
         (display test)
         (newline)
         (find-carmichael-iter start end (+ test 1)))
        (else (find-carmichael-iter start end (+ test 1)))))
```

It is a procedure that looks for Carmichael numbers within an range \\([start, end)\\).

By running the above procedure, I found the following Carmichael Numbers: 8911, 10585, 15841.

Comparing them with the Carmichael number list online confirms the correctness of the `find-carmichael` procedure.

## 1.28<a id="sec-1-28"></a>

One variant of Fermat test that cannot be fooled is called the Miller-Rabin test. This starts from an alternate form of Fermat's Little Theorem, which states that if \\(n\\) is prime number and \\(a\\) is any positive integer less than \\(n\\), then \\(a^{n-1}\\) is congruent to 1 modulo \\(n\\). To test the primality of a number \\(n\\) by the Miller-Rabin test, we pick a random number \\(a<n\\) and raise \\(a\\) to the \\((n-1)\\) power modulo \\(n\\) using the `expmod` procedure. **However, whenever we perform the squaring step in `expmod`, we check to see if we have discovered a "nontrivial square root of 1 modulo n," that is, a number not equal to 1 or \\(n-1\\) whose square is equal to 1 modulo \\(n\\).** It is possible to prove that if such a nontrivial square root of 1 exists, then \\(n\\) is not prime. It is also possible to prove that if \\(n\\) is an odd number that is not prime, then, for at least half the numbers \\(a<n\\), computing \\(a^{n-1}\\) in this way will reveal a nontrivial square root of 1 modulo \\(n\\). (This is why the Miller-Rabin test cannot be fooled.) Modify the `expmod` procedure to signal if it discovers a nontrivial square root of 1, and use this to implement the Miller-Rabin test with a procedure analogous to `fermat-test`. Check your procedure by testing various known primes and non-primes. Hint: One convenient way to make `expmod` signal it to have it return 0.

```scheme
(define (expmod-m1 base exp m)
  (cond ((= exp 0) 1)
        ((even? exp)
         (remainder (square (test-nontrivial-m1 (expmod-m1 base (/ exp 2) m) m))
                    m))
        (else
         (remainder (* base (expmod-m1 base (- exp 1) m))
                    m))))

(define (test-nontrivial-m1 a n)
  "test if: a not equal to 1 or n-1 but a^2 equals to 1 modulo n"
  (cond ((and (> a 1)
              (< a (- n 1))
              (= (remainder (square a) n) 1)) ;; if it is nontrivial case
         0) ;; return zero
        (else a))) ;; else, return its original value

(define (miller-rabin n)
  "test if n is prime using miller rabin method"
  (define (try-it a)
    (= (expmod-m1 a (- n 1) n) 1))
  (try-it (+ 1 (random (- n 1)))))

(define (fast-prime? n times)
  (cond ((= times 0) true)
        ((miller-rabin n) (fast-prime? n (- times 1)))
        (else false)))
```

I tested this procedure with common numbers and large prime numbers. Next I am going to write a procedure that tests and gives all prime numbers within a range:

```scheme
(define (print-primes start end)
  (print-primes-iter start end start))

(define (print-primes-iter start end curr)
  (cond ((> curr end) (display "---END---"))
        ((fast-prime? curr 20) ;; test 20 random numbers
         (newline)
         (display curr)
         (print-primes-iter start end (+ curr 1)))
        (else (print-primes-iter start end (+ curr 1)))))
```

By printing a lot of prime numbers and comparing them with `prime?` procedure's outputs, we can verify that this procedure does what we intended.

## 1.29<a id="sec-1-29"></a>

Simpson's Rule is a more accurate method of numerical integration than the method illustrated above. Using Simpson's rule, the integral of a function \\(f\\) between \\(a\\) and \\(b\\) is approximated as \[ \frac{h}{3}[y_0 + 4y_1 + 2y_2 + 4y_3 + 2y_4 + \cdots + 2y_{n-2} + 4y_{n-1} + y_n] \] where \\(h=(b-a)/n\\), for some even integer \\(n\\), and \\(y_k=f(a+kh)\\). (Increasing \\(n\\) increases the accuracy of the approximation.) Define a procedure that takes as arguments \\(f\\), \\(a\\), \\(b\\), and \\(n\\) and returns the value of the integral, computed using Simpson's Rule. Use your procedure to integrate `cube` between 0 and 1 (with \\(n=100\\) and \\(n=1000\\), and compare the results to those of the `integral` procedure shown above.

```scheme

(define (simpson-integral f a b n)
  (define h (/ (- b a) n))
  (define (coef x)
    (let ((diff (/ (- x a) h))) ;; compute coefficients based on the x difference.
      (cond ((or (= diff 0) (= diff n)) 1.0)
            ((even? diff) 2.0)
            (else 4.0))))
  (define (simpson-term x) (* (coef x)
                              (f x))) ;; how to deal with the coefficients? 1, 4, 2, 4, 2, ..., 2, 4, 1
  (define (simpson-next x) (+ x h))
  (* (sum simpson-term a simpson-next b)
     (/ h 3.0)))
```

Running this procedure with different \\(n\\) yields:

| \\(n\\) value | Result             |
| 100         | .24999999999999992 |
| 1000        | .2500000000000002  |

We see that it is more accurate than the results in previous section.

## 1.30<a id="sec-1-30"></a>

The `sum` procedure in the textbook generates a linear recursion. The procedure can be rewritten so that the sum is performed iteratively. Show how to do this by filling in the missing expressions in the following definition:

```scheme
(define (sum term a next b)
  (define (iter a result)
    (if (> a b)
        result
        (iter (next a)
              (+ result (term a)))))
  (iter a 0))
```

## 1.31<a id="sec-1-31"></a>

1.  The `sum` procedure is only the simplest of a vast number of similar abstractions that can be captured as

higher-order functions. Write an analogous procedure called `product` that returns the product of the values of a function at points over a given range. Show how to define `factorial` in terms of `product`. Also, use `product` to compute approximations to \\(\pi\\) using the formula: \[ \frac{\pi}{4} = \frac{2\cdot 4\cdot 4 \cdot 6 \cdot 6 \cdot 8 \cdots}{3\cdot 3 \cdot 5 \cdot 5 \cdot 7 \cdot 7 \cdots} \]

```scheme
(define (product term a next b)
  (if (> a b)
      1
      (* (term a)
         (product term (next a) next b))))

(define (pi-product start end)
  (define (pi-term x)
    (/ (* (- x 1.0) (+ x 1.0))
       (square x)))
  (define (pi-next x)
    (+ x 2))
  (product pi-term start pi-next end))

(define (pi-estimate accuracy)
  "the larger the accuracy, the more accurate the estimation is."
  (* 4
     (pi-product 3 (+ 3 (* 2 accuracy)))))
```

Running this procedure yields:

| Accuracy | Estimation |
|-------- |---------- |
| 20       | 3.1774930  |
| 200      | 3.1454832  |
| 2000     | 3.1319849  |

1.  If your `product` procedure generates a recursive process, write one that generates an iterative process. If it generates an iterative process, write one that generates a recursive process.

```scheme
(define (product term a next b)
  (define (product-iter a result)
    (if (> a b)
        result
        (product-iter (next a)
                      (* result
                         (term a)))))
  (product-iter a 1.0))
```

## 1.32<a id="sec-1-32"></a>

1.  Show that `sum` and `product` are both special cases of a still more general notion called `accumulate` that combines a collection of terms, using some general accumulation function: `(accumulate combiner null-value term a next b)` It takes as arguments the same term and range specifications as `sum` and `product`, together with a `combiner` procedure (of two arguments) that specifies how the current term is to be combined with the accumulation of the preceding terms and a `null-value` that specifies what base value to use when the terms run out. Write `accumulate` and show how sum and product can both be defined as simple calls to `accumulate`.
2.  Write another version of `accumulate`, that will generate an iterative or a recursive process.

```scheme
;; recursive process version
(define (accumulate combiner null-value term a next b)
  (if (> a b)
      null-value
      (combiner (term a)
                (accumulate combiner null-value term (next a) next b))))

;; iterative process version
(define (accumulate combiner null-value term a next b)
  (define (accumulate-iter a result)
    (if (> a b)
        result
        (accumulate-iter (next a)
                         (combiner (term a)
                                   result))))
  (accumulate-iter a null-value))

(define (sum term a next b)
  (accumulate + 0.0 term a next b))

(define (product term a next b)
  (accumulate * 1.0 term a next b))
```

## 1.33<a id="sec-1-33"></a>

You can obtain an even more general version of `accumulate` by introducing the notion of `filter` on the terms to be combined. That is, combine only those terms derived from values in the range that satisfy a specified condition. The resulting `filtered-accumulate` abstraction takes the same arguments as accumulate, together with an additional predicate of one argument that specifies the filter. Write `filtered-accumulate` as a procedure. Show how to express the following using it:

1.  the sum of the squares of the prime numbers in the interval \\(a\\) to \\(b\\) (assuming that you have `prime?`) predicate already written.
2.  the product of all the positive integers less than \\(n\\) that are relatively prime to \\(n\\) (i.e., all positive integers \\(i<n\\) such that \\(GCD(i,n) = 1\\)).

```scheme
;; recursive process
(define (filtered-accumulate combiner     ;; 2 args, combine values
                             filter       ;; 1 arg, on terms to be combined
                             null-value
                             term
                             a
                             next
                             b)
  (if (> a b)
      null-value
      (combiner (if (filter a)
                    (term a)
                    null-value);; if it doesn't, replace with null-value
                (filtered-accumulate combiner
                                     filter
                                     null-value
                                     term
                                     (next a)
                                     next
                                     b))))
;; iterative process
(define (filtered-accumulate combiner filter null-value term a next b)
  (define (filtered-accum-iter a result)
    (if (> a b)
        result
        (filtered-accum-iter (next a)
                             (combiner (if (filter a)
                                          (term a)
                                          null-value)
                                      result))))
  (filtered-accum-iter a null-value))
```

1.  

```scheme
  (define (smallest-divisor n)
    "Find the smallest divisor of n that is bigger than 1."
    (find-divisor n 2))

  (define (find-divisor n test-divisor)
    "Find divisor of n, starting from test-divisor."
    (cond ((> (square test-divisor) n) n)
          ((divides? test-divisor n) test-divisor)
          (else (find-divisor n
                              (+ test-divisor 1)))))

  (define (divides? a b)
    "Can b be divided by a with remainder 0."
    (= (remainder b a)
       0))

  (define (prime? n)
    (= (smallest-divisor n)
       n))

  (define (square-sum-primes a b)
    (filtered-accumulate + prime? 0 square a 1+ b))
;; running (square-sum-primes 2 15) yields 377
;; 377 = (+ 4 9 25 49 121 169)
```

1.  

```scheme
  (define (gcd a b)
    (if (= b 0)
        a
        (gcd b (remainder a b))))

  (define (identity x) x)
  ;; product of all the positive integers less than n that
  ;; are relatively prime to n
  (define (product-relative-prime n)
    (define (relative-prime? x)
      (= (gcd x n) 1))
    (filtered-accumulate * relative-prime? 1 identity 1 1+ (- n 1)))

;; running (product-relative-prime 15) yields 896896
;; 896996 = (* 1 2 4 7 8 11 13 14)
```

## 1.34<a id="sec-1-34"></a>

Suppose we define the procedure

```scheme
(define (f g)
  (g 2))
```

Then we have

```scheme
(f square) ;; 4
(f (lambda (z) (* z (+ z 1)))) ;; 6
```

What happens if we (perversely) ask the interpreter to evaluate the combination `(f f)`? Explain.

This will be the evaluation process:

```scheme
(f f)
;;|
;;v
(f 2)
;;|
;;v
(2 2)
```

Then, because 2 is not an applicable procedure, the interpreter is going to yield an error.

## 1.35<a id="sec-1-35"></a>

Show that the golden ratio \\(\phi\\) (section 1.2.2) is a fixed point of the transformation \\(x \mapsto 1 + 1/x\\), and use this fact to compute \\(\phi\\) by means of the `fixed-point` procedure.

By definition, a fixed point of a function \\(f\\) is an \\(x\\) such that \\(f(x) = x\\). So, let \\(x = \phi = \frac{1+\sqrt{5}}{2}\\)

$$
\begin{align*}
f(x) &= 1 + \frac{1}{x} = 1 + \frac{2}{1+\sqrt{5}} \\
&= \frac{3+\sqrt{5}}{1+\sqrt{5}}\\
&= \frac{(3+\sqrt{5})(\sqrt{5} - 1)}{(1+\sqrt{5})(\sqrt{5} - 1)}\\
&= \frac{2\sqrt{5} + 2}{4} = \frac{1+\sqrt{5}}{2} = x = \phi
\end{align*}
$$

Thus, \\(\phi\\) is a fixed point of transformation \\(x \mapsto 1+1/x\\).

```scheme
(define golden-ratio
  (fixed-point (lambda (x) (+ 1
                              (/ 1.0 x)))
               1.0))
```

Running the above procedure yields 1.61800327868852458.

## 1.36<a id="sec-1-36"></a>

Modify the `fixed-point` procedure so that it prints the sequence of approximations it generates, using the `newline` and `display` primitives shown in exercise 1.22. Then find a solution to \\(x^x = 1000\\) by finding a fixed point of \\(x \mapsto \log(1000) / \log(x)\\). (Use Scheme's primitive `log` procedure, which computes natural logarithms.) Compare the number of steps this takes with and without average damping. (Note that you cannot start `fixed-point` with a guess of 1, as this could cause division by \\(\log(1) = 0\\).)

```scheme
(define tolerance 0.000001)
(define (fixed-point f first-guess)
  (define (close-enough? v1 v2)
    (< (abs (- v1 v2)) tolerance))
  (define (try guess step)
    (newline)
    (display step)
    (display ": ")
    (display guess)
    (let ((next (f guess)))
      (if (close-enough? guess next)
          next
          (try next (1+ step)))))
  (try first-guess 0))
```

Then, finding a fixed point of \\(x^x = 1000\\):

```scheme
;; without average damping
(define (x-expo n)
  (fixed-point (lambda (x) (/ (log n)
                              (log x)))
               2.0))

(define (average x y) (/ (+ x y) 2))
;; with average damping
(define (x-expo-damped n)
  (fixed-point (lambda (x) (average x
                                    (/ (log n)
                                       (log x))))
               2.0))
```

Results:

1.  Running `(x-expo 1000)`, it takes 40 (starts from 0) steps to converge:

```bash
0: 2.
1: 9.965784284662087
2: 3.004472209841214
3: 6.279195757507157
4: 3.759850702401539
5: 5.215843784925895
6: 4.182207192401397
7: 4.8277650983445906
8: 4.387593384662677
9: 4.671250085763899
10: 4.481403616895052
11: 4.6053657460929
12: 4.5230849678718865
13: 4.577114682047341
14: 4.541382480151454
15: 4.564903245230833
16: 4.549372679303342
17: 4.559606491913287
18: 4.552853875788271
19: 4.557305529748263
20: 4.554369064436181
21: 4.556305311532999
22: 4.555028263573554
23: 4.555870396702851
24: 4.555315001192079
25: 4.5556812635433275
26: 4.555439715736846
27: 4.555599009998291
28: 4.555493957531389
29: 4.555563237292884
30: 4.555517548417651
31: 4.555547679306398
32: 4.555527808516254
33: 4.555540912917957
34: 4.555532270803653
35: 4.555537970114198
36: 4.555534211524127
37: 4.555536690243655
38: 4.555535055574168
39: 4.5555361336081
;Value: 4.555535422664798
```

1.  Running `(x-expo-damped 1000)`, it takes 11 steps to converge:

```bash
0: 2.
1: 5.9828921423310435
2: 4.922168721308343
3: 4.628224318195455
4: 4.568346513136242
5: 4.5577305909237005
6: 4.555909809045131
7: 4.555599411610624
8: 4.5555465521473675
9: 4.555537551999825
10: 4.555536019631145
;Value: 4.555535758730802
```

We can see that with damping, the procedure converges way faster than the version without.

## 1.37<a id="sec-1-37"></a>

1.  An infinite *continued fraction* is an expression of the form:

\[ f = \frac{N_1}{D_1 + \frac{N_2}{D_2 + \frac{N_3}{D_3 + \cdots}}} \]

As an example, one can show that the infinite continued fraction expansion with the \\(N_i\\) and \\(D_i\\) all equal to 1 products \\(1/\phi\\), where \\(\phi\\) is the golden ratio (described in section 1.2.2). One way to approximate an infinite continued fraction is to truncate the expansion after a given number of terms. Such as a truncation&#x2013;a so-called *k-term finite continued fraction*&#x2013;has the form:

\[ \frac{N_1}{D_1 + \frac{N_2}{\ddots + \frac{N_k}{D_k}}} \]

Suppose that \\(n\\) and \\(d\\) are procedures of one argument (the term index \\(i\\)) that return the \\(N_i\\) and \\(D_i\\) of the terms of the continued fraction. Define a procedure `cont-frac` such that evaluating `(cont-frac n d k)` computes the value of the $k$-term finite continued fraction. Check your procedure by approximating \\(1/\phi\\) using

```scheme
(cont-frac (lambda (i) 1.0)
           (lambda (i) 1.0)
           k)
```

for successive values of \\(k\\). How large must you make \\(k\\) in order to get an approximation that is accurate to 4 decimal places?

1.  If your `cont-frac` procedure generates a recursive process, write one that generates an iterative one. If it generates an iterative process, write one that generates a recursive process.

```scheme
;; recursive
(define (cont-frac n d k)
  (define (single-frac i)
    (if (= i k) ;; last term
        (/ (n i) (d i))
        (/ (n i) ;; else
           (+ (d i)
              (single-frac (1+ i))))))
  (single-frac 1))

;; iterative
(define (cont-frac n d k)
  (define (cont-frac-iter res i)  ;; cnt starts from k, res starts from zero
    (cond ((= i 0) res)                     ;; end when cnt is zero
          ((= i k) (cont-frac-iter (+ res
                                 (/ (n i) (d i)))
                              (-1+ i))) ;; first term
          (else (cont-frac-iter (/ (n i)     ;; replace the result with its new value
                              (+ (d i)
                                 res))
                           (-1+ i)))))
  (cont-frac-iter 0.0 k))
```

Then test:

```scheme
(define phi (/ (+ 1 (sqrt 5)) 2))

(cont-frac (lambda (i) 1.0)
           (lambda (i) 1.0)
           k) ;; using different k
```

So, \\(1/\phi\\) is 0.61803398874. To the 4 decimal place is 0.6180. Running the `cont-frac` procedure with \\(k=10\\) yields .6179775280898876. While using \\(k=11\\) yields .6180555555555556. So the \\(k\\) should be at least 11 to make it accurate enough.

## 1.38<a id="sec-1-38"></a>

In 1737, the Swiss mathematician Leonhard Euler published a memoir *De Fractionibus Continuis*, which included a continued fraction expansion for \\(e-2\\), where \\(e\\) is the base of the natural logarithms. In this fraction, the \\(N_i\\) are all 1, and the \\(D_i\\) are successively 1, 2, 1, 1, 4, 1, 1, 6, 1, 1, 8,&#x2026; Write a program that uses your `cont-frac` procedure from exercise 1.37 to approximate \\(e\\), based on Euler's expansion.

```scheme
;; 1, 2, 1, 1, 4, 1, 1, 6, 1, 1, 8
;; when (remainder i 3) is 2, than the D_i is (i+1)/3 *2
;; else situations, D_i is 1
(define (e-D i)
  (if (= (remainder i 3) 2)
      (* (/ (+ i 1)
            3)
         2)
      1))

(define (e-approx k) ;; k-term finite, the bigger k is, the more accurate the estimate
  (+ (cont-frac (lambda (i) 1.0)
                e-D
                k)
     2))
```

Running `(e-approx 10)` yields 2.7182817182817183. Running `(e-approx 15)` yields 2.718281828470584.

## 1.39<a id="sec-1-39"></a>

A continued fraction representation of the tangent function was published in 1770 by the German mathematician J.H. Lambert:

\[ \tan x = \frac{x}{1 - \frac{x^2}{3 - \frac{x^2}{5 - \ddots}}} \]

where \\(x\\) is radians. Define a procedure `(tan-cf x k)` that computes an approximation to the tangent function based on Lambert's formula. \\(K\\) specifies the number of terms to compute, as in exercise 1.37.

One observation is that, we should take the minus sign into \\(N_i\\) to make it compatible with `cont-frac`: 

$$ 
N_i = 
\begin{cases} 
x &i = 1\\ 
-x^2 &i = 2, 3, \cdots 
\end{cases} 
$$

And for \\(D_i\\):

\[ D_i = 2i - 1 \]

```scheme
(define (tan-cf x k)
  (cont-frac (lambda (i)
               (cond ((= i 1) x)
                     (else (* x x -1.0))))
             (lambda (i) (- (* 2 i)
                            1.0))
             k))
```

Running this procedure with \\(k=100\\):

| Call              | Result             | Accurate? |
| `(tan-cf 3.0 10)` | -.1425465438397583 | Y         |
| `(tan-cf 30 10)`  | 3.468225849832513  | N         |
| `(tan-cf 30 100)` | -6.405331196646245 | Y         |
| `(tan-cf 70 100)` | 1.2219599181369425 | Y         |

We notice that, the larger the radian is, the larger \\(k\\) required to make our approximation accurate.

## 1.40<a id="sec-1-40"></a>

Define a procedure `cubic` that can be used together with the `newtons-method` procedure in expressions of the form

```scheme
(newtons-method (cubic a b c) 1)
```

to approximate zeros of the cubic \\(x^3 + ax^2 + bx + c\\).

```scheme
(define (cubic a b c)
  (lambda (x)
    (+ (* x x x)
       (* a x x)
       (* b x)
       c)))

(define (solve-cubic a b c guess)
  (newtons-method (cubic a b c) guess))
```

Running results:

| Call                        | Running Results     | Actual Answer |
| `(solve-cubic 6 11 6 1.0)`  | -.9999999999359228  | -1            |
| `(solve-cubic 6 11 6 -4)`   | -2.9999999999839293 | -3            |
| `(solve-cubic 6 11 6 -2.1)` | -2.000000000000003  | -2            |

## 1.41<a id="sec-1-41"></a>

Define a procedure `double` that takes a procedure of one argument as argument and returns a procedure that applies the original procedure twice. For example, if `inc` is a procedure that adds 1 to its argument, then `(double inc)` should be a procedure that adds 2. What value is returned by

```scheme
(((double (double double)) inc) 5)
```

```scheme
(define (inc x) (1+ x))

(define (double f)
  (lambda (x)
    (f (f x))))
```

Using substitution:

```scheme
(((double
   (double double))
  inc)
 5)

(((double
   (lambda (x) (double (double x))))
  inc)
 5)

(((lambda (x)
    (double (double (double (double x)))))
  inc)
 5)

((double (double (double (double inc))))
 5)

((double (double (double (lambda (x) (inc (inc x)))))))

;; ....
;; finally it is equivalent to +16
```

Thus the result should be 16+5 = 21.

A easier way to deduct this: `double` is nested, and finally applied to `inc`.

\[ ((2^2)^2)^2 \times (+1) = 16 \]

Running it verifies this fact. The answer is 16+5 = 21.

## 1.42<a id="sec-1-42"></a>

Let \\(f\\) and \\(g\\) be two one-argument functions. The *composition* \\(f\\) after \\(g\\) is defined to be the function \\(x \mapsto f(g(x))\\). Define a procedure `compose` that implements composition. For example, if `inc` is a procedure that adds 1 to its argument,

```scheme
((compose square inc) 6);; should yield 49
```

```scheme
(define (compose f g)
  (lambda (x)
    (f (g x))))
```

## 1.43<a id="sec-1-43"></a>

If \\(f\\) is a numerical function and \\(n\\) is a positive integer, then we can form the \\(n\\) th repeated application of \\(f\\), which is defined to be the function whose value at \\(x\\) is

\[ f(f(\cdots(f(x))\cdots)) \]

For example, if \\(f\\) is the function \\(x\mapsto x+1\\), then the \\(n\\) th repeated application of \\(f\\) is the function \\(x \mapsto x+n\\). If \\(f\\) is the operation of squaring a number, then the \\(n\\) th repeated application of \\(f\\) is the function that raises its argument to the \\(2^n\\) th power. Write a procedure that takes as inputs a procedure that computes \\(f\\) and a positive number \\(n\\) and returns the procedure that computes the \\(n\\) th repeated application of \\(f\\). Your procedure should be able to be used as follows:

```scheme
((repeated square 2) 5) ;; should yield 625
```

```scheme
(define (repeated f n)
  (define (repeat-iter g cnt)
    (cond ((> cnt n) g)
          (else (repeat-iter (lambda (x) (f (g x)))
                             (1+ cnt)))))
  (repeat-iter f 2))
```

It is a iterative version here. Next I will try to write a recursive one:

```scheme
(define (repeated f n)
  (cond ((= n 1) f)
        (else (compose f
                       (repeat f (- n 1))))))

;; or, if we don't have compose at hand
(define (repeated f n)
  (cond ((= n 1) f)
        (else (lambda (x)
                (f ((repeat f (- n 1)) x))))))
```

## 1.44<a id="sec-1-44"></a>

The idea of smoothing a function is an important concept in signal processing. If \\(f\\) is a function and \\(dx\\) is some small number, then smoothed version of \\(f\\) is the function whose value at a point \\(x\\) is the average of \\(f(x-dx)\\), \\(f(x)\\), and \\(f(x+dx)\\). Write a procedure `smooth` that takes as input a procedure that computes \\(f\\) and returns a procedure that computes the smoothed \\(f\\). It is sometimes valuable to repeatedly smooth a function (that is smooth the smoothed function, and so on) to obtained the $n$-fold smoothed function. Show how to generate the $n$-fold smoothed function of any given function using `smooth` and `repeated` from exercise 1.43.

```scheme
(define dx 0.00001)

(define (smooth f)
  (lambda (x)
    (/ (+ (f (- x dx))
          (f x)
          (f (+ x dx)))
       3.0)))

(define (n-fold-smooth f n)
  (lambda (x)
    (((repeated smooth n) f) x)))
```

Running it using `cubic` from exercise 1.40 as an example:

```scheme
((n-fold-smooth (cubic 1 1 1) 3) 10)
```

Yields 1111.0000000062.

## 1.45<a id="sec-1-45"></a>

We saw in section 1.3.3 that attempting to compute square roots by naively finding a fixed point of \\(y \mapsto x/y\\) does not converge, and that this can be fixed by average damping. The same method works for finding cube roots as fixed points of the average-damped \\(y \mapsto x/y^2\\). Unfortunately, the process does not work for fourth roots&#x2013;a single average damp is not enough to make a fixed-point search for \\(y \mapsto x/y^3\\) converge. On the other hand, if we average damp twice (i.e., use the average damp of the average damp of \\(y\mapsto x/y^3\\)) the fixed-point search does converge.

Do some experiments to determine how many average damps are required to compute $n$th roots as a fixed-point search based upon repeated average damping of \\(y \mapsto x/y^{n-1}\\). Use this to implement a simple procedure for computing $n$th roots using `fixed-point`, `average-damp`, and the `repeated` procedure of exercise 1.43. Assume that any arithmetic operations you need are available as primitives.

```scheme
(define (nth-root x n)
  (fixed-point ((repeated average-damp
                          (nth-damp-needed n)) ;; how many damping needed
                (nth-root-mapsto n x))         ;; apply the repeated damping to the original mapsto function.
               1.0))

(define (log-base x b)
  (/ (log x) (log b)))

(define (nth-damp-needed n)
  "Returns the exact number of times average-damp needed for the fixed point to converge."
  (floor (log-base n 2)))

(define (nth-root-mapsto n x)
  "Gives the mapsto function for solving nth root"
  (lambda (y)
    (/ x (fast-expt y (- n 1)))))

(define (fast-expt b n)
  (cond ((= n 0) 1)
        ((even? n) (square (fast-expt b (/ n 2))))
        (else (* b
                 (fast-expt b (- n 1))))))

(define (even? n)
  (= (remainder n 2) 0))

;; some experiments I ran:
(fixed-point ((repeated average-damp 1) (nth-root-mapsto 3 133)) 1.0)
(fixed-point ((repeated average-damp 2) (nth-root-mapsto 4 133)) 1.0)
(fixed-point ((repeated average-damp 2) (nth-root-mapsto 7 133)) 1.0)
(fixed-point ((repeated average-damp 3) (nth-root-mapsto 8 133)) 1.0)
(fixed-point ((repeated average-damp 3) (nth-root-mapsto 15 133)) 1.0)
(fixed-point ((repeated average-damp 4) (nth-root-mapsto 16 133)) 1.0)
```

The pattern I found from running different experiments is that: \\(D = \lfloor \log n \rfloor\\), where \\(D\\) is the required times of damping needed to compute the root for $n$th roots.

## 1.46<a id="sec-1-46"></a>

Several of the numerical methods described in this chapter are instances of an extremely general computational strategy known as *iterative improvement*. Iterative improvement says that, to compute something, we start with an initial guess for the answer, test if the guess is good enough, and otherwise improve the guess and continue the process using the improved guess as the new guess.

Write a procedure `iterative-improve` that takes two procedures as arguments: a method for telling whether a guess is good enough and a method for improving a guess. `iterative-improve` should return as its value a procedure that takes a guess as argument and keeps improving the guess until it is good enough. Rewrite the `sqrt` procedure of section 1.1.7 and `fixed-point` procedure of section 1.3.3 in terms of `iterative-improve`.

```scheme
(define (iterative-improve good-enough? improve)
  (lambda (first-guess)
    (define (try guess)
      (let ((next (improve guess)))
        (if (good-enough? guess next)
            guess
            (try next))))
    (try first-guess)))

(define (average x y) (/ (+ x y) 2.0))

(define (close-enough? x y) (< (abs (- x y)) 0.00001))

(define (sqrt x)
  ((iterative-improve close-enough?
                      (lambda (y) (average y (/ x y))))
   x))

(define (fixed-point f first-guess)
  ((iterative-improve close-enough?
                      f)
   first-guess))
```
