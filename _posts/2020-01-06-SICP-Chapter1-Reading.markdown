---
layout: single
title:  "SICP Chapter1 Reading Note"
date:   2020-01-06 09:08:55 +0800
mathjax: true

excerpt: "Structure and Intrepretation of Computer Programs Reading Note: Chapter 1."
header:
  teaser: /assets/images/codeback.jpg
  overlay_image: /assets/images/codeback.jpg
  overlay_filter: 0.4 # same as adding an opacity of 0.5 to a black background
  
---

{% include toc title="Contents" %}

# Building Abstractions with Procedures

Acts of mind:

1.  Combining simple ones into a compound one
2.  Compare and see the relationship
3.  Abstraction

Real-world programming, requires care, expertise, and wisdom.

Master software engineers have the ability to organize programs so that they can
be reasonably sure that the resulting processes will perform the tasks intended. 
They can visualize the behavior of their systems in advance.

Well-designed computational systems, are designed in a modular manner, so that
the parts can be constructed, replaced and debugged separately.


<a id="org63faea8"></a>

## Programming in Lisp

Lisp = LISt Processing, was designed to provide symbol-manipulating capabilities
for attacking programming problems such as symbolic differentiation and integration
of algebraic expression.

The most significant of these features is the fact that Lisp descriptions of process,
called procedures, can themselves be represented and manipulated as Lisp data.

The importance of this is that there are powerful program-design techniques
that rely on the ability to blur the traditional distinction between "passive"
data and "active" processes.

The ability to represent procedures as data also makes Lisp an excellent language for 
writing programs that must manipulate other programs as data, such as in interpreters and
compilers that support computer languages.


<a id="orgcc0c6fc"></a>

## The elements of programming

The language serves as a framework within which we organize our ideas about processes.
Every powerful language has 3 mechanisms for accomplishing this:

1.  primitive expressions
2.  means of combination
3.  means of abstraction

Procedures and data.

Prefix notation advantage:

1.  accommodate procedures that may take an arbitrary number of arguments
2.  extends in a straight-forward way to allow combinations to be nested, to 
    have combinations whose elements are themselves combinations

`read-eval-print` loop.


<a id="orgf02f966"></a>

### 1.1.3 Evaluating Combinations

To evaluate a combination, do the following:

1.  Evaluate the subexpressions of the combination
2.  Apply the procedure that is the value of the leftmost subexpression (the operator)
    to the arguments that are the values of the other subexpressions (the operands)

Repeated application of the first step bring us to the point where we need to evaluate
primitive expressions such as, numerals, built-in operators, or other names.
We take care of the primitive cases by stipulating that:

-   the values of numerals are the numbers that they name.
-   the values of built-in operators are the machine instruction sequences
    that carry out the corresponding operations
-   the values of other names are the objects associated with those names in 
    the environment.

The general notion of the environment as providing a context in which evaluation takes
place will play an important role in our understanding of program execution.

Special forms: where the evaluation rule doesn't apply. For example, `define`.
Each special form have their own evaluation rules.

In comparison with most other programming languages, Lisp has a very simple syntax;
that is, the evaluation rule for expressions can be described by a simple general rule
together with specialized rules for a small number of special forms.


<a id="org6202fbb"></a>

### 1.1.4 Compound Procedures

-   Numbers and arithmetic operations are primitive data and procedures.
-   Nesting of combinations provides a means of combining operations
-   Definitions that associate names with values provide a limited means of abstraction.

It is possible, indeed important, to be able to separate procedure and the name.
We can create procedures without naming then, and we can also give names to procedures 
that have already been created.


<a id="org17eb8e6"></a>

### 1.1.5 The Substitution Model for Procedure Application

To apply a compound procedure to arguments, evaluate the body of the procedure with each
formal parameter replaced by the corresponding argument.

Substitution model for procedure application. It can be taken as a model that determines the 
"meaning" of procedure application, insofar as the procedures in this chapter are concerned.
However, there are two points that should be stressed:

-   The purpose of the substitution is to help us think about procedure application, not to 
    provide a description of how the interpreter really works. In practice, the "substitution"
    is accomplished by using a local environment for the formal parameters.
-   The substitution model is only a way to get started thinking formally about the evaluation
    process. When we get to the use of procedures with "mutable data", we will see that the 
    substitution model breaks down and must be replaced by a more complicated model of procedure
    application.
    In general, when modeling phenomena in science and engineering, we begin with simplified,
    incomplete models. As we examine things in greater detail, these simple models become
    inadequate and must be replaced by more refined models.

1.  Applicative order versus normal order

    According to the description of evaluation given in 1.1.3, the interpreter first evaluates
    the operator and operands and then applies the resulting procedure to the resulting arguments.
    This is not the only way to perform evaluation. 
    
    An alternative evaluation model would not evaluate the operands until their values were needed. 
    Instead it would first substitute operand expressions for parameters until it obtained an 
    expression involving only primitive operators, and would then perform the evaluation. 
    
    "Fully expand and then reduce" evaluation method is known as **normal-order** evaluation,
    in contrast to the "evaluate the arguments and then apply" method that the interpreter actually
    uses, which is called **applicative-order** evaluation.
    
    Lisp uses applicative-order evaluation, partly because of the addtional **efficiency** obtained from
    avoiding multiple evaluations of expressions. 
    Normal-order evaluation becomes much more complicated to deal with when we leave the realm of
    procedures that can be modeled by substitution.
    On the other hand, normal-order evaluation can be an extremely valuable
    tool, and we will investigate some of its implications in latter chapters.


<a id="orgf427a47"></a>

## Conditional Expressions and Predicate

But by far, we lack ways of doing case analysis. So we will introduce them in the follow section.

The general form of a conditional expression is
```scheme
    (cond (p1 e1)
          (p2 e2)
          (p3 e3))
```
the symbol `cond` is followed by parenthesized pairs of expressions called clauses.
The first pair is predicate, whose value is interpreted as either true or false.

Conditional expressions are evaluated as follows. The predicate is evaluated first.
If the value is false, then next predicate is evaluated. This keeps going until one
predicate's value is found to be true, in which case the interpreter returns the value 
of the corresponding consequent expression of the clause as the value of the corresponding
consequent expression of the clause as the value of the conditional expression.
If none of the predicates is found to be true, the value of the cond is undefined.

The word predicate is used for procedures that return true or false, as well as for expressions 
that evaluate to true or false. The absolute-value procedure `abs` makes use of the primitive 
predicates <, > and =.

Here is one way to write the absolute-value procedure:
```scheme
    (define (abs x)
      (cond ((< x 0) (- x))
            (else x)))
```
`else` is a special symbol that can be in place of the `<p>` in the final clause of a `cond`.

And the following is another way to write the absolute-value procedure:
```scheme
    (define (abs x)
      (if (< x 0)
          (- x)
          x))
```
This uses the special form `if`, a restricted type of conditional that can be used when there
are precisely two cases in the case analysis. The general form of an `if` expression is:
```scheme
    (if <predicate> <consequent> <alternative>)
```
To evaluate an `if` expression, the interpreter starts by evaluating the `<predicate>` part of
expression. If the `<predicate>` evaluates to a true value, the interpreter then evaluates the
`<consequent>` and returns its value. Otherwise it evaluates the `<alternative>` and returns 
its value.

In addition to primitive predicates such as <, = and >, there are logical composition operations,
which enable us to construct compound predicates. The three frequently used are these:
```scheme
    (and <e1> ... <en>)
    (or  <e1> ... <en>)
    (not <e>)
```
For `and`, the interpreter evaluates the expressions one at a time, from left to right, if any
is false, the value of the `and` expression is false, and the rest of the `<e>` are not evaluated.

Notice that `and` and `or` are special forms, not procedures, because the subexpressions are not 
necessarily all evaluated. `Not` is an ordinary procedure.

