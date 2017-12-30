# One-Class Training for Masquerade Detection

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Schonlau Dataset](#schonlau-dataset)
- [Machine learning methods](#machine-learning-methods)
- [Limitation](#limitation)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Modeling user behaviors
* Detect anomalous misbehaviors
* Masquerade problem is a challenging problem
    * False alarms
* Multi-class classification
    * Data from each user as samples from one class
    * Self vs non-self
    * Require retraining as users join/leave organization
    * Non-self samples bias model's view of masquerader
* Single-class classification
    * Builds a profile for user only using that user's data
    * Requires less data
    * Distributed implementation

## Schonlau Dataset

* Unix shall commands of 70 users
    * Collected using Unix acct
    * 50 random users as intrusion targets
    * 20 masquerade users
* 15,000 commands per user
    * Over days or months
    * First 5,000 commands clean
    * Next 10,000 commands randomly injected with 100-command intrusion blocks
    * Blocks of size 100: clean or dirty
* Issues
    * Widely different time periods for different users
    * Different number of login sessions per user
    * Different number (0-24) of intrusion blocks per users
    * User job functions unknown
    * Acct logs commands in the order they finished

## Machine learning methods

* Learning task
    * Identify masqueraders positively, not identify particular user
* One-class or two class
    * One-class Naïve Bayes
    * One-class SVM
* Naïve Bayes Classifier
    * Bayes rule
    * Different commands assumed independent
    * Multi-variate Bernoulli model
        * Total unique commands(Unix), 856
        * Each block as a binary N-dimensional vector
        * Each dimension with Bernoulli model
        * Performs better at small vocabulary sizes
    * Multinomial model
        * Each black as N-dimensional vector
        * Each feature = # of occurrences of command
        * Performs better at large vocabulary sizes
    * One-class Naïve Bayes
        *  Compute $$p(c_i|u)$$ only for user's self profile
        *  For masquerader, assume each command has probability $$1/N$$ (completely random)
            * Makes no assumption about masquerader
        *  Given a block $$d$$, compute: $$p(d|self)/p(d|non-self)$$
        *  Threshold controls false positive vs detection rate
* One-class support vector machine
    * Map data to a high-dimensional feature space
    * Maximally separate data points from origin
    * Allow some outliers, but probability of lying on the wrong side bounded by a parameter

## Limitation

* One-class SVM using binary features performs better than one-class Naïve Bayes and one-class SVM using word count features
* Hard problem, not achieve very high accuracy
* 2-gram features perform worse
* 1-gram & 2-grams may improve performance
* This system(paper) should not be viewed as a single detector
* Need to include command arguments, not only truncated commands

## Reference

* One-class Training for Masquerade Detection (2003)
* CS 259D Lecture 3
