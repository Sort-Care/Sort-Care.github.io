---
layout: single
title: C++ Random Number And Sampling with Specified Probabilities
date: 2018-09-21 10:47 +0800
mathjax: true

excerpt: "C++, Random Number, Sample from distribution"
header:
  teaser: /assets/images/economics.jpg
  overlay_image: /assets/images/economics.jpg
  overlay_filter: 0.5
---
{% include toc title="Contents" %}

# Random Numbers (between 0 and 1)
<span style = "display:block;
	float:left;
	font-family:Georgia;
	font-size: 310%;
	font-weight: bold;
	line-height: 90%;
	margin-right: 6px;
	margin-bottom:-2px;
	margin-top: 7px;">R</span>andom numbers are usually generated with the following code:
	
	
```c++
//include files
#include <stdlib.h>
#include <time.h>


//code for generate random numbers
srand(time(0));
double rnum = ((double)rand()/RAND_MAX);
```

However, in practice this usually doesn't work quite well. Because first, due to the `RAND_MAX` is expand to be `INT_MAX`, which is 32768, the random number generated are too coarse-grained. We can only get numbers like $\frac{1}{32768}$, $\frac{2}{32768}$ and so forth. Second, because the `srand()` function doesn't actually generate random numbers that spans well uniformly between 0 and 1, instead, it often gives numbers that are highly dependent on time, which will make random numbers concentrate at a specific region between 0 and 1 given a specific time.

Instead, we can use the following code to generate random numbers in range $[0, 1)$:
```c++
#include <random>
#include <chrono>


double random_zero_to_one(){
    std::mt19937_64 rng;
    // initialize the random number generator with time-dependent seed
    uint64_t timeSeed = std::chrono::high_resolution_clock::now().time_since_epoch().count();
    std::seed_seq ss{uint32_t(timeSeed & 0xffffffff), uint32_t(timeSeed>>32)};
    rng.seed(ss);
    // initialize a uniform distribution between 0 and 1
    std::uniform_real_distribution<double> unif(0, 1);
    // ready to generate random numbers
    const int nSimulations = 10;
    double currentRandomNumber = unif(rng);
    return currentRandomNumber;
}
```
If we want random numbers between $[1,2)$, we can simply change `unif(0,1)` to `unif(1,2)`.

# Sample from a distribution with specified probabilities
Actually, it doesn't need to be a distribution. It is the same problem if the user specified weights instead of a distribution in the given array. We can normalize the array to make it a distribution first. Then generate a random number in range $[0,1)$, say $r$. Then, for every value in the given distribution, say $D_i$, if $r < D_i$, then return $i$ as the chosen index. Otherwise update $r$ by subtracting $D_i$:
$$
	r \leftarrow r - D_i
$$
Then continue to next index until $r < D_i$ is satisfied.

The code are shown bellow:

```c++
int random_sample(double distribution[],int size){
        //check if the array adds up to 1
    double sum = 0.0;
    REP (i, 0, size-1){
        sum += distribution[i];
    }

    if(sum == 1.0){//valid
        // will generate [0,1)
        double rnum = random_zero_to_one();
        
        REP(i, 0, size-1){
            if (rnum < distribution[i]) return i;
            else{
                rnum -= distribution[i];
            }
        }
    }else{//didn't get a valid distribution
		//return -1 indicate that input not valid
        return(-1);
    }
}
```