We can define a predicate to test whether one number is greater than or equal to another as:
```scheme
    (define (>= x y)
      (or (> x y)
          (= x y)))
```
Or, alternatively, as
```scheme
    (define (>= x y)
      (not (< x y)))
```

<a id="org3482d59"></a>

## Example: Square Roots by Newton's Method

There is an important difference between mathematical functions and computer procedures.
**Procedures must be effective**.

The contrast between function and procedure is a reflection of the general distinction between describing 
properties of things and describing how to do things.

The definition of a square root is:

\\(\sqrt{x}\\) = the \\(y\\) such that \\(y \geq 0\\) and \\(y^2 = x\\)

The definition does not describe a procedure. Indeed, it tells us almost nothing about how to find the square
root of a given number. It will not help matters to rephrase this definition in pseudo-Lisp:
```scheme
    (define (sqrt x)
      (the y (and (>= y 0)
                  (= (square y) x))))
```
The mathematical knowledge is used usually to define the problem of "what is".
But the real thing we are most concerned in computer science is "how to", the imperative knowledge.

The declarative and imperative knowledge are intimately related, as indeed are mathematics and computer
science.There is a large number of research aimed at establishing techniques for proving the correctness
of a program, and much of the difficulty of this subject has to do with negotiating the transition
between imperative statements (from which programs are constructed) and declarative statements (which
can be used to deduce things).

In a related vein, an important current area in programming-language design is the exploration of so-called
very high-level languages, in which one actually programs in terms of declarative statements. The idea is to
make interpreters sophisticated enough, so that, given "what is" knowledge specified by the programmer, they
can generate "how to" knowledge automatically. This cannot be done in general, but there are important areas
where progress has been made.
```scheme
    (define (sqrt x)
      (define (good-enough? guess x)
        (< (abs (- (square guess) x)) tolerance))
      (define (improve guess x)
        (average guess (/ x guess)))
      (define (sqrt-iter guess x)
        (if (good-enough? guess x)
            guess
            (sqrt-iter (improve guess x) x)))
      (sqrt-iter 1.0 x))
```

<a id="orgc1ebed7"></a>

## Procedures as Black-Box Abstractions

Each procedure accomplishes an identifiable task that can be used as a module in defining other procedures.

**Procedure Abstraction**: A procedure definition should be able to suppress detail. A user should not need
to know how the procedure is implemented in order to use it.


<a id="org871a322"></a>

### Local Names

**Principle**: The meaning of a procedure should be independent of the parameter names used by its author.

A formal parameter of a procedure has a very special role in the procedure definition, in that it doesn't matter
what name the formal parameter has. Such a name is called a **bound variable**, and we say that the procedure definition
binds its formal parameters.

If a variable is not bound, we say that it is **free**.

The set of expressions for which a binding defines a name is called the *scope* of that name.


<a id="orgaafc9ce"></a>

### Internal Definitions and Block Structure

As shown in the code above, all of the sub-procedures are all included in the definition of `sqrt`. This is 
basically the right solution to the simplest name-packaging problem. But we can do more.

Since `x` is bound in the definition of sqrt, the procedures `good-enough?`, `improve`, and `sqrt-iter`, which
are defined internally to `sqrt`, are in the scope of `x`. Thus, it is not necessary to pass `x` explicitly 
to each of these procedures. Instead, we allow `x` to be a free variable in the internal definitions.
```scheme
    (define (sqrt x)
      (define tolerance 0.0001)
      (define (good-enough? guess)
        (< (abs (- (square guess) x)) tolerance))
      (define (improve guess)
        (average guess (/ x guess)))
      (define (sqrt-iter guess)
        (if (good-enough? guess)
            guess
            (sqrt-iter (improve guess) x)))
      (sqrt-iter 1.0))
```
Then `x` gets its value from the argument with which the enclosing procedure `sqrt` is called.
This discipline is called **lexical scoping**.

We will use block structure extensively to help us break up large programs into tractable pieces.


<a id="org2934af8"></a>

## Procedures and Processes They Generate

The ability to visualize the consequences of the actions under consideration is crucial to becoming an expert programmer,
just as it is in any synthetic, creative activity.

A procedure is a pattern for the local evolution of a computational process. It specifies how each stage of the process 
is built upon the previous stage.

We would like to be able to make statements about the overall, or global, behavior of a process whose local evolution has 
been specified by a procedure.

In this section we will examine some common "shapes" for processes generated by simple procedures. We will also investigate
the rates at which these processes consume the important computational resources of time and space.


<a id="orgc915185"></a>

### Linear Recursion and Iteration

Factorial function:
\[
n! = n\cdot (n-1) \cdot (n-2) \cdots 3 \cdot 2 \cdot 1
\]

Linear recursion approach:
```scheme
    (define (factorial n)
      (if (= n 1)
          1
          (* n (factorial (- n 1)))))
```

A different way of computing it is, we maintain a running product, together with a counter that counts from 1 up to \\(n\\).

```scheme
    (define (factorial n)
      (define (iter product counter)
        (if (> counter n)
            product
            (iter (* counter product)
                  (+ counter 1))))
      (iter 1 1))
```

When we consider the "shapes" of the two processes, we find that they evolve quite differently.

The first process builds up a chain of *deferred operations*. The contraction occurs as the operations are actually 
performed. This type of process, characterized by a chain of deferred operations, is called a *recursive process*.

Carrying out this process requires that the interpreter keep track of the operations to be performed later on.

In the computation of factorial, the length of the chain of deferred multiplications, and hence the amount of information
needed to keep track of it, grows linearly with \\(n\\), just like the number of steps. Such a process is called a 
**linear recursive process**.

By contrast, the second process does not grow and shrink. At each step, all we need to keep track of, for any \\(n\\),
are the current values of the variables `product`, `counter`, and `max-count`. We call this **iterative process**.

In general, an iterative process is one whose state can be summarized by a fixed number of **state variables**,
together with a fixed rule that describes how the state variables should be updated as the process moves from 
state to state and an (optional) end test that specifies conditions under which the process should terminate.

In computing \\(n!\\), the number of steps required grows linearly with \\(n\\). Such a process is called a **linear iterative process**.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<tbody>
<tr>
<td class="org-left">Differences</td>
<td class="org-left">&#xa0;</td>
</tr>


<tr>
<td class="org-left">Iterative</td>
<td class="org-left">variables provide a complete description of the state of the process at any point</td>
</tr>


<tr>
<td class="org-left">Recursive</td>
<td class="org-left">there is some additional information maintained by the interpreter</td>
</tr>
</tbody>
</table>

The hidden information are not contained in the program variables, which indicates "where the process is" in negotiating
the chain of deferred operations. The longer the chain, the more information must be maintained.

In contrasting iteration and recursion, we must be careful not to confuse the notion of a 
*recursive process* with the notion of a *recursive procedure*.
When we describe a procedure as recursive, we are referring to the syntactic fact that the procedure definition refers
(either directly or indirectly) to the procedure itself. But when we describe a process as following a pattern that is,
say, linearly recursive, we are speaking about how the process evolves, not about the syntax of how a procedure is written.

It may seen disturbing that we refer to a recursive procedure such as `iter` in the factorial as generating an iterative process.
However, the process really is iterative: Its state is captured completely by its three state variables, and an interpreter
need keep track of only three variables in order to execute the process.

One reason that the distinction between process and procedure may be confusing is that most implementations of common languages
(including Ada, Pascal, and C) are designed in such a way that the interpretation of any recursive procedure consumes an amount
of memory that grows with the number of procedure calls, even when the process described is, in principle, iterative.

As a consequence, these languages can describe iterative processes only by resorting to special purpose "looping-constructs"
such as `do`, `repeat`, `until`, `for`, and `while`. But the Scheme we shall consider does not share this defect.
It will execute an iterative process in constant space, even if the iterative process is described by a recursive procedure.
An implementation with this property is called **tail-recursive**.

When we discuss the implementation of procedures on register machines in chapter 5, we will see that any iterative process 
can be realized "in hardware" as machine that has a fixed set of registers and no auxiliary memory. 
In contrast, realizing a recursive process requires a machine that uses an auxiliary data structure known as stack.

