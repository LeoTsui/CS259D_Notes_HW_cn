# Short Answer Questions

Answer the following in two-three sentences. The slides should be posted to the website once the instructor finishes compiling them.

Describe the relationship between an Intrusion Detection System (IDS) and an Intrusion Prevention System (IPS). Which one is more commonly used and why?

* IDS passive
* IPS active
* IPS is more "powerful" than IDS
* So that, IDS is more common

---

Explain why an attacker would use fast-flux and why the operator is hard to discover.

* Evade detection
* Hind real attacker
* Multi-agents
* Invisibility

---

Using the characteristics of polymorphic shellcode, explain why multi-classifier systems (MCS) provide a good approach to payload-based anomaly detection?

* Better performance with more features
* High computation speed
* High accuracy

---

Why are the features chosen by McPAD a good fit for an ensemble model?

* Consider the relationship of interval characters

---

Give two reasons why the PAYL method for a payload-based anomaly detection cannot gain significantly from ensemble classification.

* Attackers can easy mimic normal payload
* Not enough independent features

---

Using Bays detection rate, explain why machine learning may not be a good detection mechanism for network intrusions?

* Bays error rate is the upper bound of ML system error rate
* Mixed samples are existed
* For actually, P(A) and P(B) are unknown
* We cannot ensure the good performance on both P(A|B) and P(B|A)

---

Building on (Prev), explain why machine learning is good fit for forensics.

* ML can help human to reduce the scope of screening

---

Explain why detecting polymorphic shellcode is essentially infeasible.

* Attackers can
    * Mimic normal payload
    * Encode shellcode
    * Sample random to mimic the noise

---

Explain how robust statistics can tolerate adversarial boiling frog attacks.

* More statistic variables can reduce the "shift" caused by the outliers

|        | robust | non-robust |
| ------ | ------ | ---------- |
| center | median | mean       |
| spread | IQR    | SD, range  |

---

Explain when a security company should "pull the bag off of the inspection conveyor" or when it should "make a copy of the bag ​~~instead of pulling it off and​~~ to catch the intruder later". (The analogy follows is derived from one of the lectures -- email me if you want an explanation)

* Deduce interference of normal process
* For some case, the cost of FP is higher than FN

---

Why would attackers not care to change their IP address when attacking fake government website set up to draw the attacks, according to McAfee?
