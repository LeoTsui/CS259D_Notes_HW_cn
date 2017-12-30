# EXPOSURE: Finding Malicious Domains Using Passive DNS Analysis

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal and Contribution](#goal-and-contribution)
- [Overview of EXPOSURE](#overview-of-exposure)
- [Collect Training Data](#collect-training-data)
- [Features](#features)
    - [Time-Based Features](#time-based-features)
        - [Change Point Detection (CPD)](#change-point-detection-cpd)
        - [Detecting Similar Daily Behavior](#detecting-similar-daily-behavior)
    - [DNS Answer-Based Features](#dns-answer-based-features)
    - [TTL Value-Based Features](#ttl-value-based-features)
    - [Domain Name-Based Features](#domain-name-based-features)
- [Training](#training)
    - [The Classifier](#the-classifier)
        - [C4.5 decision tree algorithm](#c45-decision-tree-algorithm)
- [Evaluation](#evaluation)
- [Limitation (Evasion)](#limitation-evasion)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Typical Internet attacks
    * Botnet
    * Phishing
* Attachers face same engineering challenges with global enterprises
    * Implement a reliable and flexible server infrastructure
    * Advantage of DNS
        * Change IP
        * Hide critical servers
        * Migrate servers flexibly
        * Server more "fault-tolerant" than IP

## Goal and Contribution

* Detect malicious domains
    * Passive
    * Without prior knowledge
* 15 behavioral features
    * 9 new features

## Overview of EXPOSURE

![Overview of EXPOSURE](images/EXPOSURE.png)

* Five main components
    * Data Collector
    * Feature Attribution
    * Malicious and Benign Domains Collector (Label data)
        * Malicious
        * Benign
    * Learning Module
    * Classifier
        * Classify unlabeled domains

## Collect Training Data

* DNS traffic from the Security Information Exchange (SIE)
    * Response data from authoritative name servers in North America & Europe
    * 2.5 months
    * More than 100 billion DNS queries (1 million queries/minute on average)
    * 4.8 million distinct domain names
    * Filtering
        * Alexa top 1000 (20% reduction)
        * Domains older than 1 year (50% more reduction)
* Malicious domains
    * 3500 domains
    * Types
        * Botnet C&C
        * drive-by-download sites
        * phishing/scam pages
    * Example Sources
        * malwaredomains.com
        * Zeus Block List
* Benign domains
    * 3000 domains
    * Example Source
        * Alexa top 1000

## Features

* F1: Time-based features
    * Short life
    * Daily similarity
    * Repeating patterns
    * Access ratio
* F2: DNS answer-based features
    * $$\#$$ distinct IP addresses
    * $$\#$$ distinct countries
    * $$\#$$ domains IP shared with
    * Reverse DNS query results
* F3: TTL value-based features
    * $$Avg$$ TTL
    * $$SD$$ of TTL
    * $$\#$$ distinct TTL values
    * $$\#$$ TTL changes
    * $$\%$$ usage of specific TTL ranges
* F4: Domain name-based features
    * $$\%$$ of numerical characters
    * $$\%$$ of the length of the LMS

### Time-Based Features

* Insight
    * Malicious domains often increase suddenly and decrease suddenly in the number of requests
* Analyse method
    * Divide period into fixed length intervals
    * Count queries
* Global scope
    * Short life
        * DGA(domain generation algorithm) generate domains
    * Change behavior
* Local scope
    * Main idea
        * Zoom into the life time of a domain
        * Study behavioral characteristics
    * Daily similarity
        * An increase or decrease of request count at same intervals everyday
    * Regularly repeating patterns
        * Instance of change point detection (CPD)
        * $$\#$$ changes
        * $$SD$$ the durations of detected changes
    * Access ratio
        * Idle vs popular

#### Change Point Detection (CPD)

* Operate on time series
* Goal to find points, which data values change abruptly
* Detecting abrupt changes
    * Time series for each domain
        * $$P(t)$$, Request count at hour $$t$$, normalized by max count
        * Iterate time interval $$t = 3600s$$
        * $$P^-(t)$$, Average of past $$8$$ time intervals
        * $$P^+(t)$$, Average of next $$8$$ time intervals
        * $$d(t) = |P^+(t) - P^-(t)|$$
    * Apply Cumulative Sum (CUSUM) algorithm to $$d(t)$$
        * Detect times $$t$$, when $$d(t)$$ is large and is a $$local\_max$$
        * $$CUSUM(t) = Max\{0, CUSUM(t-1) + d(t) - local\_max\}$$
        * Report $$t$$ as change point if: $$CUSUM(t) > cusum\_max$$

#### Detecting Similar Daily Behavior

* Compute distances of all pairs of daily time series
    * Normalized each time series by its $$Avg$$ and $$SD$$
    * Use Euclidian distance
* $$d_{ij}$$, Euclidian distance between $$i^{th}$$ & $$j^{th}$$ days
* $$D$$, $$Avg$$ of all $$d_{ij}$$ values

### DNS Answer-Based Features

* Domains map to multiple IPs, and IPs be shared across different domains
* Number of distinct IP addresses
*    Resolved for a domain during the experiment
* Number of distinct countries
* Number of domains share the IP with
    * Can be large for web hosting providers as well
    * Reduce false positives by looking for reverse DNS query results on Google top 3 search results
* Reverse DNS query results
* Reduce false positives
*    Reverse DNS answer from Google

### TTL Value-Based Features

* TTL(Time To Live): Length of time to cache a DNS response
    * Recommended between 1-5 days
* Insight
    * Sophisticated infrastructure of malicious networks cause frequent TTL changes
    * Setting lower TTL values to the less reliable hosts
* Average TTL
    * High availability systems
        * Low TTL values
        * Round Robin DNS
        * Example: CDNs, Fast Flux botnets
* Standard Deviation of TTL
    * Compromised home computers (dynamic IP) assigned much shorter TTL than compromised servers (static IP)
* Number of distinct TTL values
* Number of TTL change
    * Higher in malicious domains
* Percentage usage of specific TTL ranges
    * Considered ranges: $$[0,1), [1,10), [10,100), [100,300), [300,900), [900, inf)$$
    * Malicious domains peak at $$[0, 100)$$ ranges

### Domain Name-Based Features

* Insight
    * Easy-to-remember names
    * Important for benign services
        * Main purpose of DNS
    * Unimportant for attackers (e.g., DGA-generated)
* Ratio of numerical characters to name length
* Ratio of length of the longest meaningful substring to length of domain name
*    Query name on Google & check # of hits vs a threshold
* Combined to decrease false positives
* Features applied to only second-level domains(SLD)
* Other possible feature: entropy of the domain name
    * DGA-generated names more random than human-generated

## Training

* Training period
    * Initial period of 7 days (for time-based features)
    * Retraining every day

### The Classifier

* J48 decision tree
    * C4.5
* Feature selection
    * Percentage of miss-classified instances

#### C4.5 decision tree algorithm

* Check for base cases
* For each attribute
    * Compute attribute's normalized information gain
* Split over attribute with highest gain
* Recurse
* Normalized information gain = difference in entropy of class values

## Evaluation

* Evaluation of the Detection Rate
    * 216 domains reported by malwareurls.com & present in dataset
    * 5 had less than 20 queries
        * be filtered
    * 211 detected malicious
    * Detection rate: 98%
* Evaluation of the False Positives
    * Filter out domains with < 20 requests in 2.5 months (300,000 domains remaining)
    * 17,686 detected as malicious (5.9%)
    * Unlabeled domains
        * Hard to verify manually
    * Verification
        * Google searches
        * Well-known spam lists
        * Norton Safe Web
        * McAfee Site Advisor
    * False positive rate: 7.9%

## Limitation (Evasion)

* Attackers have prior information about EXPOSURE
* Assign uniform TTL values across all compromised machines
    * Reduces attacker's infrastructure reliability
* Reduce number of DNS lookups of malicious domain
    * Not trivial to implement
    * Reduces attacker's impact
    * Requires high degree of coordination

## Reference

* EXPOSURE: Finding Malicious Domains Using Passive DNS Analysis (2011)
* CS 259D Lecture 3