With tail recursive implementation, iteration can be expressed using the ordinary procedure call mechanism, so that
special iteration constructs are useful only as syntactic sugar.


<a id="orge2aec7d"></a>

## Tree Recursion

Fibonacci numbers:

$$
Fib(n) = 

\begin{cases}
0 & \text{if } n=0 \\
1 & \text{if } n=1 \\
Fib(n-1) + Fib(n-2) & \text{otherwise}
\end{cases}
$$

```scheme
    (define (fib n)
      (cond ((= n 0) 0)
            ((= n 1) 1)
            (else (+ (fib (- n 1))
                     (fib (- n 2))))))
```

This procedure is instructive as a prototypical tree recursion, but it is a terrible way to compute
Fibonacci numbers because it does so much redundant computation.
In fact, it is not hard to show that the number of times the procedure will compute `(fib 1)` or `(fib 0)`
(the number of leaves) is precisely \\(Fib(n+1)\\).

To show how bad this is, one can show that the value of \\(Fib(n)\\) grows exponentially with \\(n\\). More
precisely, \\(Fib(n)\\) is the closet integer to \\(\phi^n/ \sqrt{5}\\) where

\[
\phi = (1 + \sqrt{5}) / 2 \approx 1.6180
\]

is the golden ratio, which satisfies the equation

\[
\phi^2 = \phi + 1
\]

Thus, the process uses a number of steps that grows exponentially with the input.
On the other hand, the space required grows only linearly with the input, because we need keep track only 
of which nodes are above us in the tree at any point in the computation.

\*\*In general, the number of steps required by a tree-recursive process will be proportional to the 
number of nodes in the tree, while the space required will be proportional to the maximum depth of
the tree.\*\*

We can also formulate an iterative process for computing the Fibonacci numbers.
```scheme
    (define (fib n)
      (fib-iter 1 0 n))
    
    (define (fib-iter a b count)
      (if (= count 0)
          b
          (fib-iter (+ a b) a (- count 1))))
```
It is not hard to show that, after applying this transformation \\(n\\) times, \\(a\\) and \\(b\\) will hold the
values of \\(Fib(n+1)\\) and \\(Fib(n)\\), respectively.

This method of computing is linear iterative. The difference between the tree-recursive process and
the linear iterative process is enormous, even for small inputs.

But tree-recursion should not be considered useless. When we consider processes that operate on 
hierarchically structured data rather numbers, we will find that tree recursion is a natural and 
powerful tool.

But even in numerical operations, tree-recursive processes can be helpful in helping us to understand
and design programs.


<a id="orgd73066e"></a>

### Example: Counting Change

How many different way can we make change of $1.00, given half-dollars, quarters, dimes, nickles, 
and pennies?
More generally, can we write a procedure to compute the number of ways to change any given amount
of money?

The problem has a simple solution as a recursive procedure.
Suppose we think of types of coins available as arranged in some order. Then the following relation holds:
The number of ways to change amount \\(a\\) using \\(n\\) kinds of coins equals

-   the number of ways to change amount \\(a\\) using all but the first kind of coin, plus
-   the number of ways to change amount \\(a-d\\) using all \\(n\\) kinds of coins, where \\(d\\) is the denomination of
    the first kind of coin. (force to use at least one of the first kind of coins in the solution)

To see why this is true, observe that the ways to make change can be divided into two groups:
those that do not use any of the first kind of coins, and those that do.

Thus we can recursively reduce the problem of changing a given amount to the problem of changing smaller
amounts using fewer kinds of coins. Consider this reduction rule carefully, and convince yourself that we
can use it to describe an algorithm if we specify the following degenerate cases:

1.  If \\(a\\) is exactly 0, we should count that as 1 way to make change
2.  If \\(a\\) is less than 0, we should count that as 0 ways to make change.
3.  If \\(n\\) is 0, we should count that as 0 ways to make change.

We can easily translate this into a recursive procedure

```scheme
      (define (count-change amount)
        (cc amount 5))
    
      ;; assume that coins are sorted in some order
      ;; pennies      1
      ;; nickles      5
      ;; dimes        10
      ;; quarters     25
      ;; half-dollars 50
   
   (define (cc amount kinds-of-coins)
        (cond ((= amount 0) 1)
              ((< amount 0) 0)
              ((= kinds-of-coins 0) 0)
              (else (+ (cc amount ;; adding two groups of changing:
                           (- kinds-of-coins 1)) ;; 1. doesn't use the first kind
                       (cc (- amount
                              (first-denomination kinds-of-coins))
                           kinds-of-coins)))))   ;; 2. use at least one of it.
    
      (define (first-denomination kinds-of-coins)
        (cond ((= kinds-of-coins 1) 1)
              ((= kinds-of-coins 2) 5)
              ((= kinds-of-coins 3) 10)
              ((= kinds-of-coins 4) 25)
              ((= kinds-of-coins 5) 50)))
    
    (count-change 100)
```
Here we are thinking of the coins as arranged in order from largest to smallest, but any order will do
as well.

This procedure generates a tree-recursive process with redundancies similar to the recursive version of `fib`.
And it is not so obvious how to design a better algorithm for computing the result, and we leave that as a challenge.

1.  Challenge: More Efficient Counting Change Procedure

    1.  Method 1: DP (tabulation or memorization)
    
        **Top-Down DP**:
        Prerequisites of top-down DP to be applicable:
        
        1.  This problem has optimal sub-structures: the solution of the sub-problem is part of the original
            problem.
        2.  This problem has overlapping sub-problems.
            This is the key characteristic of DP! The search space of this problem is not as big as the 
            rough bound obtained in the naive solution.
        
        **Bottom-up DP**: 
        the true form of DP, DP was originally known as tabular method.
        The basic steps to build bottom-up DP solution are as follows:
        
        1.  Determine the required set of parameters that uniquely describe the problem (the state)
        2.  If there are \\(N\\) parameters required to represent the states, prepare an \\(N\\) dimensional DP table,
            with one entry per state. Then we need to initialize some cells of the DP table with known initial 
            values (the base cases).
        3.  With the base-case cells/states in the DP table already filled, determine the cells/states
            that can be filled next (the transitions). Repeat this process until the DP table is complete.
            For the bottom-up DP, this part is usually accomplished through iterations, using loops (more details
            about this later)
        
        For this challenge, to accomplish the DP method, either top-down or bottom-up, we need to know the way
        of storing a table in scheme.
        ```scheme
            ;; quicker approach for counting change
            ;; overlapping sub-problems: there is multiple way to reduce the amount
            ;; to a certain value. And the DP solution is based on that observation.
            ;; TODO
            (define (count-change amount)
              )
        ```
    2.  Method 2: Iterative
    
        What should be the iterative approach? Well, strictly speaking, the bottom-up DP is iterative.
        However, what I am looking for is something that is pure iterative and doesn't need to memorize
        a table for solving the problem. Is that even possible?


<a id="org786a061"></a>

### Orders of Growth

Let \\(n\\) be the parameter that measures the size of the problem, and let \\(R(n)\\) be the amount of resources
the process requires for a problem of size \\(n\\).

The meaning behind of the parameter \\(n\\) can varies. For instance, if our goal is to compute an approximation
to the square root of a number, we might take \\(n\\) to be the number of digits accuracy required.
For matrix multiplication we might take \\(n\\) to be the number of rows in the matrices.

In general there are a number of properties of the problem with respect to which it will be desirable to 
analyze a given process.
Similarly, \\(R(n)\\) might measure the number of internal storage registers used, the number of elementary machine 
operations performed, and so on. In computers that do only a fixed number of operations at a time, the time
required will be proportional to the number of elementary machine operations performed.

We say that \\(R(n)\\) has order of growth \\(\Theta(f(n))\\), written \\(R(n) = \Theta(f(n))\\), if there are positive constants \\(k_1\\) and
\\(k_2\\) independent of \\(n\\) such that:

