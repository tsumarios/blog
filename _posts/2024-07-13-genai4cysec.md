---
layout: post
title:  "(Gen)AI4CySec"
date:   2024-07-13
author:
  - "Mario Raciti"
tags: ai cti dfir
---

This blog post introduces you to the (Gen)AI4CySec project, which highlights the practical applications of Generative AI (GenAI) in cybersecurity.
<!-- readmore -->

![cover](https://img.freepik.com/free-photo/person-suffering-from-technology-addiction-cybersickness_23-2151552591.jpg)

*"The future of cybersecurity lies in the hands of those who can harness the power of AI."* ― ChatGPT

## Table of Contents

- [Introduction](#introduction)
- [A Primer on the Project](#a-primer-on-the-project)
- [Overview of GenAI Scripts](#overview-of-genai-scripts)
- [AI4CySec Notebook](#ai4cysec-notebook)
- [Conclusions](#conclusions)

## Introduction

As a cybersecurity professional, I have always been fascinated by the potential of leveraging Artificial Intelligence (AI) to enhance our capabilities in both defensive and offensive security tasks.
The [(Gen)AI4CySec repository](https://github.com/tsumarios/GenAI4CySec) is a compilation of scripts and a Jupyter notebook developed with the assistance of ChatGPT. Recently, I attended the [Generative AI: Boost Your Cybersecurity Career](https://www.coursera.org/learn/generative-ai-boost-your-cybersecurity-career) course by IBM, which inspired me to start this project.
The tools featured from (Gen)AI4CySec serve as educational resources, showcasing how generative AI can be utilised to perform a range of cybersecurity tasks, from ethical hacking to anti-forensics and machine learning applications.

## A Primer on the Project

The creation of the scripts and the notebook was a collaborative effort with ChatGPT, highlighting the potential of GenAI, and in particular Large Language Models (LLMs), in supporting cybersecurity tasks. Briefly, the process can be summarised as follows.

*Script Development:* Each script was conceptualised based on common cybersecurity challenges and educational objectives. ChatGPT provided coding assistance, helping to refine and optimise the scripts.

*Notebook Creation:* The AI4CySec notebook was designed to demonstrate practical applications of ML in cybersecurity. ChatGPT contributed by generating code snippets, explaining complex concepts, and providing detailed examples.

## Overview of GenAI Scripts

The repository features several scripts designed to illustrate different aspects of cybersecurity, from creating attack tools to detecting malicious software. Here’s a detailed look at each script:

### RAM Reserver (ram_reserver.c)

*URL:* <https://github.com/tsumarios/GenAI4CySec/blob/main/ram_reserver.c>

**Description:** This C script is designed to allocate a large portion of RAM, putting the system under memory pressure and potentially causing a Denial of Service (DoS) condition.

**Use Case:** Demonstrates how resource exhaustion attacks can impact system stability and performance.

### Cookie Manipulator (cookie_manipulator.py)

*URL:* <https://github.com/tsumarios/GenAI4CySec/blob/main/cookie_manipulator.py>

**Description:** This Python script is written to provide multiple manipulation techniques to add/edit/remove HTTP cookies from most modern browsers.

**Use Case:** Illustrates Anti-Forensics manipulation techniques for HTTP cookies.

### DOCX Terminator (docx_terminator.py)

*URL:* <https://github.com/tsumarios/GenAI4CySec/blob/main/docx_terminator.py>

**Description:** A Python script that locates and removes DOCX files from a specified directory.

**Use Case:** Illustrates the simplicity of creating destructive scripts and emphasises the importance of robust file backup and protection strategies.

### Keystroke Logger (keystroke_logger.py)

*URL:* <https://github.com/tsumarios/GenAI4CySec/blob/main/keystroke_logger.py>

**Description:** A basic Python keystroke logger that captures and logs keystrokes to understand the mechanics of keylogging.

**Use Case:** Educates about the risks of keylogging and how such tools can be used maliciously.

### Local Port Blocker (local_port_blocker.py)

*URL:* <https://github.com/tsumarios/GenAI4CySec/blob/main/local_port_blocker.py>

**Description:** This Python script blocks specified local ports by calling iptables.

**Use Case:** Demonstrates how to manipulate network traffic and enforce network policies programmatically.

### Spyware (spyware.py)

*URL:* <https://github.com/tsumarios/GenAI4CySec/blob/main/spyware.py>

**Description:** A simple example of Python spyware to illustrate common techniques used in malicious software, such as data exfiltration and user activity monitoring.

**Use Case:** Highlights the dangers of spyware and the need for effective detection and prevention mechanisms.

### Spyware Detector (spyware_detector.sh)

*URL:* <https://github.com/tsumarios/GenAI4CySec/blob/main/spyware_detector.sh>

**Description:** A shell script to detect and remove spyware by scanning for known signatures and behaviours.

**Use Case:** Provides a practical approach to identifying and mitigating spyware threats on a system.

## AI4CySec Notebook

*URL:* <https://github.com/tsumarios/GenAI4CySec/blob/main/ai4cysec.ipynb>

Beyond the individual scripts, the repository also includes a comprehensive Jupyter notebook that explores the application of Machine Learning (ML) techniques in cybersecurity. The notebook features several key components:

### Anti-Forensics vs Legitimate Privacy Practices

**Description:** The notebook includes an example that simulates a classification between anti-forensics techniques and legitimate privacy practices using various classification algorithms.
Algorithms Used: K-Nearest Neighbors, Naive Bayes, Logistic Regression, Support Vector Machines, Random Forest, XGBoost, and Neural Networks.

**Use Case:** Demonstrates the application of ML in distinguishing between different types of data manipulation techniques.

### Adversarial Machine Learning

**Description:** An introduction to adversarial machine learning with an example based on the Fast Gradient Sign Method (FGSM).

**Use Case:** Provides insights into how adversarial attacks can be used to deceive ML models and the importance of developing robust models.

### Ethical Considerations

While these scripts and the notebook serve as valuable educational tools, it is crucial to emphasise ethical considerations. The tools developed in this repository should be used strictly for educational and research purposes. Unauthorised use of these tools for malicious activities is illegal and unethical.

## Conclusions

The **(Gen)AI4CySec** project showcases the powerful synergy between generative AI and cybersecurity. By leveraging AI tools, in particular LLMs like ChatGPT, cybersecurity professionals can develop innovative solutions and gain deeper insights into both defensive and offensive security strategies. Explore the repository, experiment with the scripts, and delve into the AI4CySec notebook to enhance your understanding of AI-driven cybersecurity techniques.

---

### References

- <https://github.com/tsumarios/GenAI4CySec>
