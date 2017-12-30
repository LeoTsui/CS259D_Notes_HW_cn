# Polymorphic Blending Attacks

<!-- TOC -->

- [Goal](#goal)
- [Related Work](#related-work)
- [Blending Attacks](#blending-attacks)
    - [Scheme of a polymorphic blending attack (PBA)](#scheme-of-a-polymorphic-blending-attack-pba)
    - [Polymorphism Attacks](#polymorphism-attacks)
    - [Polymorphic Blending Attacks](#polymorphic-blending-attacks)
    - [Steps of Polymorphic Blending Attacks](#steps-of-polymorphic-blending-attacks)
    - [Attack Packet Design](#attack-packet-design)
- [Evading PAYL](#evading-payl)
    - [Evading 1-gram](#evading-1-gram)
    - [Evading 2-gram](#evading-2-gram)
- [Experiment Setup](#experiment-setup)
    - [Attack vector](#attack-vector)
    - [Dataset](#dataset)
- [Evaluation](#evaluation)
        - [PAYL Training](#payl-training)
    - [Traditional Polymorphic Attacks (CLET)](#traditional-polymorphic-attacks-clet)
    - [Artificial Profile](#artificial-profile)
    - [1-gram and 2-gram Attacks](#1-gram-and-2-gram-attacks)
- [Countermeasures](#countermeasures)
- [Limitations and Improvements](#limitations-and-improvements)
- [Reference](#reference)

<!-- /TOC -->

## Goal

* Study polymorphic blending attack
* Why network anomaly IDS based on payload statistics not work
* Case study: 1-gram and 2-gram PAYL

## Related Work

* Polymorphic attacks:
    * IP/TCP transformations
    * Mutation exploits (Vigna et al.)
    * Fragroute, Whisker, AGENT, Mistfall, tPE, EXPO, DINA, ADMutate, PHATBOT, JempiScodes
    * CLET
* Defenses against polymorphism
    * Looking for executable code (Toth et al.)
    * Looking for similar structure in multiple code instances (Kruegel et al.)
    * Looking for common substrings present in multiple code instances (Polygraph) - defeated by noise
    * Looking for any exploit of a known vulnerability (Shield)
* Defenses against polymorphism
    * Looking for instruction semantics, detect known code transformations (Cristodorescu et al.)
    * Detect sequence of anomalous system calls (Forest et al.) - can be defeated through mimicry attacks. 
        * New approaches use stack information but they can also be defeated
    * Payload-based anomaly detection: use length, character distribution, probabilistic grammar and tokens to model HTTP traffic (Kruegel et al.); record byte frequency for each port's traffic (PAYL)

## Blending Attacks

### Scheme of a polymorphic blending attack (PBA)

![Scheme-of-a-Polymorphic-Blending-Attack-PBA](images\Scheme-of-a-Polymorphic-Blending-Attack-PBA.png "Scheme of a polymorphic blending attack (PBA)")

### Polymorphism Attacks

* Polymorphism
    * Disguise the packets as normal traffic
    * Change the contents of packets to make it look different
    * Exploit code, not used in normal
    * **Making attacks look different from each other rather than normal**
* Attack Vector
    * Modified part, invariant part
    * Attack invariant could be
        * Small
        * Existing in the normal traffic
    * Could cause high FP
* Attack Body (shellcode)
    * Register shuffling
    * Equivalent instruction substitution
    * Instruction reordering
    * Garbage insertions
    * Encryption
* Polymorphic Decryptor
    * Decrypt shellcode
    * Code obfuscation
* Detection of Polymorphic Attacks
    * Exploit code and/or input data contain some characters that have very low probability of appearing in a normal packet
    * This deviation can be detected

### Polymorphic Blending Attacks

* Making attacks looks different from each other rather
* **Adjust their byte frequency to match that of legitimate traffic (look like normal)**
* Assumption
    * The adversary has already compromised a host X inside a network A which communicates with the target host Y inside network B.
    * The adversary has knowledge of the IDS<sub>B</sub> that monitors the victim host network.
    * A typical anomaly IDS has a threshold setting that can be adjusted to obtain a desired false positive rate.
        * Adversary knows the learning algorithm used by IDS of Network B
        * IDS of Network B is a payload statistics based system
* Adversary's trade-off
    * Attack size
        * Monitor network flow size
    * Process speed
        * Attack should be economical in time and space

### Steps of Polymorphic Blending Attacks

1. Learning The IDS Normal Profile
    * Sniffing the network traffic
    * Generates artificial profile
    * More normal packets captured more closer to the normal profile
    * Profile
        * Average size
        * Rate of packets
        * Byte frequency distribution
        * Range of tokens at different offsets
2. Attack Body Encryption
    * To match the normal profile
        * Substituting every character
        * Padded with some garbage data
    * Reversible operation
    * Generated suitable substitution table
3. Polymorphic Decryptor
    * Remove padding
    * re-substitute characters
    * The decryptor routine is not encrypted but mutated using shellcode polymorphism processing

### Attack Packet Design

* Find a normal profile that similar to the attack packet
    * Character frequency (substitution table)
        * If no significantly different between new and old substitution tables, replace old by new 
    * Packet length
        * Longer than the attack packet
        * Plan B: divide attack body into multiple small packets


## Evading PAYL

### Evading 1-gram

* **Minimize the maximum frequency difference**
* Padding
    * $$\hat{\omega}$$, substituted attack body before padding
    * $$\acute{\omega}$$, substituted attack body after padding
    * $$\|\omega\|$$, length of $$\omega$$
    * $$i$$, idx of characters
    * $$x$$, characters
        * $$x_i$$, the $$i^{th}$$ of the characters
    * $$\lambda$$, $$\#$$ occurrences
        * $$\lambda_i$$, $$\#$$ occurrences of $$x_i$$
    * $$\|\acute{\omega}\| = \|\hat{\omega}\| + \sum_{i=1}^n{\lambda_i}$$
    * $$f(x_i)$$, relative frequency of character $$x_i$$ in normal traffic
    * $$\hat{f}(x_i)$$, relative frequency of character $$x_i$$ in substituted attack traffic
    * $$\lambda_i = \|\acute{\omega}\|f(x_i) - \|\hat{\omega}\|\hat{f}(x_i)$$
    * For some characters, $$f{x_i} < \hat{f}(x_i)$$
    * **The most frequent such character need not be padded** 
    * Let $$\delta = \max{(\hat{f}(x_i) / f(x_i))}$$ be the maximum overuse
    * $$\lambda_i = \|\hat{\omega}\|(\delta f(x_i) - \hat{f}(x_i))$$
* Substitution
    * To minimize padding we need to minimize　$$\delta$$
    * Case 1: attack chars are less numerous than legitimate chars
        * A greedy algorithm that generates one-to-many mapping
        * Sort characters by frequency in attack and legitimate Traffic
        * Match frequencies in decreasing order
        * Remaining legitimate characters are assigned to attack characters that have highest to bring it down
    * Case 2: attack chars are more numerous than legitimate chars
        * A greedy algorithm that generates n-gram-to-one mapping
        * Construct a Huffman tree where leaves are characters in the attack traffic, and smallest two nodes are iteratively connected (thus most frequent characters have shortest n-gram length)
        * We must choose the labels for the links so to preserve the original legitimate character frequency
            * Sort vertices in the tree by weight
            * Sort legitimate characters by their frequency
            * Choose the highest frequency character for the highest weight vertex
            * Remove the vertex from the list and remove the given portion of the character's frequency from further consideration; then resort the characters

### Evading 2-gram

* Must match all 2-byte pairs
* Represent valid 2-grams as states in FSM
* A simple approach will enumerate valid paths in FSM and map attack characters to paths randomly but this generates large code size
    * Better mapping can be obtained by using entropy information, i.e., mapping frequent characters to short paths
* Another approach will attempt to find single byte mappings so that 2-grams are also matched
    * Greedy algorithm sorts 2-grams by frequencies in legitimate and attack traffic and matches them greedily taking care not to violate any existing mappings
* Generate padding so to match the target distribution greedily

## Experiment Setup

### Attack vector

* Windows Media Services (MS03-022)
    * Exploits a vulnerability with logging of user requests
* Attacker vector 99 bytes
    * Presented at the start of the HTTP request
* For buffer overflow attack must send 10KB of data
* Attack body opens a TCP connection and sends registry files
* Size of attack body is 558B and contains 109 unique characters
* Attack was divided into multiple packets
    * Divide decryptor into several packets
* After padding, if final blending attack packet not up to 10KB 
    * Send normal packets

### Dataset

* Captured 15 days of HTTP traffic
    * From one department
    * 14 days' traffic to train the IDS (4,356,565 packets, 1.9GB)
    * Last day's traffic is used by the attacker to learn character distributions
    * Only TCP data packets are used that do not contain known attacks
* IDS builds profiles per packet length
* Selected three frequent packet sizes for the attack

## Evaluation

#### PAYL Training

* PAYL training time increases with the size of the training data because new packets carry more unique n-grams

### Traditional Polymorphic Attacks (CLET)

* Tested CLET-generated polymorphic attacks against PAYL
    * CLET only adds padding to match byte frequency
    * Other polymorphic engines perform worse than CLET against PAYL
    * CLET attack sequence will avoid PAYL detection only  if all packets have an anomaly score above the  threshold
    * Both 1-gram and 2-gram PAYL detected all attacks  with chosen threshold setting

### Artificial Profile

* Training of the artificial profile is stopped when there is no significant improvement over existing profile (measured using Manhattan distance)  within two packets
* Number of packets required for convergence

### 1-gram and 2-gram Attacks

* For 1-gram attacks used one-to-one substitution cipher
* For 2-gram attacks used single byte encoding scheme
    * Two types of transformations were tested
    * Substitution table is constructed for entire attack body - global substitution
    * Substitution table is constructed for each packet separately - local substitution
    * If attack characters are more numerous than those in legitimate traffic, non-existing characters were used
* 2-gram IDS had consistently higher anomaly scores for attacks but it also had higher  thresholds to avoid false positives
    * Overall similar performance as 1-gram IDS
    * More costly for IDS
* Local substitution always outperformed global  substitution

## Countermeasures

* Models
    * More complex models
    * Blend more models
* Features
    * syntactic and semantic information
* High speed hardware
* Introduce randomness
    * Random feature

## Limitations and Improvements

* PAYL is the only case study
* Are the assumption of PBA are realistic
* Explore techniques for continuous data streams

## Reference

* Polymorphic Blending Attacks, Fogla et al, 2006
* [CDA6938 Special Topic:Research in Computer and Network Security (Spring 2007)](http://www.cs.ucf.edu/~czou/CDA6938/Polymorphic_blending_attacks_Himanshu_Pagey.ppt)
* [CIS 864 - Advanced Topics in Network Security - Spring 2007](https://www.eecis.udel.edu/~sunshine/courses/S07/CIS864/blend.ppt)
* Sergio Pastrana, Agustin Orfila, Juan E. Tapiador, Pedro Peris-Lopez, Randomized Anagram revisited, In Journal of Network and Computer Applications, Volume 41, 2014, Pages 182-196, ISSN 1084-8045, 
* CS 259D Lecture 14
