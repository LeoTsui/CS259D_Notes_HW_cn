# Data Streaming Algorithms for Estimating Entropy of Network Traffic

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal](#goal)
- [Data Source](#data-source)
- [Formulation](#formulation)
    - [Notation](#notation)
    - [Relative Error](#relative-error)
    - [Approximation Algorithm](#approximation-algorithm)
- [Lower Bound](#lower-bound)
- [Algorithm](#algorithm)
    - [Streaming Algorithm](#streaming-algorithm)
    - [Sieving Algorithm](#sieving-algorithm)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Network traffic flow analysis
    * Volume-based analysis
    * Distribution-based analysis
        * More succinct
        * Requires appropriate metrics to encapsulate and capture features
* Entropy of distribution
    * Moment Method
        * the mean, standard deviation, skewness, kurtosis, etc.
    * Two significant benefits
        * Increase sensitivity
        * Additional diagnostic information
    * Offer distancd information
* Hard Task
    * Processing capacity
    * Memory
        * Impossible to processing per flow
    * Sampling
        * Trade off accuracy for efficiency
* **Estimating the entropy shares structural similarity with estimating the frequency moments**
* **Separating the high-frequency (elephant) flows from the  low-frequency (mice) flows**

## Goal

* Data streaming algorithm
    * Compute the entropy
    * Lightweight
        * Memory
        * Computational complexity
* First algorithm
    * Estimating the entropy shares structural similarity with estimating the frequency moments
    * Identification of appropriate estimator functions for calculating the entropy accurately
    * Providing proofs of approximation guarantees and resource usage
* Second algorithm
    * Build on the base streaming algorithm
    * Separating the large (elephant) flows from the small (mice) flows

## Data Source

| Trace                      | Period | Bins         | TCP packets / bin | Distinct IP / bin | Ports / bin |
| -------------------------- | ------ | ------------ | ----------------- | ----------------- | ----------- |
| University Trace (Trace 1) | 1 Hour | 1 min epochs | 1.7M              | 30267             | 15165       |
| Department Trace (Trace 2) | 5 Hour | 5 min epochs | 0.5M              | 2587              | 4672        |
| University Trace (Trace 3) | 1 Hour | 1 min epochs | 2.5M              | 25565             | 8080        |

## Formulation

### Notation

* $$[n]=\{1,2,3,...,n\}$$, set of all items come over the stream
    * $$n$$ can be various when measuring different features
        * Port
        * Source or destination addresses
* $$m_i$$, the frequency of item $$i \in [n]$$
* $$m$$, the total number of items in the stream
    * $$m = \sum_{i=1}^n m_i$$
* $$a_j \in [n]$$, the $$j\text{th}$$ item obersved in the stream
* $$n_0$$, $$\#$$ distince items appeared in the stream
    * not all $$n$$ items are present
* $$n \gg m$$
* $$H \equiv - \sum_{i=1}^n \frac{m_i}{m} \log \frac{m_i}{m}$$, entropy(sample entropy)
$$H = \log m - \frac{1}{m} \sum_i m_i \log m_i$$

### Relative Error

* $$S \equiv \sum_i m_i \log m_i$$
$$H = \log m - \frac{S}{m}$$
* $$\vert S - \tilde{S} \vert / S$$
    * $$S$$, true value
    * $$\tilde{S}$$, estimated value
* $$\tilde{H} = \log m - \tilde{S} / m$$, estimated value of $$H$$
* $$S$$ with relative error at most $$\epsilon$$
    * $$(1-\epsilon)S \le \tilde{S} \le (1+\epsilon)S$$
$$\frac{\vert H - \tilde{H} \vert}{H} \le \epsilon \frac{S}{H m} $$

### Approximation Algorithm

* $$(\epsilon, \delta)$$-Approximation Algorithm
    * $$X$$, real values
    * $$\tilde{X}$$, estimated values
    * Given a relative error of at most $$\epsilon$$ with probability at least $$1-\delta$$ 
    * s.t.
$$P(\vert X - \tilde{X} \vert \le X \epsilon) \ge 1-\delta$$

## Lower Bound

* $$H \le \alpha \log m$$
$$O(\log m)$$
* Observed errors on the traffic traces are much smaller than the theoretical guarantees
    * Alogrithm must guarantee the error bound for any stream with any distribution. 
    * Real packet traces have considerable underlying structure


## Algorithm

### Streaming Algorithm

* Alon–Matias–Szegedy frequency moment estimation algorithm
* Three stages
    1. Pre-processing stage
        * Selsect random locations
        * Decide counters
    2.  Online stage
        * Keep an exact counter for the number of subsequent occurrences of that item
            * Select position $$k$$
            * Keep exact counter, $$\alpha _k$$, for the items' location between position $$k$$ and $$m$$
    3. Post-processing stage
        * Use various counters to obtain an estimator for the $$S$$ value for the stream

### Sieving Algorithm

* Online stage
    * Sieve the elephants from the mice
        * Threshold = 2
* Post-processing stage
    * $$S_\text{elephant} + S_\text{mice}$$

## Reference

* Lall, et all 2006. Data Streaming Algorithms for Estimating Entropy of Network Traffic
