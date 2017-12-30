# McPAD : A Multiple Classifier System for Accurate Payload-based Anomaly Detection

<!-- TOC -->

- [Goal and Contribution](#goal-and-contribution)
- [Architecture](#architecture)
    - [One-class SVM](#one-class-svm)
    - [Fusion rules](#fusion-rules)
    - [McPAD](#mcpad)
    - [Attacks](#attacks)
- [Data](#data)
- [Limitation](#limitation)
- [Reference](#reference)

<!-- /TOC -->

## Goal and Contribution

* Improve $$2$$-grams

## Architecture

### One-class SVM

* [Introduction to One-class Support Vector Machines](http://rvlasveld.github.io/blog/2013/07/12/introduction-to-one-class-support-vector-machines/)

### Fusion rules

* Combine all different One-Class SVM
* Min, Max, Mean, Product, Majority voting
* Apply for every $$p_i(\mathrm{x}|\omega)$$
* Assume uniform distribution for outliers

### McPAD

![Overview of McPAD](images/McPAD.png)

* Feature Extraction
    * $$2_v$$-grams, $$65536$$ dimensions
        * $$n$$-grams, $$256^n$$ dimensions
        * For $$v = 0$$, $$2$$-gram
        * Size of sliding window, $$v + 2$$
    * No auto way to derive $$2_(v-1)$$-grams, $$2_(v-2)$$-grams from $$2_v$$-grams
        * Not like $$n$$-grams
        * Different $$v$$ cause different structural information about the payload
* Feature Reduction
    * Cluster

### Attacks

* Generic attacks
* Shell-code attacks
* CLET attacks
* PBA attacks

## Data

* Normal
    * DARPA
    * GATECH
* Attacks
    * Public non-polymorphic HTTP attack
    * Create polymorphic HTTP attack
    * Hard to collect a sufficient amount of attack traffic

## Limitation

* [High FP rate](http://www.cse.chalmers.se/edu/course/DAT285B/SLIDESNOTES/NGramPresentation.pptx)

## Reference

* McPAD : A Multiple Classifier System for Accurate Payload-based Anomaly Detection, Perdisci et al, 2009
* CS 259D Lecture 15
