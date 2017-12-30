# Anomaly Detection Using Layered Networks Based on Eigen Co-occurrence Matrix Note

<!-- TOC -->

- [Insight](#insight)
- [Goal](#goal)
- [Algorithm](#algorithm)
- [Reference](#reference)

<!-- /TOC -->

## Insight
* Hard to accurately model user behavior
    * Dynamic usesr's behabvior
    * Difficult to capture completely
* model user behavior
    * Feature vectorby
        * Histogram
            * no sequence
        * N-grams
            * n-connected sequence
        * Correlation between unadjacent events
    * Network model
        * Automaton
            * Require well-defined rules
            * Various contexts not have well-defined rules
        * Bayesian network
            * Bayesian network indicates the direction of causality between the corresponding variables
            * Topology must be predefined
        * Hidden Markov Model (HMM)
            *  Hard to build an adequate topology
* Assumption
    * The dynamic behavior of a user appearing in a sequence can be captured by correlating not only connected events but also events that are not adjacent to each other while appearing within a certain distance (non-connected events)
* ECM, Eigen co-occurrence matrices
    * Inspired by the Eigenface technique
    * Three main components:
        * modeling of the dynamic features of a sequence
        * extraction of the principal features of the resulting model
        * automatic construction of a layered network from the extracted principal features

## Goal
* Eigen co-occurrence matrix (ECM)
* For dynamic user behavior

## Algorithm

* Handwritten Notes :)

## Reference

* Anomaly Detection Using Layered Networks Based on Eigen Co-occurrence Matrix
* Anomaly Detection Using Integration Model of Vector Space and Network Representation