\[
k_1 f(n) \leq R(n) \leq k_2 f(n)
\]

for any sufficiently large value of \\(n\\). (In other words, for large \\(n\\), the value \\(R(n)\\) is sandwiched between
\\(k_1f(n)\\) and \\(k_2f(n)\\).)

Order of growth provide only a crude description of the behavior of a process. On the other hand, order
of growth provides a useful indication of how we may expect the behavior of the process to change as we change the
size of the problem.


<a id="orgac21499"></a>

### Exponentiation

Considering computing exponential of a given number:
\[
b^n
\]
One way to do this is via recursive definition:

$$
\begin{align*}
b^n &= b\cdot b^{n-1}\\
b^0 &= 1
\end{align*}
$$

which translates readily into procedure:
```scheme
    (define (expt b n)
      (if (= n 0)
          1
          (* b (expt b (- n 1)))))
```
This is linear recursion where both space and number of steps taken are \\(\Theta(n)\\).

Just as factorial, we can readily formulate an linear iteration version:
```scheme
    (define (expt b n)
      (expt-iter b n 1))
    
    (define (expt-iter b count product)
      (if (= count 0)
          product
          (expt-iter b
                     (- count 1)
                     (* b product))))
```
This version requires \\(\Theta(n)\\) steps and \\(\Theta(1)\\) space.

We can compute exponentials in fewer steps by using successive squaring.

$$
\begin{align*}
b^2 &= b\cdot b \\
b^4 &= b^2 \cdot b^2 \\
b^8 &= b^4 \cdot b^4
\end{align*}
$$

This method works fine for exponents that are powers of 2. We can also take advantage of 
successive squaring in computing exponentials in general if we use the rule.

$$
\begin{align*}
b^n &= (b^{n/2})^2 &\text{if}\ n\ \text{is even}\\
b^n &= b\cdot b^{n-1} &\text{if}\ n\ \text{is odd}
\end{align*}
$$

We can express this method as a procedure:
```scheme
    (define (fast-expt b n)
      (cond ((= n 0) 1)
            ((even? n) (square (fast-expt b (/ n 2))))
            (else (* b
                     (fast-expt b (- n 1))))))
    
    (define (even? n)
      (= (remainder n 2) 0))
    
    (define (square x) (* x x))
```
The process evolved by `fast-expt` grows logarithmically with \\(n\\) in both space and number of steps.
The process has \\(\Theta(n)\\) growth.


<a id="orgd51975b"></a>

### Greatest Common Divisors

The greatest common divisor (GCD) of two integers \\(a\\) and \\(b\\) is defined to be the
largest integer that divides both \\(a\\) and \\(b\\) with no remainder. We will need a way
to compute GCD when we investigate how to implement rational number arithmetic in 
next chapter. (To reduce a rational number to lowest terms).

The idea of the Euclid's Algorithm is based on the observation that, if \\(r\\) is the
remainder when \\(a\\) is divided by \\(b\\), then the common divisors of \\(a\\) and \\(b\\) are
precisely the same as the common divisors of \\(b\\) and \\(r\\). Thus, we can use the equation:

\[
GCD(a, b) = GCD(b, r)
\]

to successively reduce the problem of computing a GCD to the problem of computing the
GCD of smaller and smaller pairs of integers.

It is possible to show that starting with any two positive integers and performing 
repeated reductions will always eventually produce a pair where the second number is 0.
Then the GCD is the other number in the pair.
```scheme
      (define (gcd a b)
        (if (= b 0)
            a
            (gcd b (remainder a b))))
    (gcd 206 40)
```
This generates an iterative process, whose number of steps grows as the logarithm
of the numbers involved.

The fact that the number of steps required by Euclid's Algorithm has logarithm growth
bears an interesting relation to the Fibonacci numbers:

-   **Lame Theorem**: If Euclid's Algorithm requires \\(k\\) steps to compute the GCD of
    some pair, then the smaller number in the pair must be greater than or equal to the $k$th
    Fibonacci number.

We can use this theorem to get an order-of growth estimate fro Euclid's Algorithm.
Let \\(n\\) be the smaller of the two inputs to the procedure. If the process takes \\(k\\)
steps, then we must have \\(n \geq Fib(k) \approx \phi^k / \sqrt{5}\\).
Notice that the relationship between \\(n\\) and \\(k\\), as \\(n\\) grows, \\(k\\) grow as the logarithm
(to the base \\(\phi\\)) of \\(n\\), or less. Hence, the order of growth is \\(\Theta(\log{n})\\).


<a id="org22967a9"></a>

### Example: Testing for Primality

This section describes two methods for checking the primality of an integer \\(n\\), one with 
order of growth \\(\Theta (\sqrt{n})\\), and a "probabilistic" algorithm with order of growth
\\(\Theta(\log{n})\\).

