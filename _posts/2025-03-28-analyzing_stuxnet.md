---
layout: post
title: "Analyzing Stuxnet"
description: "An in-depth post-event technical analysis on Stuxnet"
comments: false
date: 2025-03-28
categories: [MALWARE, report]
tags: [research]
---

## What Is Stuxnet?
Stuxnet is one of the first known cyber-weapons designed by the United States and Israeli Intelligence departments to disable crucial components of the Iranian Nuclear Program. 
The backbone of this malware utilises a subset of Trojan Horse Malware (“Trojan”) known as a Computer Worm (“Worm” or “Worms”). Worms utilise a self-propagating technology to spread from one device to another without human interaction, commonly referred to as a Zero-Click Exploit, unlike their Virus counterpart that requires human interaction to execute and replicate. The method of lateral movement performed by worms exploits in Local Area Network (LAN) connections, which can be created over wireless or wired connections (such as; WiFi or Bluetooth). The exploits utilised by worms are identified within exposed programs on the network.

<br><br>

## Why Was It Created?-
The trigger for the development of Stuxnet was the assumed intelligence discovery of air-gapped (a connection-less state of computer devices) nuclear facilities in Iran, leading to the belief of nuclear weapons development. Although neither government has officially confirmed their involvement in its development, anonymous reports from government officials and celebratory retirement video for Gabi Ashkenazi (A former Israeli Defence Force General) from 2011 hinted towards the governments’ involvement. The anonymous reports given by officials stated that development began during the Bush Administration in an unspecified date during 2005 under the name “Operation Olympic Games,” and continuing it’s development through the Obama Administration. Further analysis from the computer security communities lead to the discovery of a domain registration from November 2005 and a public discovery of the malware in the wild through a public scanning tool, VirusTotal, in November 2007 and then investigated by security researchers in 2010. Although the individual developers of the malware have yet to be identified, Kaspersky Lab’s Roel Schouwenberg estimated that the development team consisted of ten highly skilled programmers, taking up to three years to fully develop the worm.

During analysis of the malware, researchers uncovered more technical intents of the malware outside of its political nature. The design of the malware was to destroy uranium enriching centrifuges made by Iran as part of its nuclear program. Once Stuxnet has infected a target computer, it makes a unique check for specific programmable logic controllers (PLCs) models manufactured by Siemens. These PLCs were used to interact and control the centrifuges, and if the infected device lacked a connection to any PLCs it would simply move to the next device or remain dormant on the system until a PLC was connected. By manipulating the PLCs, Stuxnet was able to damage the uranium isotopes and centrifuges by sending modified signals to the PLC while sending fake information back to the main device, telling it that everything was fine and making diagnosis of issues difficult until damages already occurred.

<br><br>

## Exploitation
Stuxnet utilised four zero-day vulnerabilities within the Windows operating system, along with an addition rootkit that could operate in user and kernel modes. The specific zero-days utilised in Stuxnet leveraged were in relation to print servers (MS10-073), Windows Server Service (MS08-067), LNK vulnerability (CVE-2010-2568). Starting from Stuxnet’s initial infection phase, the malware’s abuse of CVE-2010-2568 allowed it to replicate through removable drives once the initial perpetrator successfully infected the first air-gapped device. Stuxnet performed the exploit with a carefully crafted .LNK file, allowing for the malware to execute code written by an attacker. From there, the malware would then attempt to gain higher privileges through privilege escalation (P.E.) the exploit MS10-073, a vulnerability in Windows’ Kernel-Mode Driver that allowed carefully crafted applications to gain higher access to a system. Although requiring valid credentials to the system, Stuxnet obtained the necessary data through key-logging. Once it gained system-level privileges, the malware would resume to install rootkits (currently unspecified) to hide its files and persistence in the infected system from users and system administrators. Upon completion, the malware would then move onto abusing the MS08-067 exploit to spread itself through networked devices by attacking print servers running under the Windows Server Service program. It also used MS08–067 which allowed for attackers to perform Remote Code Execution (RCE) against consumer and server systems of Windows by crafting a special Remote Procedure Call (RPC) packet, aiding in its propagation.

<br><br>

## What Was The Impact?
Like many other malware, Stuxnet was engineered to maximise the possible damage to a nuclear facility. While holding no immediate threat to the infected device, the centrifuges communicating from a PLC were subjected to harm caused by the intentional malfunctions commanded by Stuxnet. These malfunctions lead to issues with machinery, overheating of complex nuclear systems, and potentially causing deterioration to the compounds inside of the system. In a further unforeseen event, started by the lack of restrictions when spreading through a network, the malware eventually leaked out into public networks, where it’s believed to have infected an estimated 100,000 devices. The outbreak triggered an investigation from community researchers into Stuxnet after it breached into secure networks and facilities. Over the course of a year, the investigation lead to various patches and mitigation methods for the abused exploits, allowing for the development of file signatures and YARA detection policies to identify and prevent any further consumer infections.

<br><br>

### References
\- Fruhlinger, J. (2022). _Stuxnet explained: The first known cyberweapon_. [online] CSO Online. Available at: https://www.csoonline.com/article/562691/stuxnet-explained-the-first-known-cyberweapon.html.
<br>
\- Alvarez, J. (2015). _Stuxnet: The world’s first cyber weapon_. [online] cisac.fsi.stanford.edu. Available at: https://cisac.fsi.stanford.edu/news/stuxnet.
<br>
\- gHale (2011). _Stuxnet Report V: Security Culture Needs Work - ISSSource_. [online] ISSSource. Available at: https://www.isssource.com/stuxnet-report-v-security-culture-needs-work/.
<br>
\- Wang, H., Lau, N. and Gerdes, R.M. (2018). Examining Cybersecurity of Cyberphysical Systems for Critical Infrastructures Through Work Domain Analysis. _Human Factors: The Journal of the Human Factors and Ergonomics Society_, 60(5), pp.699–718. doi:https://doi.org/10.1177/0018720818769250.
<br>
\- BetaFred (2008). _Microsoft Security Bulletin MS08-067 - Critical_. [online] learn.microsoft.com. Available at: https://learn.microsoft.com/en-us/security-updates/securitybulletins/2008/ms08-067.
<br>
\- BetaFred (2023). _Microsoft Security Bulletin MS10-073 - Important_. [online] Microsoft.com. Available at: https://learn.microsoft.com/en-us/security-updates/securitybulletins/2010/ms10-073 [Accessed 25 Mar. 2025].
<br>
\- nvd.nist.gov. (n.d.). _NVD - CVE-2010-2568_. [online] Available at: https://nvd.nist.gov/vuln/detail/CVE-2010-2568.
