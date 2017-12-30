# BotFinder: Finding Bots in Network Traffic Without Deep Packet Inspection

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal and Contribution](#goal-and-contribution)
- [DATA](#data)
- [BotFinder](#botfinder)
    - [Notation](#notation)
    - [Input Data Processing](#input-data-processing)
    - [Flow Reassembly](#flow-reassembly)
    - [Trace Extraction](#trace-extraction)
    - [Statistical Features Analysis](#statistical-features-analysis)
    - [Model Creation (Training)](#model-creation-training)
    - [Malware Detection](#malware-detection)
- [Bot Evolution](#bot-evolution)
    - [Botnet's strategy](#botnets-strategy)
    - [Cost of botnet](#cost-of-botnet)
    - [Fail case](#fail-case)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Three core design goals
    * Able to detect individual bot infections
    * Only rely on high-level/network-flow information
        * Not inspect payloads
        * Resilient to the encrypted bot communication
        * Easy to obtain
    * Work for stealthy bots
        * Steal sensitive data
        * Not rely on noisy activity
            * Spamming
            * DoS
* Observation
    * C&C connections follow regular patterns
    * Bots send similar traffic to C&C
    * Upload information to C&C in similar way
    * Timing patterns of communications with C&C

## Goal and Contribution

* Observe that C&C traffic of different bot families exhibits regularities
* A learning-based approach that automatically generates bot detection models
* Prototype of BotFinder
    * A system that detects individual, bot-infected machines by monitoring their network traffic

## DATA

* Pick active malware samples in Anubis within a window of 30 days in June 2011
* Average 32 samples, for each bot family

## BotFinder

![General architecture of BotFinder](images/BotFinder.png)

### Notation

* $$\tau$$, trace, a sequence of chronologically-ordered flows between two network endpoints
* $$M$$, model
* $$\tau_M$$, score of trace $$\tau$$ on model $$M$$
* $$q_{cluster}$$, quality rate

### Input Data Processing

* All network traffic
* Scanned by
    * VirusTotal
    * Anubis
* Tolerate noise

### Flow Reassembly

* Aggregate data according to NetFlow

### Trace Extraction

![Traces with different statistical behaviors](images/Traces_BotFinder.png)

* Two ways to filter the traffic and identify the relevant traffic traces
    * Whitelist
    * Third-party knowledge

### Statistical Features Analysis

* Average time between the start times of two subsequent flows in the trace
    * Botmaster has to ensure that all bots under his control
    * Communication not following a push model
        * Hosts are behind NAT boxes
        * Not registered with C&C
    * Connect under a constant time interval, and exhibit loosely periodicity
* Average duration of a connection
    * Expect the durations are similar
* Number of bytes on average transferred to the source
* Number of bytes on average transferred to the destination
* Fast Fourier Transformation(FFT) over the flow start times
    * Identify underlying frequencies of communication

### Model Creation (Training)

* Cluster each feature separately
    * Malware features uncorrelated
* Drop small clusters with more diverse data(lower clustering quality)
* CLUES algorithm
    * Allow non-parametric clustering
    * Better than k-means
* Cluster quality
    * Large clusters , highly similar values
    * $$q_{cluster} = exp(-\beta \frac{SD}{Avg})$$
        * $$\beta = 2.5$$ (empirical value)

### Malware Detection

* Match each feature of the trace against the corresponding model's cluster
* $$\tau$$ hit one feature of $$M$$
* Add $$q_{cluster} \cdot exp(-\beta \frac{SD_{trace}}{Avg_{trace}})$$ to $$\tau_M$$
    * $$\beta = 2.5$$ (empirical value)
* Maintain a $$\tau_M$$ for each model
* Compare highest $$\tau_M$$ with threshold $$a$$
* Minimal number of feature hits, $$h$$

## Bot Evolution

### Botnet's strategy

* Adding Randomness
    * BotFinder's detection rate remains 60%, when 100% randomization
* Introducing Larger Gaps
    * Fast Fourier Transformation
* High Fluctuation of C&C Servers
    * BotFinder cannot build traces of minimal length $$|\tau|_{min} = 50$$
        * Not observe such high C&C server fluctuations (IP flux)
    * Additional pre-processing step before Step 4
        * Merge two sub-traces $$\tau_A$$ and $$\tau_B$$
        * Two factors
            * $$SD$$ of $$\tau_{AB}$$ is lower than the $$SD$$ of at least one of the individual traces
            * $$q_{cluster}$$ of $$\tau_{AB}$$ higher than a threshold
* P2P Bots
* Bot-like Benign Traffic

### Cost of botnet

* C&C and bots need to update Botnet Communication Topologies
* Increase the botnet operator's costs and reduce stability and performance of the malware network

### Fail case

* Significantly randomize the bot's communication pattern
* Drastically increase the communication intervals to force BotFinder to capture traces over longer periods of time
* Introduce overhead traffic for source and destination byte variation
* Change the C&C server extremely frequently
* Use completely different traffic patterns after each C&C server change


## Reference
* BotFinder: Finding Bots in Network Traffic Without Deep Packet Inspection
* CS 259D Lecture 2