1.  Searching for divisors

    One way to test if a number is prime is to find the number's divisors.
    The following program finds the smallest integral divisor (greater than 1) of a given 
    number \\(n\\). It does this in a straightforward way, by testing \\(n\\) for divisibility
    by successive integers starting with 2.
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
    ``` 
    With this, we can next easily check if a number is prime or not by comparing 
    itself with its smallest divisor.
    ```scheme
        (define (prime? n)
          (= (smallest-divisor n)
             n))
    ```
    Notice that the quick quitting condition checking in the first clause of `cond`.
    It is based on the fact that if \\(n\\) is not prime, it must have a divisor less
    than or equal to \\(\sqrt{n}\\). 
    Thus, the number of steps required to identify \\(n\\) as prime will have order of growth
    \\(\Theta(\sqrt{n})\\).

2.  The Fermat test

    The \\(\Theta(\log{n})\\) primality test is based on the result from number theory known as
    the following theorem:
    
    -   **Fermat's Little Theorem:** If \\(n\\) is a prime number and \\(a\\) is any positive integer
        less than \\(n\\), then \\(a\\) raised to the \\(n\\) th power is congruent to \\(a\\) modulo \\(n\\).
    
    \[
    a^n \equiv a \pmod{n}
    \]
    
    And that is equivalent to saying that \\(a^n-a\\) is an integer multiple of \\(n\\).
    
    (Two numbers are said to be *congruent modulo* \\(n\\) if they both have the same 
    remainder when divided by \\(n\\). The remainder of a number \\(a\\) when divided by
    \\(n\\) is also referred to as the *remainder of a modulo* \\(n\\), or simply as
    \\(a\\) *modulo* \\(n\\).)
    
    If \\(n\\) is not prime, the, in general, most of the numbers \\(a<n\\) will not satisfy
    the above relations. This leads to the following algorithm for testing primality:
    Given a number \\(n\\), pick a random number \\(a<n\\) and compute the remainder of \\(a^n\\)
    modulo \\(n\\). If the result is not equal to \\(a\\), then \\(n\\) is certainly not prime.
    If it is \\(a\\), then the chances are good that \\(n\\) is prime. Now pick another random
    number \\(a\\) and test it with the same method. If it also satisfies the equation,
    then we can be even more confident that \\(n\\) is prime. By trying more and more 
    values of \\(a\\), we can increase our confidence in the result. This algorithm is
    known as the Fermat test.
    
    To implement the Fermat test, we need a procedure that computes the exponential
    of a number modulo another number:
    ```scheme
        ;; notice that there are some modulo arithmetic used
        ;; in this procedure
        (define (expmod base exp m)
          (cond ((= exp 0) 1)
                ((even? exp)
                 (remainder (square (expmod base (/ exp 2) m))
                            m))
                (else
                 (remainder (* base (expmod base (- exp 1) m))
                            m))))
    ```
    This is very similar to the `fast-expt` procedure of section 1.2.4. It uses
    successive squaring, so that the number of steps grows logarithmically with
    the exponent.
    
    The Fermat test is performed by choosing at random a number \\(a\\) between \\(1\\) and \\(n-1\\)
    inclusive and checking whether the remainder modulo \\(n\\) of the \\(n\\) th power of \\(a\\)
    is equal to \\(a\\). The random number \\(a\\) is chosen using the procedure `random`, which
    we assume is included as a primitive in Scheme. `Random` returns an non-negative 
    integer less than its integer input. Hence, to obtain a number between \\(1\\) and \\(n-1\\),
    we call `random` with \\(n-1\\) and add 1 to the result:
    ```scheme
        (define (fermat-test n)
          (define (try-it a)
            (= (expmod a n n) a))
          (try-it (+ 1 (random (- n 1)))))
    ```
    The following procedure runs the test a given number of times. Its value is true if the test
    succeeds every time, and false otherwise.
    ```scheme
        (define (fast-prime? n times)
          (cond ((= times 0) true)
                ((fermat-test n) (fast-prime? n (- times 1))) 
                (else false)))
    ```
3.  Probabilistic methods

    The Fermat test differs in character from most familiar algorithms, in which one computes
    an answer that is guaranteed to be correct. Here, the answer obtained is only probably
    correct. More precisely, if \\(n\\) ever fails the Fermat test, we can be certain that \\(n\\)
    is not prime. But the fact that \\(n\\) passes the test, while an extremely strong indication,
    is still not a guarantee that \\(n\\) is prime. What we would like to say is that for any number
    \\(n\\), if we perform the test enough times and find that \\(n\\) always passes the test, then
    the probability of error in our primality test can be made as small as we like.
    
    Unfortunately, this assertion is not quite correct. There do exist numbers that fool
    Fermat test. Such numbers are extremely rare. They are called *Carmichael numbers*, and
    little is known about them other than that they are extremely rare. There are 255 Carmichael
    numbers below 100,000,000. The smallest few are 561, 1105, 1729, 2465, 2821, and 6601. 
    In testing primality of very large numbers chosen at random, the chance of stumbling upon
    a value that fools Fermat test is less than the chance that cosmic radiation will cause
    the computer to make an error in carrying out a "correct" algorithm.
    Considering an algorithm to be inadequate for the first reason but not for the second
    illustrates the difference between mathematics and engineering.
    
    So, Fermat test is quite reliable in practice. There are variations of the Fermat test that
    cannot be fooled. In these tests, as with the Fermat method, one tests the primality of an
    integer \\(n\\) by choosing a random integer \\(a<n\\) and checking some condition that depends
    upon \\(n\\) and \\(a\\). (Exercise 1.28).
    
    On the other hand, in contrast to the Fermat test, one can prove that, for any \\(n\\), the 
    condition does not hold for most of the integers \\(a<n\\) unless \\(n\\) is prime. Thus, if \\(n\\)
    passes the test for some random choice of \\(a\\), the chances are better than even that \\(n\\)
    is prime. If \\(n\\) passes the test for two random choices of \\(a\\), the chances are better 
    than 3 out of 4 that \\(n\\) is prime. By running the test with more and more randomly chosen
    values of \\(a\\), we can make the probability of error as small as we like.
    
    The existence of tests for which one can prove that the chance of error become arbitrarily 
    small has sparked interest in algorithm of this type, which have come to be known as 
    probabilistic algorithms. There is a great deal of research activity in this area, and
    probabilistic algorithm have been fruitfully applied to many fields.
    
    One of the most striking applications of probabilistic prime testing has been to the field
    of cryptography. Although it is now computationally infeasible to factor an arbitrary
    200-digit number, the primality of such a number can be checked in a few seconds with the 
    Fermat test. This fact forms the basis of a technique for constructing "unbreakable codes".
    The resulting RSA algorithm has become a widely used technique for enhancing the security
    of electronic communications.
    
    Because of this and related developments, the study of prime numbers, once considered the 
    epitome of a topic in "pure" mathematics to be studied only for its own sake, now turns
    out to have important practical applications to cryptography, electronic funds transfer,
    and information retrieval.


<a id="orgb83f88e"></a>

## Formulating Abstractions with Higher-Order Procedures

We have seen that procedures are, in effect, abstractions that **describe compound operations on numbers
independent of the particular numbers**.
For example, when we
```scheme
    (define (cube x) (* x x x))
```
we are not talking about the cube of a particular number, but rather about a method for obtaining the cube
of any number. Of course we could get along without ever defining this procedure and never mention `cube`
explicitly. This would place us at a serious disadvantage, forcing us to work always at the level of the
particular operations that happen to be primitives in the language (multiplication, in this case) rather
than higher-level operations. Our programs would be able to compute cubes, but our language would lack
the ability to express the concept of cubing.
**One of the things we should demand from a powerful programming language is the ability to build 
abstractions by assigning names to common patterns and then to work in terms of the abstractions directly.**
Procedures provide this ability. This is why all but the most primitive programming languages include 
mechanisms for defining procedures.

Yet even in numerical processing we will be severely limited in our ability to create abstractions if we are
restricted to procedures whose parameters must be numbers. **Often the same programming pattern will be used
with a number of different procedures.** To express such patterns as concepts, we will need to construct 
procedures that can accept procedures as arguments or return procedures as values. Procedures that 
manipulate procedures are called **higher-order procedures**. This section shows how higher-order procedures
can serve as powerful abstraction mechanisms, vastly increasing the expressive power of our language.


<a id="org1eda602"></a>

### Procedures as Arguments

Consider the following three procedures. The first computes the sum of the integers from \\(a\\) through \\(b\\),
the second computes the sum of cubes of the integers in the given range:
```scheme
    (define (sum-integers a b)
      (if (> a b)
          0
          (+ a (sum-integers (+ a 1) b))))
    
    (define (sum-cubes a b)
      (if (> a b)
          0
          (+ (cube a) (sum-cubes (+ a 1) b))))
```
The third computes the sum of a sequence of terms in the series:
\[
\frac{1}{1\cdot 3} + \frac{1}{5\cdot 7} + \frac{1}{9 \cdot 11} + \cdots
\]
which converges to \\(\pi/8\\) (very slowly):
```scheme
    (define (pi-sum a b)
      (if (> a b)
          0
          (+ (/ 1.0 (* a (+ a 2))) (pi-sum (+ a 4) b))))
```
These three procedures clearly share a common underlying pattern. They are for the most part identical,
differing only in the name of the procedure, the function of \\(a\\) used to compute the term to be added,
and the function that provides the next value of \\(a\\). We could generate each of the procedures by filling
in slots in the same template:
```scheme
    (define (<name> a b)
      (if (> a b)
          0
          (+ (<term> a)
             (<name> (<next> a) b))))
```
This is actually the summation of a series:
\[
\sum_{n=a}^b f(n) = f(a) + \cdots + f(b)
\]

The power of sigma notation is that it allows mathematicians to deal with the concept of summation
itself rather than only with particular sums.

Similarly, as program designers, we would like our language to be powerful enough so that we can write
a procedures that expresses the particular sums. We can do so readily in our procedural language
by taking the common template shown above and transforming the "slots" into formal parameters:
```scheme
    (define (sum term a next b)
      (if (> a b)
          0
          (+ (term a)
             (sum term (next a) next b))))
```
Notice that the `sum` takes as its arguments the lower and upper bounds \\(a\\) and \\(b\\) together with
the procedures `term` and `next`. We can use `sum` just as we would any procedure.
For example, we can use it (along with a procedure `inc` that increments its argument by 1) to define
`sum-cubes`:
```scheme
    (define (inc n) (+ n 1))
    
    (define (sum-cubes a b)
      (sum cube a inc b))
```
Using this, we can compute the sum of the cubes of the integers from 1 to 10:
```scheme
    (sum-cubes 1 10)
```
With the aid of an identity procedure to compute the term, we can define `sum-integers` in terms of `sum`:
```scheme
    (define (identity x) x)
    (define (sum-integers a b)
      (sum identity a inc b))
