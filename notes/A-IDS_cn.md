# Adaptive Intrusion Detection System via Online Machine Learning

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal](#goal)
- [Data Source](#data-source)
- [Feature](#feature)
- [Adaptive Intrusion Detection System (A-IDS)](#adaptive-intrusion-detection-system-a-ids)
    - [Mixing Algorithm](#mixing-algorithm)
        - [Notation](#notation)
        - [Mixing Algorithm](#mixing-algorithm)
- [Experimental](#experimental)
- [Limitation](#limitation)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Diverse network environments
* Dynamic attack types
* Adversarial environment
* IDS performance strongly depends on chosen classifier
    * Misuse IDS
        * Detect known attacks
        * SNORT: signature-based IDS
    * Anomaly IDS
        * Detect novel attacks
        * More FP
    * Decision-Tree-based IDS 
        * Detects well on DoS
        * Fail on user-to-root and remote-to-local attacks
    * **Perform differently in different environments**
    * **No Free Lunch Theorem**
* Combination IDSs
    * High cost
    * Incorrect
    * Majority Voting
    * Hedge/Boosting
        * Online learning framework
        * Chose best IDS in period T
        * Destroy the superiority of the best IDS by changing the attacks all the time
        * All perform badly
* Adaptability of the IDS
    * Adapt to dynamic adversarial environments
    * How to choose the best

## Goal

* New efficient online learning framework
* Adaptive Intrusion Detection System (**A-IDS**)

## Data Source

* ECML-PKDD HTTP 2007
    * 50,000 samples, 20% attacks
        * Attacks or Normal
    * Attacks
        * Cross-Site Scripting
        * SQL
        * Injection
        * LDAP Injection
        * XPATH Injection
        * Path traversal
        * Command Execution
        * SSI attacks
* CSIC HTTP 2010
    * 61,000 samples, 41% attacks
        * Anomalous or Normal
    * Targeted Web APP
        * Real
        * Developed for this purpose
        * E-commerce Web APP
        * Apache server
    * Attacks
        * SQL injection
        * Buffer overflow
        * Information gathering
        * CRLF
        * Injection
        * XSS
        * Server side include
        * Parameter tampering

## Feature

* 30 features
    * length
    * number
    * Max
    * Min
    * Type of header
    * Four types of characters
        * Letters
        * Digits
        * Non-alphanumeric characters
            * Special meanings in programming languages
            * 'special' char
        * Others
    * Entropy
    * Programming languages keywords


## Adaptive Intrusion Detection System (A-IDS)

* Base IDS
    * Base classifiers
        * NaïveBayes
        * BayesNetwork
        * Decision Stump
        * RBFNetwork
    * **No assumptions on selection of baseline classifiers**
    * 1O-folds cross-validation
* *Loss Update*
    * Hedge/Boosting algorithm
* *Mixing Update*
    * Bousquet and Warmuth's algorithm (2002)
    * Quick recovery property of the IDSs to deal with their favorite data
        * Remembering the past average weight vector
* Supervised Framework:
    * Combine results of base IDSs
    * Receive the true label of the current sample
    * Measure losses between IDS outputs and true label
    * Maintain weights for base IDSs

### Mixing Algorithm

#### Notation

* $$T$$, $$\#$$ instances
* $$n$$, $$\#$$ base IDSs
* $$t$$, $$(t=1,...,T)$$, a certain time or trial
* $$x_t$$, a vector of $$n$$ IDS's output
    * A-IDS receives $$x_t$$
    * $$x_t = (x_{t,1}, ..., x_{t,n})$$
        * where $$x_{t, i} \in \{0\text{(normal)}, 1\text{(attack)}\}$$
* $$pred(t)$$, the prediction of A-IDS
* $$y_t$$, $$y_t \in \{0, 1\}$$, the true label of the $$t\text{-th}$$ instance at the time $$t$$
* $$L$$, loss function
    * For the trial $$t$$, and the $$i\text{-th}$$ IDS
    * $$L_{t, i} = (y_t - x_{t, i})^2$$
    * Measure the discrepancy between the true label and the predictions of the base IDSs
* $$v_t$$, the weight vector maintained by the A-IDS
    * For the trial $$t$$, and the $$i\text{-th}$$ IDS
    * $$v_t = (v_{t,1}, v_{t,2}, ..., v_{t,n})$$
    * $$v_{t,i} \geq 0$$
    * $$\sum_{i = 1}^n v_{t,i} = 1$$

#### Mixing Algorithm

* Parameters
    * $$\eta > 0$$, learning rate
    * $$0 \leq \alpha \leq 1$$
    * $$n$$
* Initialization
    * $$v_1 = v_0^m = (1/n, ...,  1/n, ..., 1/n)$$
* For $$t=1 \to T$$
    * Prediction
        * $$\hat y_t = v_t \cdot x_t$$
        * $$pred(t)$$:
            * $$0$$, $$0 \leq \hat y_t \leq 0.5$$
            * $$1$$, $$\hat y_t \geq 0.5$$
    * *Loss Update*
        * Find the best IDS from a pool of $$n$$ base ones
        * $$L_{t, i} = (y_t - x_{t, i})^2$$
        * $$e^{-\eta L_{t,i}}$$, a factor
        * $$v_{t,i}^m = {v_{t,i} e^{-\eta L_{t,i}}} / {\sum_{j=1}^n v_{t,j} e^{-\eta L_{t,j}}}$$
    * *Mixing Update*
        * $$av_t = \frac{1}{t} \sum_{q=0}^{t-1} v_q^m$$, average weight vector 
        * $$v_{t+1} = \alpha av_t + (1-\alpha)v_t^m$$

## Experimental

* Setting (Not Mixing Update)
    * Base classifiers:
        * NaïveBayes
        * BayesNetwork
        * Decision Stump
        * RBFNetwork
    * 1O-folds cross-validation
    * Single intrusion detection cannot detect all types
        * Good performance on special segments
        * Need combination
    * *Loss Update*
        * $$\eta = 0.1$$
    * Not ***Mixing** Update*
        * $$v_{t_1,i} = v_{t,i}^m$$
        * Combination is not help
        * The performance is almost the same as the best IDS
* Combining (A-IDS)
    * Random permutation
    * 1O-folds cross-validation
    * Mixing Algorithm
        * $$\eta = 0.1$$
        * $$\alpha = 0.001$$
        * Uniform Past update
* Expanding (A-ExIDS)

## Limitation

* Double-edge knife of *Lose Update*
    * The best $$v_{t,i}$$($$L_{t,i} = 0$$) controls $$v_{t,i}^m$$
    * Hard to recover if an IDS temporarily performs poorly and then performs well
    * Consequence:
        * Slow adaptation to changes in IDS performances
        * Attackers can change attack patterns all the times
    * *Mixing Update* is used to remember the past average weight vector

## Reference

* Adaptive Intrusion Detection System via Online Learning, 2012
* CS 259D Session 10
