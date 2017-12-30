# Cantina: A content-based approach to detecting phishing websites

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal and Contribution](#goal-and-contribution)
- [Data Source](#data-source)
- [CANTINA](#cantina)
    - [Words](#words)
    - [Feature: Included in Google](#feature-included-in-google)
    - [Other Features](#other-features)
- [Limitation](#limitation)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* TF-IDF
    * Measure importance of word in document
    * TF, frequency of word in document
    * IDF, measure popularity of word in corpus
        * Log(N/#{documents having the term})
    * $$tf\textrm{-}idf (t, d, D) = tf(t, d) \times idf(t, D)$$
* Robust Hyperlinks
    * Lexical signatures for identifying URLs
    * Signature words chosen using TF-IDF
    * Experiments: 5 terms enough for unique page identification
* Observations
    * Minimal changes to original page
        * Detectable via robust hyperlinks
    * Phishing sites often include brand names
        * Common on brand's webpages
        * Rare on the web

## Goal and Contribution

* CANTINA makes use of TF-IDF for detecting phishing sites
* Apply Robust Hyperlinks to anti-phishing
* Not rely on blacklists

## Data Source

* English language sites
* Lhishing URLs
    * PhishTank.com
* Legitimate URLs
    * 3Sharp’s study of anti-phishing toolbars
    * Select the login pages of 35 sites that are often attacked by phishers
    * Select the 35 top pages from Alexa Web Search
    * Select 30 random pages from random.yahoo.com/fast/ryl
* URLs gathered from email

## CANTINA

### Words

* List of word frequencies
    * British National Corpus
        * 67,962,112 total words
        * 9,022 unique words
* Analyze page
    * Downloaded web page
    * Document Object Model (DOM)

### Feature: Included in Google

* Assumption
    * Phishing pages have low pagerank
        * Lack of links pointing to the phishing pages
        * Average time that a phishing site stays online is 4.5 days
* Workflow
    * Compute term TF-IDFs
    * Find top 5 terms
    * Submit terms as query to Google
    * Check if domain is among top-N results
* Decrease False Positives
    * Include domain name in lexical signature
    * Zero results Means Phishing (ZMP)

### Other Features

* Age of domain
* Known images
    * Presence of inconsistent well-known logos
    * Top-10 identified targets: eBay, PayPal, Citibank, Bank of America, Fifth Third Bank, Barclays Bank, ANZ Bank, Chase Bank, and Wells Fargo Bank
* Suspicious URL
    * Contains `@` or `–` in domain name
* Suspicious links
    * Same as suspicious URLs 
* IP address as domain
* Dots in URL
    * Binary: # `.` > 5
* Forms
    * HTML `<input>` tag, with text such as "credit card", "password"

## Limitation

* Non-English web sites
* Rely on Google query
    * Timeout
    * Denial service by Google
    * Google's PageRank, SEO
* TF-IDF
    * Images instead of words
    * Invisible text

## Reference

* Cantina: A content-based approach to detecting phishing websites, Zhang et al, 2007
* CS 259D Lecture 16
