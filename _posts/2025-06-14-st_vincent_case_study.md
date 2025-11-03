---
layout: post
title: "Case Study: St. Vincent's Data Breach"
description: "A relatively in-depth post-analysis of the St. Vincent's 2023 data breach"
comments: false
date: 2025-06-14
categories: [report, data breach]
tags: [research, case study]
author: ashlynn
---

## Victims and Impacts
On December 19, 2023, an Australian health organisation that provided non-for profit medical care and research across various states of Australia, *St. Vincent’s Health Australia* (SVHA), discovered that they had been a target of a cyber security breach in which internal data was illegally accessed and stolen.

As of December 21, 2023, SVHA confirmed that data had been stolen from internal systems by the offending cyber criminals ([SVHA, 2023a](https://www.svha.org.au/news/latest/update-on-cyber-incident-and-our-response)). While the SVHA stated that there has been no direct evidence that sensitive personal information had been accessed, modified, or stolen, the full scope of the breach has since remained unclear and had remained under investigation throughout early 2024. 

Despite SVHA’s positive response in Operational Security, with a prompt forensic investigation and monitoring, the lack of transparency with the media, patients, and the induced uncertainty from stakeholders had created reputational harm from the criticism placed by the public ([ACS, 2024](https://ia.acs.org.au/article/2024/st-vincent-s-stays-silent-on-cyber-attack.html)).

## Method of Operations
While the SVHA haven’t publicly disclosed the specific methods used to breach its systems, the nature of the attack can create some Indicators of Compromise which can be coupled with common attack vectors in the healthcare industry.

Exploring some of these possibilities, the most likely methods used to gain initial access –before moving laterally throughout SVHA’s network to access the extracted data– would be Social Engineering, or, Phishing. These kinds of attacks target the employees directly by attempting to impersonate trusted sources, such as IT Help-Desk, to harvest user account details. As mentioned before, this method often servers as a way to gain initial access into a network before attempting to discover misconfigured access controls or outdated software.

Once the attackers had gained the initial access to the network, there is two common methods of moving laterally through the network to gain access to sensitive movement, these two are typically either misconfigured Active Directory (AD), or Access Control, settings or reliance on insecure software. If the attackers had discovered a misconfiguration in the AD configurations, they can then increase their privileges by moving to an account with higher privileges until they can access the systems or data they’re after. The other option mentioned is reliance on insecure software; like an AD misconfiguration, an attacker can discover the use of outdated software or third-party software with known exploits to attempt to gain privileged access to a system or read secure data from a permission-less system.

Although none of the aforementioned vectors have been confirmed by SVHA, there was confirmation that data had been stolen from the systems, which may indicate to prior reconnaissance from the attackers.

## Security Controls and Prevention Systems
Although the specific details of the breach are unknown to public entities, there are five control mechanisms that may have been able to prevent or mitigate the attack, such as:

- Multi-Factor Authentication (MFA): This control factor is designed to add a second form of identity verification in the form of a physical device or a One-Time Passcode (OTP) to help create a buffer against initial access.

- Endpoint Detection and Response (EDR): EDR tools will continuously monitor systems for suspicious activity or known threat signatures, allowing for faster detection and response to threats like data exfiltration.

- Least Privilege Access (LPA): This constraint limits the access to integral files and information on a system to only the permissions needed for the account user to effectively perform their work, limiting the impact of a compromised account.

- Data Encryption at Rest/Transit: Ensuring that the data being transmitted and stored on, or with, a system essentially rendering it unreadable without having access to the necessary decryption keys.

- Routine Patching and Vulnerability Scanning: routinely performing security scans and automated system and application updates will reduce the possible attack surface that can be used by an attacker before and after initial access has been gained.

While the above list does not cover every possible security control that could have been implemented by SVHA, the recommended measures align closes with the core principles of cyber security. The principles of Confidentiality, Integrity and Availability, more commonly known as the CIA Triad, serve as a baseline for securing sensitive information.

It currently remains unclear whether some, or all, of these constraints were already implemented prior to the breach, as public statements fail to disclose, the SVHA infrastructure has remained obscured throughout the investigation. Due to this lack of transparency, it makes it increasingly difficult for outside members of the public to make recommendations for additional controls and improvements, despite this, the nature of the breach and its target provide an indicator as to how the attack may have happened and what areas of the infrastructure may have been insecure at the time.

## References
ABC News. (2023, December 22). _St Vincent’s Health Australia confirms data stolen in cyber attack_. [https://www.abc.net.au/news/2023-12-22/st-vincents-cyber-attack-data-stolen/103259114](https://www.abc.net.au/news/2023-12-22/st-vincents-cyber-attack-data-stolen/103259114)

Australian Computer Society. (2024, January). _St Vincent’s stays silent on cyber attack_. Information Age. [https://ia.acs.org.au/article/2024/st-vincent-s-stays-silent-on-cyber-attack.html](https://ia.acs.org.au/article/2024/st-vincent-s-stays-silent-on-cyber-attack.html)

St Vincent’s Health Australia. (2023a, December 21). _Update on cyber incident and our response_. [https://www.svha.org.au/news/latest/update-on-cyber-incident-and-our-response](https://www.svha.org.au/news/latest/update-on-cyber-incident-and-our-response)

St Vincent’s Health Australia. (2023b, December 22). _Media statement – Cyber incident update_. [https://www.svha.org.au/news/latest/media-statement-cyber-incident-update](https://www.svha.org.au/news/latest/media-statement-cyber-incident-update)