# Learning to Detect Phishing Emails

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal and Contribution](#goal-and-contribution)
- [Data](#data)
- [Feature](#feature)
- [Features in Webpage Classification](#features-in-webpage-classification)
- [Machine Learning Implementation](#machine-learning-implementation)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* Phishing
    * Goal:
        * Account information
        * Logon credentials
        * Identity information
    * Attack vectors:
        * Legitimate-looking emails
        * Legitimate-looking websites
* Detection
    * Toolbars
        * Examples
            * Spoofguard
            * Netcraft
        * Disadvantages
            * Decreased amount of contextual information
            * Involve users in decision making process
    * Email filtering
        * Examples
            * SpamAssassin
            * Spamato
        * Advantages
            * More complete context (content, headers, etc.)
            * Completely shield user from decision-making process

## Goal and Contribution

* A machine-learning based phishing e-mail filter

## Data

* SpamAssassin ham corpora
    * ~6950 non-phishing non-spam
* Publicly available phishingcorpus
    * ~ 860 phishing messages
    * Challenge with WHOIS queries
        * Only 505 domains out of 870 domains
        * Increases false negative rate

## Feature

* IP-based URLs
    * Example:
        * `http://192.168.0.1/paypal.cgi?fix_account`
    * Compromised PCs with no DNS entries
    * Not popular
    * Binary feature
* Age of linked-to domain names
    * Registered legitimate-sounding domain names
        * Example: `playpal.com`, `paypal-update.com`
    * Typically short life-span
        * Registered using stolen credit cards, canceled by registrar
        * Domain caught by anti-phishing monitors
        * Often lasting only ~ 48 hours
    * Obtained using a WHOIS query
    * Binary feature: Lifetime < 60 days
* Non-matching URLs
    * Example: `<a href="badsite.com"> paypal.com</a>`
    * Binary feature: URL text different from HREF
* "Here" links to non-modal domain
    * Example: Click here to restore your account
    * Modal domain: domain most frequently linked to
    * Binary feature: link with text "link", "click", "here" that links to a domain other than modal domain
* HTML emails
    * Binary feature: email section with MIME type text/html
* Number of links
    * Continuous feature(Numeric feature): # links in HTML part(s) of email
    * Link defined by an `<a>` tag with href attribute
        * Including `mailto:` links
* Number of domains
    * Domain names for URLs starting with http/ https
    * Only the main part of the domain name
        * What registrar gets paid for
            * Not necessarily same as combination of top- & 2nd-level domain
        * Example:
            * `university.edu` for `www.cs.university.edu`
            * `company.co.jp` for `www.company.co.jp`
                * Top-level: `.jp`
                * second-level: `.co`
    * Numeric feature: # distinct domains
* Number of dots
    * Subdomains: `http://www.my-bank.update.data.com`
    * Redirection script: `http://www.google.com/url?q=http://www.badsite.com`
        * Looks to naive user to be from `google.com`
        * Redirects browser to `badsite.com`
    * Numeric feature: Maximum number of dots in any of the links in the email
* Contains javascript
    * Binary feature: string "javascript" appears in email
    * In `<script>` or `<a>` tag
* Spam filter output
    * Binary feature: class assigned to email by SpamAssassin

## Features in Webpage Classification

* Site in browser history
    * A site never previously visited(not in the history) is more likely to be a phishing website
* Redirected site
* tf-idf
    * Identify key terms of a page

## Machine Learning Implementation

* 10-fold cross validation
* Classifier: Random forest
    * 10 decision trees
    * Each decision made on a random attribute
    * Trees pruned

## Reference

* Learning to Detect Phishing Emails, Fette et al, 2007
* CS 259D Lecture 16