```
We can also define `pi-sum` in the same way:
```scheme
    (define (pi-sum a b)
      (define (pi-term x)
        (/ 1.0 (* x (+ x 2))))
      (define (pi-next x)
        (+ x 4))
      (sum pi-term a pi-next b))
```
Once we have `sum`, we can use it as a building block in formulating further concepts.
For instance, the definite integral of a function \\(f\\) between the limits \\(a\\) and \\(b\\) can be approximated
numerically using the formula:

$$
\int_a^b f = \left[f(a+\frac{dx}{2}) + f(a+dx+\frac{dx}{2}) + f(a+2dx+\frac{dx}{2}) + \cdots \right]\,dx
$$

for small values of \\(dx\\).
We can express this directly as a procedure:
```scheme
    (define (integral f a b dx)
      (define (add-dx x) (+ x dx))
      (* (sum f (+ a (/ dx 2)) add-dx b)
         dx))
```

<a id="org2b885d0"></a>

### Constructing Procedures Using `lambda`

In using `sum` as in section 1.3.1, it seems terribly awkward to have to define trivial procedures such
as `pi-term` and `pi-next` just so we can use them as arguments to our higher-order procedure. Rather
than define `pi-next` and `pi-term`, it would be more convenient to have a way to directly specify
"the procedure that returns its input incremented by 4" and "the procedure that returns the reciprocal
of its input times its input plus 2." 
We can do this by introducing the special form `lambda`, which creates procedures. Using `lambda` we can
describe what we want as

```scheme
    (lambda (x) (+ x 4))
```
and

```scheme
    (lambda (x) (/ 1.0 (* x (+ x 2))))
```

Then our `pi-sum` procedure can be expressed without defining any auxiliary procedures as

```scheme
    (define (pi-sum a b)
      (sum (lambda (x) (/ 1.0 (* x (+ x 2))))
           a
           (lambda (x) (+ x 4))
           b))
```
In general, `lambda` is used to create procedures in the same way as `define`, except that no name is
specified for the procedure.
```scheme
    (lambda (<formal-parameters>) <body>)
```
The resulting procedure is just as much a procedure as one that is created using `define`. The only
difference is that it has not been associated with any name in the environment. In fact,
```scheme
    (define (plus 4) (+ x 4))
```
is equivalent to
```scheme
    (define plus (lambda (x) (+ x 4)))
```
We can read the `lambda` expression above as: *the procedure of an argument \\(x\\) that adds \\(x\\) and 4*.

Like any expression that has a procedure as its value, a `lambda` expression can be used as the operator
in a combination such as
```scheme
    ((lambda (x y z) (+ x y (square z))) 1 2 3)
```
or, more generally, in any context where we would normally use a procedure name.


<a id="org0357334"></a>

### Using `let` to create local variables

Another use of `lambda` is in creating local variables. We often need local variables in our procedures 
other than those that have been bound as formal parameters. For example, suppose we wish to compute the
function

\[
f(x,y) = x(1+xy)^2 + y(1-y) + (1+xy)(1-y)
\]

which we could also express as:

$$
\begin{align*}
a &= 1 + xy\\
b &= 1-y \\
f(x, y) &= xa^2 + yb + ab
\end{align*}
$$

In writing a procedure to compute \\(f\\), we would like to include as local variables not only \\(x\\) and \\(y\\)
but also the names of intermediate quantities like \\(a\\) and \\(b\\).

One way to accomplish this is to use an auxiliary procedure to bind the local variables:
```scheme
    (define (f x y)
      (define (f-helper a b)
        (+ (* x (square a))
           (* y b)
           (* a b)))
      (f-helper (+ 1 (* x y))
                (- 1 y)))
```
Of course, we could use a `lambda` expression to specify an anonymous procedure for binding
our local variables. The body of \\(f\\) then become a single call to that procedure:
```scheme
    (define (f x y)
      ((lambda (a b)
         (+ (* x (square a))
            (* y b)
            (* a b)))
       (+ 1 (* x y))
       (- 1 y)))
```
This construct is so useful that there is a special form called `let` to make its use more convenient.
Using `let`, the \\(f\\) procedure could be written as
```scheme
    (define (f x y)
      (let ((a (+ 1 (* x y)))
            (b (- 1 y)))
        (+ (* x (square a))
           (* y b)
           (* a b))))
```
The general form of a `let` expression is
```scheme
    (let ((<var1> <exp1>)
          (<var2> <exp2>)
          ...
          (<varn> <expn>))
      <body>)
```

The first part of the `let` expression is a list of name-expression pairs.
When the `let` is evaluated, each name is associated with the value of the corresponding expression.
The body of the `let` is evaluated with these names bound as local variables. The way this happens is that
the `let` expression is interpreted as an alternate syntax for
```scheme
    ((lambda (<var1> <var2> ... <varn>)
       <body>)
     <exp1>
     <exp2>
     ...
     <expn>)
```

**No new mechanism is required in the interpreter in order to provide local variables. A `let` expression
is simply syntactic sugar for the underlying `lambda` application**

My note: This is fascinating! This way of implementing this local variables is pure functional!

We can see from this equivalence that the scope of a variable specified by a `let` expression is the body
of `let`. This implies that:

-   `Let` allows one to bind variables as locally as possible to where they are to be used. For example,
    if the value of \\(x\\) is 5, the value of the expression:
```scheme
        (define x 5)
        (+ (let ((x 3))
             (+ x (* x 10)))
           x)
```    
    is 38. Here, the \\(x\\) in the body of the `let` is 3, so the value of the let expression is 33. On the
    other hand, the \\(x\\) that is the second argument to the outermost + is still 5.
-   The variables' values are computed outside the `let`. This matters when the expressions that provide
    the values for the local variables depend upon variables having the same names as the local variables
    themselves. For example, if the value of \\(x\\) is 2, the expression:
    ```scheme
        (define x 2)
        (let ((x 3)
              (y (+ x 2)))
          (* x y))
        ;; yields 12
    ```
    
    will have the value 12 because, inside the body of the `let`, \\(x\\) will be 3 and \\(y\\) will be 4 (which
    is the outer \\(x\\) plus 2).

Sometimes we can use internal definitions to get the same effect as with `let`. For example, we could
have defined the procedure \\(f\\) above as
```scheme
    (define (f x y)
      (define a (+ 1 (* x y)))
      (define b (- 1 y))
      (+ (* x (square a))
         (* y b)
         (* a b)))
