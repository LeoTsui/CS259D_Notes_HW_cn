# PAYL: Anomalous Payload-based Network Intrusion Detection

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal](#goal)
- [Data](#data)
- [Feature](#feature)
- [PAYL (Payload Modeling and Anomaly Detection)](#payl-payload-modeling-and-anomaly-detection)
    - [Length Conditioned n-Gram Payload Model](#length-conditioned-n-gram-payload-model)
    - [Simplified Mahalanobis Distance](#simplified-mahalanobis-distance)
    - [Incremental Learning](#incremental-learning)
    - [Reduced Model Size by Clustering](#reduced-model-size-by-clustering)
    - [Unsupervised learning](#unsupervised-learning)
    - [Z-string](#z-string)
- [Limitation](#limitation)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Available IDS failed on
    * 0-day
    * Slow and stealthy worm propagation

## Goal

* Detect first occurrences of zero-day worms or new malicious codes delivered via network
    * Signatures not effective
    * Slow/stealthy worm propagation can avoid bursts in network traffic flows or probes
    * Requires payload based detection

## Data

* 1999 DARPA IDS dataset
* CUCS dataset
* Smoothing factor = 0.001
* Data units
    * Full packet
    * First 100 bytes of packet
    * Last 100 bytes of packet
    * Full connection
    * First 1000 bytes of connection

## Feature

* Mean and variance of each frequency

## PAYL (Payload Modeling and Anomaly Detection)

* Design criteria and operating objectives
    * Hands-free
    * Generality for any service or system
    * Incremental update to accommodate changing or drifting environments
    * Low FP
    * High bandwidth environments, low latency, efficient real-time operation

### Length Conditioned n-Gram Payload Model

* Cluster streams
    * Port number
        * Proxy for application
            * 21, 22, 80, etc.
    * Packet length range
        * Proxy for type of payload
            * Larger payloads contain media or binary data
    * Direction of stream
        * Inbound
        * Outbound
* Measurement: n-gram frequencies
    * Length $$L$$: frequency $$= \#$$ of occurrences $$/ (L-n+1)$$
    * Use $$n = 1$$: 256 ASCII characters
* $$i$$, observed length bin
* $$j$$, port number
* $$M_{ij}$$, average byte frequency and the standard deviation of each byte’s frequency


### Simplified Mahalanobis Distance

* Simplifications
    * Naïve assumption: Byte frequencies independent
        * Covariance matrix becomes diagonal
    * Replace variance with standard deviation
        * Avoid time-consuming square and square-root computations
    * Add smoothing factor
        * Avoid zero SD and infinite distance
        * Avoid same frequency
        * Reflect statistical confidence of sampled training data
            * larger smoothing factor, less confidence
* $$x$$, feature vector of the new observation
* $$\overline{y}$$, averaged feature vector computed from the training dataset
* $$\overline{\sigma}$$, standard deviation
* $$d(x, \overline{y}) = \sum_{i = 0}^{n - 1}{|x_i - \overline{y_i}| / ({\overline{\sigma} + \alpha})}$$
* Intuitive Explanation
    * **A kind of "normalization" observation examples by "length bin", and summing the results**
    * For each dimension (observed length bin, $$i$$)
        * Measure the Euclidean distance between a sample data and the center point
        * Normalize with smoothed SD
    * Summation

### Incremental Learning

* Adapt to concept Drift
* Use streaming measurements for mean and standard deviation
* $$\overline{x} = \overline{x} + {(x_{N + 1} - \overline{x})} / {(N + 1)}$$
* Store the average of $$x_i^2$$, 256-element array

### Reduced Model Size by Clustering

* Fine-grained model problem:
    * Large total size of model
        * Similar distributions for near lengths
    * Insufficient training data for some lengths
* Solution:
    * Merge neighboring models
        * Manhattan distance
    * Borrow data from neighboring bins
* For lengths not observed in training data
    * Use closest length range
    * Alert on unusual length

### Unsupervised learning

* Assumption: Attacks are rare and their payload distribution is substantially different from normal traffic
* Remove training data noise
    * Apply the learned models to training data
    * Remove anomalous training samples
    * Update models

### Z-string

* Distribution ordered by Zipf law can represent the signature
* Z-strings of payloads from different match each other
    * A worm has appeared

## Limitation

* Curse of dimensionality (Maybe not today)
* Spurious features
* Not robust against adversaries
* No focused scope

## Reference

* Anomalous payload-based network intrusion detection, Wang-Stolfo 2004
* CS 259D Session 12
