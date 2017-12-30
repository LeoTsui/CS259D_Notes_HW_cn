# BotMiner: Clustering Analysis of Network Traffic for Protocol- and Structure-Independent Botnet Detection

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal and Contribution](#goal-and-contribution)
- [BotMiner](#botminer)
    - [C-plane Monitor](#c-plane-monitor)
    - [A-plane Monitor](#a-plane-monitor)
    - [C-plane Clustering](#c-plane-clustering)
    - [A-plane Clustering](#a-plane-clustering)
    - [Cross-plane Correlation](#cross-plane-correlation)
- [Limitations and Potential Solutions](#limitations-and-potential-solutions)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Bot
    * A malware instance that runs autonomously and automatically on a compromised computer (zombie) without owner's consent
* Botnet (Bot Army)
    * Network of bots controlled by criminals
    * A coordinated group of malware instances that are controlled by a botmaster via some C&C channel
* Evolving and quite flexible
* Bots communicate with C&C servers/peers
    * Centralized
        * IRC
            * 53% of botnet activity related to scan
                * For DDoS or spreading
            * 14.4% related to binary downloading
        * HTTP
            * Mostly for sending spam
    * Distributed
        * P2P
            * Mostly for sending spam
* Bots act a similar/correlated way
    * Otherwise, just a group of unrelated/isolated infections
    * Bots are non-human driven, programmed to perform C&C logic/communications


## Goal and Contribution

* Grounded on the definition and essential properties, no a priori knowledge
* A new "aggregated communication flow" (C-flow)
* BotMiner

## BotMiner

![Architecture overview of our BotMiner detection framework](images/BotMiner.png)

* Assume
    * Bots within the same botnet will be characterized by similar malicious activities and similar C&C communication patterns
* Detection method
    * Cluster similar communication traffic
        * Who is talking to whom
        * C-plane (C&C communication traffic)
    * Cluster similar malicious traffic
        * Who is doing what
        * A-plane (Activity traffic)
    * Perform cross-cluster correlation
        * Find a coordinated group pattern
* Priori knowledge
    * Botnet's protocol
    * Captured bot binaries (botnet signatures)
    * C&C server names/addresses
    * Content of the C&C communication
* Objectives
    * Independent of the protocol and structure
    * Resistant to changing location
    * Independent of the content of communication
    * Low false positive
    * Low false negative
    * Efficient

### C-plane Monitor

* **Who is talking to whom**
* Limit to TCP and UDP
* Each flow record contains
    * Time
    * Duration
    * Source IP
    * Source port
    * Destination IP
    * Destination port
    * \# packets, both directions
    * Bytes, both directions

### A-plane Monitor

* **Who is doing what**
* Analyzes outbound traffic
* Based on Snort, with some modifications
* Detects several types of malicious activities
    * Scanning
        * SCADE (Statistical sCan Anomaly Detection Engine)
        * Two anomaly detection modules
            * Abnormally-high scan rate
            * Weighted failed connection rate
    * Spamming
        * Developed a new Snort plug-in
    * PE (Portable Executable) binary downloading
        * PEHunter
        * BotHunter
            * Egg download detection method
    * Exploit
    * Easily add others
* A-plane monitoring alone is not sufficient for botnet detection purpose
    * A-plane activities are not exclusively used in botnets
    * Loose design will generate a lot of false positives

### C-plane Clustering

![C-plane Clustering](images/C-plane_Clustering.png)

* Basic-filtering (F1, F2)
    * Irrelevant traffic flows (F1)
        * Internal hosts
        * From external hosts to internal hosts=
    * Not completely established (F2)
* White-listing (F3)
    * Flows with well-known destinations
* Given an epoch $$E$$, aggregate into communication flows (C-flows)
    * $$c_i = \{f_j\}_{j=1..m}$$, C-flow
        * Where $$f_j$$ have same protocol (TCP/UDP), source IP, destination IP & port
        * $$m$$, $$\#$$ TCP/UDP flows
* Feature extraction
    * For each C-flow
        * Temporal
            * $$\#$$ flows per hour ($$fph$$)
            * $$\#$$ bytes per second ($$bps$$)
        * Spatial
            * $$\#$$ packets per flow ($$ppf$$)
            * $$\#$$ bytes per packets ($$bpp$$)
    * $$13$$ bins per feature
    * Dimension of features, $$d = 4 \times 13 = 52$$
* Two-step Clustering
    * Clustering C-flows is a challenging task
        * Large networks
        * Large feature space
        * Small percentage of bots
    * X-means, a variant of k-means
        * Not require the user to choose the number of clusters
    * Feature reduction
        * $$d = 52$$ to $$d' = 8$$ 
        * $$\{Avg, SD\} \times \{fph, ppf, bpp, bps\}$$
    * First-step Coarse grained clustering on entire dataset, $$d' = 8$$
    * Second-step Fine-grained clustering on multiple smaller clusters using all features, $$d = 52$$
* FP and FN can be reduced by A-plane

### A-plane Clustering

![A-plane clustering](images/A-plane_clustering.png)

* Two-layer
    * Cluster according to the activity types
    * Cluster according to the activity features
* Scan activity features
    * Scanning ports
    * Target subnet
* Spam activity features
    * SMTP connection destinations
* Binary download
    * First/random portion/packet of the binary

### Cross-plane Correlation

* Calculate botnet score, $$s(h)$$, for every host $$h$$
* Similarity score between host $$h_i$$ and $$h_j$$
    * Indication function
*  Hierarchical clustering

## Limitations and Potential Solutions

* Evading C-plane Monitoring and Clustering
    * Utilize a legitimate website
        * Maybe not able to hide this secondary URL and the corresponding communications
    * Manipulate their communication patterns
        * Still could be clustered together just like P2P
    * Randomize individual communication patterns
        * Measure the distribution and entropy of communication features
        * Normal user communications may not have such randomized patterns
    * Mimic the communication patterns of normal hosts
    * Covert channels
    * Communication randomization, mimicry attacks and covert channel represent limitations for all traffic-based detection approaches
* Evading A-plane monitoring/clustering
    * Stealthy malicious activities
        * Scan slowly
        * Spam slowly
    * Botmaster commands each bot randomly and individually to perform different task
        * Not likely a botnet
    * Differentiate the bots and avoid commanding bots in the same monitored network the same way
        * Distributed monitors, larger monitored space
    * Use complementary systems like BotHunter
* Evading cross-plane analysis
    * Extremely delayed task
        * Use multiple-day data and cross check back several days
        * Impedes efficiency
        * The bot may be offline or powered off
* Offline system

## Reference

* [BotMiner: Clustering Analysis of Network Traffic for Protocol- and Structure- Independent Botnet Detection](https://www.usenix.org/legacy/event/sec08/tech/full_papers/gu/gu_html/)
* CS 259D Lecture 2
* [botMiner-Security08-slides](http://faculty.cs.tamu.edu/guofei/paper/botMiner-Security08-slides.pdf)