```
We prefer, however, to use `let` in situations like this and to use internal `define` only for internal
procedures.


<a id="org90b8a60"></a>

### Procedures as General Methods

We introduced compound procedures in section 1.1.4 as a mechanism for abstracting patterns of numerical 
operations so as to make them independent of the particular numbers involved. With higher-order 
procedures, such as the integral procedure of section 1.3.1, we began to see a more powerful kind of
abstraction: **procedures used to express general methods of computation, independent of the particular
functions involved**. In this section we discuss two more elaborate examples&#x2013;general methods for finding
zeros and fixed points of functions&#x2013;and show how these methods can be expressed directly as procedures.

1.  Finding roots of equations by the half-interval method

    The `half-interval` method is a simple but powerful technique for finding roots of an equation 
    \\(f(x) = 0\\), where \\(f\\) is a continuous function.
    
    The idea is that, if we are given points \\(a\\) and \\(b\\) such that \\(f(a)<0 < f(b)\\), then \\(f\\) must have at
    least one zero between \\(a\\) and \\(b\\). To locate a zero, let \\(x\\) be the average of \\(a\\) and \\(b\\) and compute
    \\(f(x)\\). If \\(f(x)>0\\), then \\(f\\) must have a zero between \\(a\\) and \\(x\\). If \\(f(x)<0\\), then \\(f\\) must have a
    zero between \\(x\\) and \\(b\\). Continuing this way, we can identify smaller and smaller intervals on which
    \\(f\\) must have a zero. When we reach a point where the interval is small enough, the process stops.
    Since the interval of uncertainty is reduced by half at each step of the process, the number of steps
    required grows as \\(\Theta (\log (L/T))\\), where \\(L\\) is the length of the original interval and \\(T\\) is the error 
    tolerance (that is, the size of the interval we will consider "small enough"). Here is a procedure that
    implements this strategy:
   ```scheme
        (define (search f neg-point pos-point)
          (let ((mid-point (average neg-point pos-point)))
            (if (close-enough? neg-point pos-point)
                mid-point
                (let ((test-value (f mid-point)))
                  (cond ((positive? test-value)
                         (search f neg-point mid-point))
                        ((negative? test-value)
                         (search f mid-point pos-point))
                        (else mid-point))))))
    ``` 
    We assume that we are initially given the function \\(f\\) together with points at which its values are
    negative and positive. We first compute the midpoint of the two given points. Next we check to see
    if the given interval is small enough, and if so we simply return the midpoint as our answer.
    Otherwise, we compute as a test value the value of \\(f\\) at the midpoint. If the test value is positive,
    then we continue the process with a new interval running from the original negative point to the 
    midpoint. If the test value is negative, we continue with the interval from the midpoint to the positive
    point. Finally, there is the possibility that the test value is 0, in which case the midpoint is itself
    the root we are looking for.
    
    To test whether the endpoints are "close enough" we can use a procedure similar to the one used in
    section 1.1.7 for computing square roots:
    ```scheme
        (define (close-enough? x y)
          (< (abs (- x y)) 0.001))
    ```
    The appropriate tolerance for a real calculation depends upon the problem to be solved and the 
    limitations of the computer and the algorithm. This is often a very subtle consideration, requiring
    help from a numerical analyst or some other kind of magician.
    
    `Search` is awkward to use directly, because we can accidentally give it points at which \\(f\\)'s values
    do not have the required sign, in which case we get a wrong answer. Instead we will use `search`
    via the following procedure, which checks to see which of the endpoints has a negative function value
    and which has a positive function value, and calls the `search` procedure accordingly. If the function
    has the same sign on the two given points, the half-interval method cannot be used, in which case 
    the procedure signals error:
    ```scheme
        (define (half-interval-method f a b)
          (let ((a-value (f a))
                (b-value (f b)))
            (cond ((and (negative? a-value) (positive? b-value))
                   (search f a b))
                  ((and (positive? a-value) (negative? b-value))
                   (search f b a))
                  (else
                   (error "Values are not of opposite sign" a b)))))
    ```
    Usage:
    ```scheme
        (half-interval-method sin 2.0 4.0)
        ;; yields 3.14111328125
        (half-interval-method (lambda (x) (- (* x x x) (* 2 x) 3))
                              1.0
                              2.0)
        ;; yields 1.89306640625
    ```
2.  Finding fixed points of functions

    A number \\(x\\) is called a *fixed point* of a function \\(f\\) if \\(x\\) satisfies the equation \\(f(x)=x\\).
    For some functions \\(f\\) we can locate a fixed point by beginning with an initial guess and applying
    \\(f\\) repeatedly,
    \[
    f(x), f(f(x)), f(f(f(x))), \ldots
    \]
    until the value does not change very much. Using this idea, we can devise a procedure `fixed-point`
    that takes as inputs a function and an initial guess and produces an approximation to a fixed point of
    the function. We apply the function repeatedly until we find two successive values whose difference
    is less than some prescribed tolerance.
    ```scheme
        (define tolerance 0.00001)
        (define (fixed-point f first-guess)
          (define (close-enough? v1 v2)
            (< (abs (- v1 v2)) tolerance))
          (define (try guess)
            (let ((next (f guess)))
              (if (close-enough? guess next)
                  next
                  (try next))))
          (try first-guess))
    ```
    For example, we can use this method to approximate the fixed point of the cosine function, starting
    with 1 as an initial approximation:
    ```scheme
        (fixed-point cos 1.0)
        ;; yields .739082298522403
    ```
    Similarly, we can find a solution to the equation \\(y= \sin{y} + \cos{y}\\)
    ```scheme
          (fixed-point (lambda (y) (+ (sin y) (+ cos y)))
                       1.0)
        ;; yields 1.2587315962971173
    ```
    The fixed-point process is reminiscent of the process we used for finding square roots in section 1.1.7.
    Both are based on the idea of repeatedly improving a guess until the result satisfies some criterion.
    
    In fact, we can readily formulate the square-root computation as a fixed point search. Computing the 
    square root of some number \\(x\\) requires finding a \\(y\\) such that \\(y^2 = x\\). Putting this equation into
    the equivalent form \\(y = x / y\\), we recognize that we are looking for a fixed point of the function 
    \\(y \mapsto x/y\\), and we can therefore try to compute square roots as
    ```scheme
        (define (sqrt x)
          (fixed-point (lambda (y) (/ x y))
                       1.0))
    ```
    \\(\mapsto\\) (pronounced as "maps to") is the mathematician's way of writing `lambda`. \\(y \mapsto x/y\\) means
    `(lambda (y) (/ x y))`, that is, the function whose value at \\(y\\) is \\(x/y\\).
    
    Unfortunately, this fixed-point search does not converge. It oscillates. Consider an initial guess
    \\(y_1\\). The next guess is \\(y_2 = x/y_1\\) and the next guess is \\(y_3 = x / y_2 = x / (x/y_1) = y_1\\). This 
    results in an infinite loop in which the two guesses \\(y_1\\) and \\(y_2\\) repeat over and over, oscillating
    about the answer.
    
    One way to control such oscillations is to prevent the guess from changing to much. Since the answer is
    always between our guess \\(y\\) and \\(x/y\\), we can make a new guess that is not as far from \\(y\\) as \\(x/y\\) by
    averaging them, so that the next guess is \\((y + x/y)/2\\) instead of \\(x/y\\). The process of making such
    a sequence of guesses is simply the process of looking for a fixed point of \\(y\mapsto \frac{1}{2}(y+x/y)\\):
    ```scheme
        (define (sqrt x)
          (fixed-point (lambda (y) (average y (/ x y)))
                       1.0))
    ```
    (Note that \\(y = \frac{1}{2} (y+x/y)\\) is a simple transformation of the equation \\(y = x/y\\); to derive it, add \\(y\\) to 
    both sides of the equation and divide by 2.)
    
    With this modification, the square-root procedure works. In fact, if we unravel the definitions, we
    can see that the sequence of approximations to the square root generated here is precisely the same 
    as the one generated by our original square-root procedure of section 1.1.7. This approach of 
    **averaging successive approximations to a solution, a technique we call average damping**, often aids
    the convergence of fixed-point searches.


<a id="org1e0a253"></a>

### Procedures as Returned Values

The above examples demonstrate how the ability to pass procedures as arguments significantly enhances
the expressive power of our programming language. We can achieve even more expressive power by creating
procedures whose returned values are themselves procedures.

We can illustrate this idea by looking again at the fixed-point example described at the end of 
section 1.3.3. We formulated a new version of the square-root procedure as a fixed-point search, starting
with the observation that \\(\sqrt{x}\\) is a fixed-point of the function \\(y \mapsto x/y\\). Then we used 
average damping to make the approximations converge. Average damping is useful general technique itself.
Namely, given a function \\(f\\), we consider the function whose value at \\(x\\) is equal to the average of
\\(x\\) and \\(f(x)\\).

We can express the idea of average damping by means of the following procedure:
```scheme
    (define (average-damp f)
      (lambda (x) (average x (f x))))
```
`average-damp` is a procedure that takes as its argument a procedure `f` and returns as its value a
procedure (produced by the `lambda`) that, when applied to a number `x`, produces the average of 
`x` and `(f x)`. For example, applying `average-damp` to the `square` procedure produces a procedure
whose value at some number \\(x\\) is the average of \\(x\\) and \\(x^2\\). Applying this resulting procedure
to 10 returns the average of 10 and 100, or 55.
```scheme
    ((average-damp square) 10) ;; yields 55
