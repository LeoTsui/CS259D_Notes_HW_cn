# The security of machine learning

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Framework](#framework)
    - [Security analysis](#security-analysis)
    - [Taxonomy](#taxonomy)
    - [Notation](#notation)
    - [Adversarial learning game](#adversarial-learning-game)
- [Learn, Attack and Defend](#learn-attack-and-defend)
    - [Attack: Causative Integrity](#attack-causative-integrity)
    - [Attack: Causative Availability](#attack-causative-availability)
    - [Attack: Exploratory Integrity](#attack-exploratory-integrity)
    - [Attack: Exploratory Availability](#attack-exploratory-availability)
    - [Defend: against Exploratory attacks](#defend-against-exploratory-attacks)
    - [Defend: against Causative attacks](#defend-against-causative-attacks)
- [SpamBayes Example](#spambayes-example)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Evaluate the quality of a learning system
* Determine whether it satisfies requirements for secure learning
* Computer security evaluation
    * Determining classes of attacks on the system
    * Evaluating the resilience of the system against those attacks
    * Strengthening the system against those classes of attacks
* **Use same model to evaluate secure learning**

## Framework

### Security analysis
* Security goals
    * Integrity goal
    * Availability goal
* Threat model
    * Attacker goal/incentives
        * Cost function
            * **Cost of defender** corresponds to **benefit of attacker**
    * Attacker capabilities
        * Attackers have knowledge of machine learning
        * Attackers can generate arbitrary instances, or not
            * Label by hand labeled
            * Arrival order of packets
        * Attackers can control training data, to what extent

### Taxonomy

* **Influence** (**capability**, influence training data or not)
    * **Causative** attacks influence learning with control over training data
    * **Exploratory** attacks exploit misclassifications but do not affect training
* **Security violation** (**type**)
    * **Integrity** attacks compromise assets via false negatives
    * **Availability** attacks cause denial of service, usually via false positives
* **Specificity** (**specific intention**)
    * **Targeted** attacks focus on a particular instance
    * **Indiscriminate** attacks encompass a wide class of instances
* Influence, structure of the game and move sequence
* Security violation and specificity, cost function

### Notation

![notation](images/SecML-MLJ2010.notation.png)

###  Adversarial learning game

* Exploratory game
    * Defender Choose procedure $$H$$ for selecting hypothesis
    * Attacker Choose procedure $$A_E$$ for selecting distribution
        * Construct an unfavorable evaluation distribution concentrating probability mass on high-cost instances
        * Procedure $$A_E$$ try to construct $$P_E$$
* Causative game
    * Defender Choose procedure $$H$$ for selecting hypothesis
    * Attacker Choose procedures $$A_T$$ and $$A_E$$ for selecting distributions
* Defenders' trade-off
    * Better performance on worst-case vs Less effective on average

## Learn, Attack and Defend

*"Defender" is the "Learner"*

### Attack: Causative Integrity

* Contamination in PAC learning (Kearns and Li 1993)
    * PAC, probably approximately correct
    * **Learner**
        * Success with $$P \geq 1 - \delta$$
        * $$P_\text{incorrect} \leq \epsilon$$
    * **Attacker**
        * Control over training data with fraction $$\beta$$
        * Prevent the learner form succeeding if $$\beta \geq \epsilon / (1 + \epsilon)$$
* Spam foretold
    * **Attacker**
        * Send non-spam resembling the desired spam
        * eg: polysemous words. "watch"
    * **Learner**
        * Mis-train: Misses eventual spam(s)
* Red herring (Newsome et al. 2006)
    * **Attacker**
        * Introduce spurious features into all malicious instances used by defender for training
        * At attack time, malicious instances lack the spurious features and bypass the filter
    * **Learner**
        * Learn spurious features as necessary elements of malicious behavior

### Attack: Causative Availability

* Rogue filter (Nelson et al. 2008)
    * **Attacker**
        * Send spam resembling benign messages
            * Include both spam words and benign words
    * **Learner**
        * Associates benign words with spam
* Correlated outlier (Newsome et al. 2006)
    * **Attacker**
        * Add spurious features to malicious instances
    * **Learner**
        * Filter blocks benign traffic with those features
* Allergy attack (Chung and Mok 2006, 2007) 
    * Against the Autograph worm signature generation system
    * **Learner**
        * Identify infected nodes, based on behavioral patterns
        * Learn new blocking rules by observing traffic from infected nodes
    * **Attacker**
        * Convince Autograph that an attack node is infected by scanning
        * Send crafted packets from attack node, causing Autograph to lean rules blocking begin traffic (DoS)

### Attack: Exploratory Integrity

* Shifty spammer, Good word attacks  (Lowd and Meek 2005b and Wittel and Wu 2004)
    * **Attacker**
        * Craft spam so as to evade classifier without direct influence over the classifier itself
            * Exchange common spam words with less common synonyms
            * Add benign words to sanitize spam
* Polymorphic blending (Fogla and Lee 2006)
    * **Attacker**
        * Encrypt attack traffic so it appears statistically identical to normal traffic
* Mimicry attack, Attacking a sequence-based IDS (Tan et al. 2002)
    * Example: attacking sequence-based IDS
        * Shortest malicious subsequence longer than IDS window size
* Feature drop
* Reverse engineering (Lowd and Meek 2005a)
    * **Attacker**
        * Seeks the highest cost instance that passes the classifier

### Attack: Exploratory Availability

* Mistaken identity (Moore et al. 2006)
    * **Attacker**
        * Interfere with legitimate operation without influence over training
            * Create a spam campaign with target’s email address as the From: address of spams
            * Flood of message bounces, vacation replies, angry responses, etc. fill target’s inbox
* Spoofing
    * **Learner**
        * IPS trained on intrusion traffic blocks hosts that originate intrusions
    * **Attacker**
        * Attack node spoofs legitimate host’s IP address
* Algorithmic complexity  (Dredze et al. 2007; Wang et al. 2007)
    * **Attacker**
        * Sending spams embedded in images

### Defend: against Exploratory attacks

* Defenses against attacks without probing
    * Training data
        * **Defender**
            * Limit information accessible to attacker
    * Feature selection
        * **Defender**
            * Example: use inexact string matching in feature selection to defeat obfuscation of words in spams
            * Avoid spurious features
            * Regularization: smooth weights, defend against feature deletion
    * Hypothesis space/learning procedures
        * **Defender**
            * Complex space harder to decode, but also harder to learn
            * Regularization: balance complexity and over-fitting
* Defenses against probing attacks
    * Analysis of reverse engineering
        * **Attacker**
            * Do not need to model the classifier explicitly
            * Find lowest-attacker-cost instance
        * **Defender**
            * adversarial classifier reverse engineering (ACRE)
            * ACRE find the lowest-attacker-cost
    * Randomization
        * **Defender**
            * Random decision instead of binary decision
    * Limiting/misleading feedback
        * **Defender**
            * Eliminating bounce emails
            * Sending fraudulent feedback

### Defend: against Causative attacks

* The RONI defense
    * Reject On Negative Impact (RONI)
    * Data sanitization
    * **Learner**
        * Train two classifier, by whether including a certain instance
        * Measure the accuracy of them
        * Determine the instance is malicious, or not
        * Reject the instance as detrimental in its effect
* Robustness
    * Robust Statistics, Boiling frog defense
        * Mean, Mean Squared Error(MSE), Standard Deviation
        * Median, Median Absolute Deviation(MAD)
    * Proper regularization
* Online prediction with experts
    * Multi-classifier systems

## SpamBayes Example

| (**Targeted** or **Indiscriminate**) | **Integrity**                                                 | **Availability**                                                                        |
| ------------------------------------ | ------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| **Causative**                        | **Spam foretold**: mis-train **a** or **any** particular spam | **Rogue filter**: mis-train filter to block **a certain** or **arbitrary** normal email |
| **Exploratory**                      | **Shifty spammer**: obfuscate **a** or **any** chosen spam    | **Unwanted reply**: flood **a particular** or **any of several** target inbox           |

* Method:
    * Send attack emails with legitimate words
    * Legitimate words receive higher spam scores
    * Future legitimate emails more likely filtered
* Types:
    * Indiscriminate: Dictionary attack
    * Targeted: Focused attack
* Goals:
    * Get target to disable spam filter
    * DoS against a bidding competitor

## Reference

* The security of machine learning, 2010
* CS 259D Session 10
