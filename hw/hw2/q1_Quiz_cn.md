# Question 1. True/False + Short answer

## True/False. Answer the following questions with T/F and a short justification.

### a

When designing a intrusion detection system, it is fundamentally harder to create a detector with a low rate of false positives than false negatives.

* False

|     | P      | N      |
| --- | ------ | ------ |
| T   | TP     | **FP** |
| N   | **FN** | TN     |

* FPR = FP / N
* FNR = FN / P
* Since most users are benign, N >> P
* Mathematically, FPR could be lower than FNR
* But, FP could be much more than FN

### b

Your network logger sits on the channel between clients and a server that serves a webpage. Communication happens using TLS. If two clients request the same webpage, they will get the same set of bytes.

* False
* Every TLS session will create new `Client random` and `Server random`
* Session key is different.

### c

Firewalls are signature based intrusion detection systems.

* False
* Firewalls are based on predetermined security rules

### d

According to our guest lecturers, for online web attack detection against their applications, web server logs are the most fruitful (as opposed to logs collected at database, firewall, etc).

* True (I guess)

### e

If you switch your server to service requests over DNSSec, then you are no longer vulnerable to DNS cache poisoning attacks. 

* True
* DNSSec is designed for defending DNS cache poisoning.

## Short Answer. Answer the following questions in a few sentences.

### f

Let’s say Catie is sitting between Alice and Bob. Alice is sending malicious IP packets to Bob. Bob asks Catie to drop any IP packets that match a signature. Catie decides to look at IP packets one at a time and if the payload matches the malicious regex, then Catie drops the packet. Describe a strategy for Alice, so that Bob still gets the malicious payload, but Catie misses it using her regex approach.

* Alice can split single IP packet to evade **"one at a time"** detection.


### g

Describe how defenses at Union Bank (or any financial institution worried about security) would be different if most people in the world, including attackers, used the Tor protocol (onion routing). Specifically describe how Tor would be reflected in the logs and how attack detection would be affected.

* It is almost impossible to know where are the attacks come from by Tor.
* It will be hard to distinguish users and attackers by source IPs.

### h

Traceback is an approach to stop DDoS attacks. Describe the advantages and limitations.

* Advantage
    * Targeted filter source IPs
    * Ever identify attackers
* Limitation
    * Maybe just filter bots controled by vicitom
    * Need to cooperate with ISP
    * Or deployment new detection devices on global network nodes

### i

Let’s say Fred implemented his spam detector using online learning that classifies emails with off-color terms as spam and associates any co-occurring words with spam. Describe how Jill could DoS Mike’s communications with Fred.

* Send spam to Fred, contain "off-color terms" and some special information such as
    * Mike's first/last mane nickname, user name, abbreviation
    * key words of the topics that may include in their communications
* If one special "Mike-Fred" key word be include in black list, that will cause snowball effect: more words will be include in black list, and communication will be completely refused.

### j

Describe how frog boiling in cooking applies to intrusion detection systems.

* The attacker can gradually increase frequency of illegal operations, until detector is not sensitive to those behaviors