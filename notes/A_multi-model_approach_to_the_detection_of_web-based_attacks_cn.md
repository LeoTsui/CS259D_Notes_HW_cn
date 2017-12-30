# A Multi-Model Approach to the Detection of Web-Based Attacks

<!-- TOC -->

- [Insight](#insight)
- [Goal](#goal)
- [Architecture](#architecture)
- [Web-related attack detection](#web-related-attack-detection)
- [Data model](#data-model)
- [Detection models](#detection-models)
    - [Attribute length](#attribute-length)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
    - [Attribute character distribution](#attribute-character-distribution)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
    - [Structural inference](#structural-inference)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
    - [Token finder](#token-finder)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
    - [Attribute presence or absence](#attribute-presence-or-absence)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
    - [Attribute order](#attribute-order)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
    - [Access frequency](#access-frequency)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
    - [Inter-request time delay](#inter-request-time-delay)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
    - [Invocation order](#invocation-order)
        - [User](#user)
        - [Attacker](#attacker)
        - [Learning](#learning)
        - [Detection](#detection)
- [Limitation](#limitation)
- [Reference](#reference)

<!-- /TOC -->

## Insight

* Web servers accessible by outside world
* Web apps developed with security as an afterthought
* Developing ad hoc signatures is a time-intensive and error-prone activity
* What's new
    1. A number of different models
        * Reduce vulnerability
    2. Target specific types of applications
        * More focused analysis

## Goal

* Animaly detextion system, detect web-based attacks
* Unsupervised, learning-based anomaly detection
* Deployed on host

## Architecture

* Input: Web server log files
  * Common Log Format (CLF)
* Analysis: build profiles for apps & active docs
    * Lower error rates than generic profiles
    * Use multiple models
        * Reduce vulnerability to mimicry attacks
* Output: Anomaly score for each web request

## Web-related attack detection

1. Relay on models of the intended behavior of user and application
2. Assume attack patterns is **different** from normal behavior
3. The **defference** can be expressed either quantitatively or qualitatively
4. Interprets deviations from this "normal" behavior as **evidence**


* Misuse-based detection
    * Example: Snort
        * 1037 out of 2464 signatures
    * Hard to keep up-to-date
        * Time-intensive, error-prone, requires significant security expertise
    * Challenge with apps developed in-house
    * Unable to detect un-modeled attacks
* Anomaly-based
    * Applicable to custom-developed web apps
    * Support detection of new attacks
* Classification of intrusion detection

## Data model

* An ordered set $$U=\{u_1,u_2,...,u_m\}$$ of URIs
    * Extract from successful GET requests
    * $$200 \leq \text{return-code} < 300$$
* Components of $$u_i$$
    * Path to desired resource: pathi
    * Optional path information: pinfoi
    * Optional query string: $$q$$
        * Following a "?" Character
        * Passing parameters to referenced resource
        * Attributes and values: $$q = (a_1, v_1), (a_2, v_2), ..., (a_n, v_n)$$
        * $$A$$, the set of all attributes, $$a_i$$ belongs to $$A$$
        * $$v_i$$ is a string
        * $$S_q = \{a_1=v_1, a_2=v_2, ..., a_n=v_n\}$$
* URIs without query strings not included in $$U$$
* $$U_r$$: subset of $$U$$ with resource path $$r$$
    * Partition $$U$$
    * Anomaly detection run independently on each $$U_r$$

## Detection models

* One model, one query
* Task: returns probability $$p$$ of normalcy
* Alerting on a single anomalous attribute is **necessarily  excessively cautious**
* Anomaly Score
* Has an associated weight $$w$$
    * Default $$\text{value} = 1$$, in this paper
* Training mode
    1. Create profiles for each server-side program  and each of its attributes
    2. Establish suitable thresholds
        * Store the highest anomaly score
        * Default thresholds: $$10\%$$ larger than the max anomaly score
* Detection mode

### Attribute length

* Length distribution mot follow a smooth curve
* Distribution has a large variance

#### User

* Fixed size tokens
    * Session identifiers
* Short input strings
    * Fields in an HTML form

#### Attacker

* Buffer overflow: shell code & padding
    * Several hundred bytes
* XSS

#### Learning

* Estimate mean μ and variance $$\sigma^2$$ of lengths in training data

#### Detection

* Strings with length larger than mean
    * If $$\text{length} < \text{mean}$$, $$p = 1$$
    * Padding not effective
* **Chebyshev inequality is weak bound**
* **Useful to flag only significant outliers**

### Attribute character distribution

* Character distribution: sorted relative frequencies
    * Lost relative frequencies
    * Example: $$\text{passwd} \Rightarrow 0.33, 0.17, 0.17, 0.17, 0.17, 0, ..., 0$$
    * Fall smoothly for human-readable tokens
    * Fall quickly for malicious input

#### User

* Observations about attributes:
    * Regular structure
    * Mostly human readable
    * Almost always contain only printable characters
*  Frequencies of query parameters distribution
    * Not identical to a standard English text
    * Similar to others

#### Attacker

* Example:
    * Buffer overflow: needs to send binary data & padding
    * Directory traversal exploit: many dots in attribute value

#### Learning

* character distribution of each observed attribute is stored
* Average of all character distributions computed

#### Detection

* Variant of the $$\text{Pearson}\ \chi^2\text{-test}$$
    * goodness-of-fit
* Bins: $$\{[0], [1, 3], [4, 6], [7, 11], [12, 15], [16, 255]\}$$
* For each query attribute:
    * Compute character distribution
    * Observed values $$O_i$$ : Aggregate over bins
    * Expected values $$E_i$$ : Learned character distribution attribute length
* Compute: $$\chi^2$$
* Read corresponding probability

### Structural inference

#### User

* Parameter structure
    * Regular grammar describing all of its legitimate values

#### Attacker

* Detect exploits requiring different parameter structure
    * Examples: Buffer overflow, directory traversal, XSS
* Simple manifestations of an exploit
    * Unusually long parameters
    * Parameters containing repetitions of non-printable characters
* **Evasion**
    * Replace non-printable characters by groups of printable characters

#### Learning

* Seems to be "reasonable"
* Stop before lost much structural information
* Goal: Find a model(NFA) with highest likelihood given training examples
* Markov model/Non-deterministic finite automaton (NFA)
    * "Reasonable generalizaton"
    * $$P_s(o)$$: probability of emitting symbol $$o$$ at state $$S$$
    * $$P(t)$$: probability of transition $$t$$
    * Output: paths from Start state to Terminalstate
* Bayesian model induction:
    * $$P(\text{model}\mid\text{training}\_\text{data}) = \frac{p(\text{training}\_\text{data}\mid\text{model})*p(\text{model})}{p(\text{training}\_\text{data})}$$
    * $$P(\text{training}\_\text{data})$$ a scaling factor; ignored
    * $$P(\text{training}\_\text{data}\mid\text{model})$$ computed as last slide
    * $$P(\text{model})$$: preference towards smaller models
        * Total number of states: $$N$$
        * Total number of transitions at each state $$S$$: $$T(S)$$
        * Total number of emissions at each state $$S$$: $$E(S)$$
    * Start with a model exactly reflecting input data
    * Gradually merge states
    * Until posterior probability does not increase
    * Cost: $$O((n*L)^3)$$ with n training input strings, and L maximum length of each string
    * Up to $$n*L$$ states
    * $$\frac{(n*L)(n*L-1)}{2}$$ comparisons for each merging
    * Up to $$n*(L-1)$$ merges
* Optimizations
    * Viterbi path approximations
    * Path prefix compression
    * Cost: $$O(n*L^2)$$

#### Detection

* First option: Compute probability of query attribute
    * Issue: probabilities of all input words sum up to $$1$$
    * all words have small probabilities
* Output:
    * $$p = 1$$ if word is a valid output of Markov model
    * $$p = 0$$ otherwise

### Token finder

* Goal: determine whether values of an attribute are drawn from an enumerated set of tokens

#### User

* Web applications often require one out of a few possible values for certain query attributes
    * Example: flags, indices

#### Attacker

* Use these attributes to pass illegal values

#### Learning

* Enumerated or Random
* Growth in # of different argument instances compared to total # of argument instances
* Compute correlation between these numbers:
* $$F(x) = x$$
* $$G(x)$$, $$G$$ is like a "enumeration counter"
    * $$G(x) = G(x-1)+1$$ if $$\text{x-th}$$ value is new
    * $$G(x) = G(x-1)-1$$ if $$\text{x-th}$$ value was seen before
    * $$G(x) = 0$$ if $$x = 0$$
    * $$Corr = \frac{Covar(F, G)}{\sqrt{Var(F)Var(G)}}$$
    * If $$Corr < 0$$, then enumeration
    * If enumeration, then store all values for use in detection phase

#### Detection

* If enumeration: value expected to be among stored values
    * Output $$p = 1$$ or $$p = 0$$ correspondingly
    * If random: $$p = 1$$
    * Hash table lookup, efficiency

### Attribute presence or absence

#### User

* URIs typically produced not directly by user, but by scripts, forms, client-side programs
    * Regularity in number, name, order of parameters

#### Attacker

* Hand-crafted attacks typically break this regularity
    * Incomplete or malformed requests to probe/exploit web app
        * Missing argument
        * Mutually exclusive arguments appearing together

#### Learning

* Create a model of acceptable subsets of attributes
* Record each distinct set $$S_q$$
    * Each query $$q$$ during training
    * Hash table

#### Detection

* Lookup the attribute set in hash table
    * Return $$p = 1$$ or $$p = 0$$ correspondingly

### Attribute order

#### User

* same parameters in the same order
* sequential
* Sequential program logic preserves order even when some attributes left out

#### Attacker

* Arbitrary
* No influence on the execution of the program

#### Learning

* Attribute $$a_s$$ precedes $$a_t$$ if as and at appear together in parameter list of at least one query and $$a_s$$ comes before $$a_t$$  when theyappear together
* Directed graph
* $$\text{number of vertices} = \text{number of attributes}$$
* For each training query, add edges between nodes of ordered attribute pairs
* Find all strongly connected components (SCC) of the graph
* Remove edges between nodes in same SCC
* For each node, find all reachable nodes
* Add corresponding pairs to set of precedence orders
* Tarjan's algorithm, $$O(v + e)$$

#### Detection

* Find all order violations
    * Return $$p = 0$$ or $$p = 1$$ correspondingly

### Access frequency

* Frequency patterns of different server-side web applications
* Two types of frequencies:
    * Frequency of application being accessed from a certain client
        * Base on IP address
    * Total frequency of all accesses

#### User

* Eg 1. Authentication script
    * Very infrequently for each individual client
* Eg 2. Search script
    * High for particular client
    * Low for total

#### Attacker

* Suddenly increase access frequency
* Probing
* Guess parameter values
* Evasion: slow down

#### Learning

* divide training time to intervals of fixed time (e.g., 10 sec)
* Count accesses in each interval
* Find total and client-specific distributions

#### Detection

* Chebyshev probability for total, and for client
* Return average of the two probabilities

### Inter-request time delay

#### User

* Wide variance

#### Attacker

* Regular delay between each successive request
    * Surveillance
    * Scripted probe

#### Learning

* Find distribution of normal delays
    * Similar to character distribution model

#### Detection

* $$\text{Pearson}\ \chi^2\text{-test}$$

### Invocation order

* Order of invocation of web-based applications for each client
    * Infer session structure regularity
    * Similar to structural inference model
#### User

* Invocation order can be generated by a certain Markov model

#### Attacker

* Cannot be outputed by that model

#### Learning

* group queries based on source IP
    * Session: Queries within an interval of time
    * Build NFA for sessions

#### Detection

* $$p = 1$$ or $$p = 0$$ depending on session being an output of NFA

## Limitation

* Google
    * Nearly half of the number of false positives
        * Anomalous search strings that contain instances of non-printable characters
            * Probably requests issued by users with incompatible character sets
        * Extremely long strings
            * Such as URLs directly pasted into the search field

## Reference

* A multi-model approach to the detection of web-based attacks, 2005
* CS 259D Lecture 8