```
Using `average-damp`, we can reformulate the square-root procedure as follows:
```scheme
    (define (sqrt x)
      (fixed-point (average-damp (lambda (y) (/ x y)))
                   1.0))
```
Notice how this formulation makes explicit the three ideas in the method:

1.  Fixed-point search
2.  Average damping
3.  The function \\(y \mapsto x/y\\).

It is instructive to compare this formulation of the square-root method with the original version given
in section 1.1.7. Bear in mind that these procedures express the same process, and notice how much clearer
the idea becomes when we express the process in terms of these abstractions.

In general, there are many ways to formulate a process as a procedure. Experienced programmers know
how to choose procedural formulations that are particularly perspicuous, and where useful elements of the
procedure are exposed as separate entities that can be reused in other applications.

As simple example of reuse, notice that the cube root of \\(x\\) is a fixed-point of the function 
\\(y\mapsto x/y^2\\), so we can immediately generalize our square-root procedure to one that extract cube roots:
```scheme
    (define (cube-root x)
      (fixed-point (average-damp (lambda (y) (/ x (square y))))
                   1.0))
```

1. Newton's Method

    When we first introduced the square-root procedure, in section 1.1.7, we mentioned that this was a 
    special case of Newton's method. If \\(x\mapsto g(x)\\) is a differentiable function, then a solution of 
    the equation \\(g(x) = 0\\) is a fixed-point of the function \\(x \mapsto f(x)\\) where:
   
   \[
    f(x) = x - \frac{g(x)}{Dg(x)}
    \]
   
   and \\(Dg(x)\\) is the derivative of \\(g\\) evaluated at \\(x\\). 
    
    Newton's method is the use of the fixed-point method we saw above to approximate a solution of the 
    equation by finding a fixed point of the function \\(f\\).
    
    My Note: How is the square-root a special case? In the square root example, let \\(n\\) is the number
    which we want to find a square root for. Then we want to find a \\(x\\) that \\(x^2=n\\). So \\(g(x) = x^2-n\\).
    The fixed point we are looking for is \\(g(x) = x^2 - n = 0\\).
    The \\(f(x)\\) is:
    
    $$
    \begin{align*}
    f(x) &= x - \frac{x^2 - n}{2x}\\
    &=x - \frac{x}{2} + \frac{n}{2x}\\
    &= \frac{x}{2} + \frac{n}{2x}\\
    &= \frac{1}{2}(x + \frac{n}{x})
    \end{align*}
    $$
    
    To make this more clear, the \\(x\\) here is the \\(y\\) we used before, the answer of square root. The \\(n\\)
    is the \\(x\\) we used before, which is the number we want to find a square root for. 
    
    For many functions \\(g\\) and for sufficiently good initial guesses for \\(x\\), Newton's method converges
    very rapidly to a solution of \\(g(x) = 0\\).
    
    (Newton's method doesn't always converge to an answer, but it can be shown that in favorable cases each
    iteration doubles the number-of-digits accuracy of the approximation to the solution. In such cases,
    Newton's method will converge much more rapidly than the half-interval method.)
    
    In order to implement Newton's method as a procedure, we must first express the idea of derivative. Note
    that "derivative," like average damping, is **something that transforms a function into another function.** For instance, the derivative of the function \\(x \mapsto x^3\\) is the function \\(x \mapsto 3x^2\\).
    In general, if \\(g\\) is a function and \\(dx\\) is a small number, then the derivative \\(Dg\\) of \\(g\\) is the 
    function whose value at any number \\(x\\) is given (in the limit of small \\(dx\\)) by:
    
    \[
    Dg(x) = \frac{g(x+dx) - g(x)}{dx}
    \]
    
    Thus, we can express the idea of derivative (taking \\(dx\\) to be, say, 0.00001) as the procedure
    ```scheme
        (define (deriv g)
          (lambda (x)
            (/ (- (g (+ x dx)) (g x))
               dx)))
        
        (define dx 0.00001)
    ```
    Like `average-damp`, `deriv` is a procedure that takes a procedure as argument and returns a procedure
    as value. For example, to approximate the derivative of \\(x\mapsto x^3\\) at 5 (whose exact value is 75)
    we can evaluate
    ```scheme
          (define (cube x) (* x x x))
        
        ((deriv cube) 5)
        ;; yields 75.00014999664018
    ```
    With the aid of `deriv`, we can express Newton's method as a fixed-point process:
    ```scheme
        (define (newton-transform g)
          (lambda (x)
            (- x (/ (g x) ((deriv g) x)))))
        
        (define (newtons-method g guess)
          (fixed-point (newton-transform g) guess))
    ```
    
    The `newton-transform` procedure expresses the formula at the beginning of this section, and
    `newtons-method` is readily defined in terms of this. It takes arguments a procedure that computes
    the function for which we want to find a zero, together with an initial guess. For instance,
    to find the square root of \\(x\\), we can use Newton's method to find a zero of the function
    \\(y \mapsto y^2-x\\) starting with an initial value of 1.0. This provides yet another form of the 
    square-root procedure:
    ```scheme
        (define (sqrt x)
          (newtons-method (lambda (y) (- (square y) x))
                          1.0))
    ```
2.  Abstractions and first-class procedures

    We've seen two ways to express the square-root computation as an instance of a more general method, 
    once as a fixed-point search and once using Newton's method. Since Newton's method was itself
    expressed as a fixed-point process, we actually saw two ways to compute square roots as fixed points.
    
    **Each method begins with a function and finds a fixed point of some transformation of the function**.
    We can express this general idea itself as a procedure:
    ```scheme
        (define (fixed-point-of-transform g transform guess)
          (fixed-point (transform g) guess))
    ```
    This very general procedure takes as its arguments a procedure `g` that computes some function,
    a function that transforms `g`, and an initial guess. The returned result is a fixed point of the 
    transformed function.
    
    Using this abstraction, we can recast the first square-root computation from this section (where we 
    look for a fixed point of the average-damped version of \\(y\mapsto x/y\\)) as an instance of this 
    general method:
    ```scheme
        (define (sqrt x)
          (fixed-point-of-transform (lambda (y) (/ x y))
                                    average-damp
                                    1.0))
    ```
    Similarly, we can express the second square-root computation from this section (an instance of
    Newton's method that finds a fixed point of the Newton transform \\(y\mapsto y^2 - x\\)) as
    ```scheme
        (define (sqrt x)
          (fixed-point-of-transform (lambda (y) (- (square y) x))
                                    newton-transform
                                    1.0))
    ```
    We began section 1.3 with the observation that compound procedures are a crucial abstraction mechanism,
    because they permit us to express general methods of computing as explicit elements in our programming
    language. Now we've seen how higher-order procedures permit us to manipulate these general methods
    to create further abstractions.
    
    **As programmers, we should be alert to opportunities to identify the underlying abstractions in our 
    programs and to build upon them and generalize them to create more powerful abstractions.**
    This is not to say that one should always write programs in the most abstract way possible;
    expert programmers know how to choose the level of abstraction appropriate to their task.
    But it is important to be able to think in terms of these abstractions, so that we can be ready to apply
    them in new contexts.
    
    The significance of higher-order procedures is that they enable us to represent these abstractions 
    explicitly as elements in our programming language, so that they can be handled just like other
    computational elements.
    
    In general, programming languages impose restrictions on the ways in which computational elements can
    be manipulated. Elements with the fewest restrictions are said to have *first-class* status.
    
    Some of the "rights and privileges" of first-class elements are:
    
    -   They may be named by variables.
    -   They may be passed as arguments to procedures.
    -   They may be returned as the results of procedures.
    -   They may be included in data structures.
    
    **Lisp, unlike other programming languages, awards procedures full first-class status. This poses 
    challenges for efficient implementation, but the resulting gain in expressive power is enormous.**
    (The major implementation cost of first-class procedures is that allowing procedures to be returned
    as values requires reserving storage for a procedure's free variables even while the procedure is not
    executing.)

