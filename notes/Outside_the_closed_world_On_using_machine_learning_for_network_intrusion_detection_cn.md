# Outside the Closed World: On Using Machine Learning for Network Intrusion Detection
<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Challenges of Using Machine Learning](#challenges-of-using-machine-learning)
    - [Outlier Detection](#outlier-detection)
    - [High Cost of Errors](#high-cost-of-errors)
    - [Semantic Gap, Interpretation of Results](#semantic-gap-interpretation-of-results)
    - [Diversity of Network Traffic](#diversity-of-network-traffic)
    - [Adaptive adversaries](#adaptive-adversaries)
    - [Difficulties with Evaluation](#difficulties-with-evaluation)
- [Recommendations for using Machine Learning](#recommendations-for-using-machine-learning)
    - [Understanding the Threat Model](#understanding-the-threat-model)
    - [Keeping The Scope Narrow](#keeping-the-scope-narrow)
    - [Reducing the Costs](#reducing-the-costs)
    - [Evaluation](#evaluation)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Network Intrusion Detection Systems (NIDS)
    *  Misuse detection
        * _Exact descriptions_ of known bad behavior
    * Anomaly detection
        * Deviations from profiles of normal behavior
        * Without _precisely describe_ activity
        * First proposed in 1987 by Dorothy Denning (Stanford Research Institute)
* Misuse detectors are used more commonly than anomaly detectors
    * Despite lots of research on anomaly detection
    * Despite anomaly detection is suitable for finding _novel_ attacks
    * Despite success of machine learning in many other areas

## Challenges of Using Machine Learning

### Outlier Detection

||Classification|Outlier detection|
|-|-|-|
|Training samples|Many from both classes|Almost all from one class|
|Required quality|Enough to distinguish two classes|Perfect model of normal|

* Premise: Anomaly detection can find **novel** attacks
* Fact: ML is better at finding similar patterns than at finding outliers
    * Example: Recommend similar products; similarity: products purchased together
* Conclusion: ML is better for finding variants of **known** attacks
* Hard to create **a "closed" world**
    * Cover all cases
* Underlying assumptions
    * Malicious activity is anomalous
    * Anomalies correspond to malicious activity
* Do these assumptions hold?
    * Former employee requests authorization code
        * Account revocation bug? Insider threat?
        * Username typo
        * Brute force attack?
        * User changed password, forgot to update script

### High Cost of Errors

||Cost of False Negatives|Cost of False Positives|
|-|-|-|
|Product recommendation|Low: potential missed sales|Low: continue shopping|
|Spam detection|Low: spam finding way to inbox|**High**: missed important email|
|Intrusion detection|**High**: Arbitrary damage|**High**: wasted precious analyst time|


### Semantic Gap, Interpretation of Results

* Network operator needs actionable reports
    * What does the anomaly mean?
    * Abnormal activity vs. Attack
    * Incorporation of site-specific security policies
    * Relation between features of anomaly detection & semantics of environment
* Local security policies
    * Difference between academic and enterprise environment
* Vague security policies
    * eg: Tolerate P2P, "appropriate", "egregiously large"

### Diversity of Network Traffic

* Variability across all layers of the network
    * Even most basic characteristics: bandwidth, duration of connections, application mix
    * Various occurs regularly
    * From packet-level to application-layer-level
* Large bursts of activity
* What is a stable notion of normality?
* Anomalies ≠ Attacks
* One solution: Reduced granularity
    * Example: Time-of-Day, Day-of-Week effects exhibit reliable patterns
    * Pro: More stable
    * Con: Reduced visibility

### Adaptive adversaries

* Adversaries adapt
    * ML assumptions do not necessarily hold
        * I.I.D, stationary distributions, linear separability, etc.
* ML algorithm itself can be an attack target
    * Mistraining, evasion

### Difficulties with Evaluation

* Difficulties of data
    * Data's sensitive nature
        * Personal communications
        * Organization's business secret
        * Nerwork access patterns
    * Lack of appropriate public data
        * DARPA/Lincoln Labs packet traces
        * KDD Cup dataset
        * [Machine-Learning-for-Cyber-Security datasets](https://github.com/wtsxDev/Machine-Learning-for-Cyber-Security#-datasets)
        * [awesome-ml-for-cybersecurity Datasets](https://github.com/jivoi/awesome-ml-for-cybersecurity#table-of-contents)
    * Simulation
        * Capturing characteristics of real data
        * Capturing novel attack detection
    * Anonymization
        * Fear of de-anonymization
        * Removing features of interest to anomaly detection
    * Assemble own datasets
        * Not large enough
            * Network size
            * Datasets size
* Interpreting the results
    * "HTTP traffic of host did not match profile"
    * Contrast with spam detection: Little room for interpretation
* Adversarial setting
    * Contrast with product recommendation: Little incentive to mislead the recommendation system

## Recommendations for using Machine Learning

### Understanding the Threat Model

* What kind of target environment?
    * Academic vs enterprise; small vs large/backbone
* Cost of missed attacks
    * Security demands, other deployed detectors
* Attackers' skills and resources
    * Targeted vs background radiation
* Risk posed by evasion

### Keeping The Scope Narrow

* What are the specific attacks to detect?
* Choose the right tool for the task
    * ML not a silver bullet
    * Common pitfall: Start with intention to use ML or even worse a particular ML tool
    * No Free Lunch Theorem
* Not only mathematical guarantee, suit for domain-specific properties
* Insight into the features' significance and capabilities
* Example: Anomalous payload-based network intrusion detection, Wang-Stolfo 2004
    * Features: Byte frequencies in packet payloads
    * Algorithm: Detect packets with anomalous frequency patterns
    * Assumption: Attack payloads have different payload byte frequencies
    * Question: Where does this assumption come from?
* Example: Anomaly Detection of Web-based Attacks, C. Kruegel and G. Vigna 2003
    * Threat model
        * Web-based attacks using input parameters to web applications 
    * Why anomaly detection
        * Attacks share conceptual similarities, yet different enough in their specifics for signatures 
    * Data
        * Successful GET requests to CGI apps, from web server Access Logs 
    * Features
        * Length of attribute value, Character distribution of attribute value 
    * Why is this feature relevant
        * Length: Buffer overflow needs to send shellcode and padding
        * Character distribution: Directory traversal uses too many "." & "/"

### Reducing the Costs

* Reduce the system's scope
* Classification over outlier detection
* Aggregate or averaging features over suitable intervals
    * Features on different granularity
* Post-process the alerts with the support of additional information
* Provide meta-information to analyst to speed up inspection

### Evaluation

* Working with data
    * Gold Standard
        * Real network traffic
        * Large network
        * Different networks
    * **Not bring the data to the experimenter, bring the experiment to the data**
    * No dataset is perfect
    * Separate training data and testing data
* Understanding results
    * Manually examine FP
    * FN harder to investigate than FP
        * Use a different mechanism to label the input
        * Manually label a subset input
    * Include TP and TF
        * A ML system can produces correct results even learned nothing
    * Causality
    * Require low-latency real-time detection
    * Local environment optimization
* Develop insight into anomaly detection system's capabilities
    * What can/can't it detect? Why?

## Reference

* Outside the closed world: On using machine learning for network intrusion detection, Sommer-Paxson, 2010 
* CS 259D Session 13