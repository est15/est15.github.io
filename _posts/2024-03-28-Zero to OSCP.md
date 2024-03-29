---
layout: post
date: 2024-03-28
title: "My Zero to OSCP Journey" 
categories: [General]
tags: [OSCP,Zero-to-Hero,My Journey,Cybersecurity]
---

## Brief Intro About Myself
My name is Ethan Tomford and I am an aspiring offensive security specialist. I am about to obtain my Bachelor of Science in Computer Information Systems: Cybersecurity. I wanted to write this blog post to cover my journey from having zero cybersecurity knowledge when I started college, to obtaining my OSCP in 2-years.  

## Getting Into Cybersecurity
In high school, I was on the wrestling team and competed in powerlifting. My computer interest started with a pre-built PC from Costco where I would play video games like Counter-Strike: Global Offensive (CSGO) or Rust. I wouldn't have considered myself a “computer” person, least of all someone who is into programming or cybersecurity. Although, I did have the general desire to get a job in a field related to technology. At the time, the the University of Akron (UA) was one of the only colleges to offer a fully-fledged B.S. in Cybersecurity. 

The coursework during my time with UA’s cybersecurity program put a heavy focus on digital forensics. Freshman year, during my Computer Forensics Methods (CFM) course, is where my initial interest in the realm of cybersecurity began. I was getting hands-on experience with tools like FTK Imager, WinHex, VirtualMachines, and Linux. Although I was using these tools very basically, it intrigued me to see the practical application of them.

![WinHex Screenshot](/assets/zero-to-oscp/WinHex-Use.PNG)
_WinHex to Analyze JPEG File Header_


### Akron Cyber Defense Club 
During the fall semester of my sophomore year (2021), I joined the Akron Cyber Defense Club (ACDC) and started my CCNA coursework. ACDC’s main purpose is to compete in the National Collegiate Cyber Defense Competition ([NCDC](https://www.nationalccdc.org/)), and I was lucky enough to be selected as one of the members of the competing team. If you do not know what NCCDC is, it's a competition where college teams are tasked with protecting various servers in addition to solving tasks. All the while professional hackers are attempting to break into our systems and compromise them.

I was tasked with protecting the team's Windows 2012 Server, which I had no prior experience working with. I did my best to learn as much as possible within the 2-week time frame. I researched anything protecting a Windows server, how Active Directory operates, and secure configurations. However, during the competition I was the first machine to get pwned (fully compromised). I ended up locked out of my server for the remainder of the competition. This was my first real look at how fun cybersecurity could be, even when in the face of absolute defeat.

### CCNA Coursework
Another large factor that helped me obtain a solid base and interest in cyber was learning the basics of networking and routing. I knew what an IP address was but nothing of the actual intricies of the protocol. These two courses gave me the fundmental understandings of how a Local Area Network (LAN) is built, the operational layers of the OSI model, protocols, etc. At the time, I didnt know what I wanted to with my cyber career, so I focused very heavily on networking. Im glad I did because a large portion of offensive cybersecurity requires a base level of knowledge in networking.

---

## Focus Shift Towards Offensive Security
At the beginning of the second semester of my sophmore year (2022), ACDC's president at the time, [John Crynick](https://www.linkedin.com/in/john-c-387a61172/), first introduced me to the idea of offensive cybersecurity. During one of our club’s meetings he walked through a retired easy machine on [HackTheBox](https://app.hackthebox.com/profile/811699). I hadn't the slightest idea of anything he was doing, but I wanted to learn. 

### TCM and TryHackMe
John had mentioned starting with The Cyber Mentor (TCM)'s free [Zero to Hero Pentesting](https://www.youtube.com/watch?v=qlK174d_uu8&list=PLLKT__MCUeiwBa7d7F_vN1GUwz_2TmVQj) course and thats what I did. I watched well over 14 hours of content through TCM’s online YouTube course. I would take notes and follow along with what the video was doing, but I found myself struggling to fully grasp the concepts. This is when I discovered TryHackMe ([THM](https://tryhackme.com/)), an "anyone" can learn cybersecurity platform.

I signed up to TryHackeMe and began their [Complete Beginner](https://tryhackme.com/paths) pathway. I was hooked, spending the next two months going through the complete beginner and pre-security pathways. I used KeepNote (which I do not recommend using) to keep track of all my notes. I was beginning to become comfortable with Linux using the command line. I also started to understand the general core concepts when it comes to cybersecurity, PenTesting, Linux, Windows, and vulnerabilities.

![TryHackMe Badge](https://tryhackme-badges.s3.amazonaws.com/est15.png)
_My TryHackMe Profile Badge_


### OverTheWire, PicoCTF, and CodeWars
Other than TryHackMe I used many other resources to supplement my learning. I used [OverTheWire](https://overthewire.org/wargames/bandit/)'s Bandit to assist in learning general Linux command-line operations. [PicoCTF](https://play.picoctf.org/users/est15) to learn various capture-the-flag (CTF) categories such as web exploitation, reverse engineering, forensics, general skills, and binary exploitation. [CodeWars](https://www.codewars.com/users/est15) to gain a base level of understanding in Python for scripting purposes.

![CodeWars Profile Badge](https://www.codewars.com/users/est15/badges/large)
_My CodeWars Profile Badge_ 


### HackTheBox
I used all these resources together to continue my education outside of my degree's coursework. I was hesitant to start on HackTheBox because, at the time, it lacked truly beginner-friendly content. This obvioulsy isnt the case anymore with the improvement of [HTB Academy](https://academy.hackthebox.com/). I slowly started attempting a few of the retired easy boxes, and when I couldn't figure out the next step I would reference a walkthrough. This back and forth process of trying things on my own, then getting a nudge when necessary helped me to create my own methodology for approaching boxes and how to oragnize my notes. I completed roughly around 10 retired easy machines on HTB by the end of my sophmore year.



## Offensive Security Certified Professional (OSCP)
The summer between my sophomore and junior year of college I had a general cybersecurity internship in Manhattan, New York. External studies such as THM and HTB mostly came to a halt during this time. However, I started getting real-world exposure to how Red Teams and PenTesters operate within a corporate environment. By August (2022) at the end of my internship, the desire to have a profession in offensive cybersecurity had become even more strong. In October of that same year I found out about the Offensive Security Certified Professional (OSCP) certification. I decided that getting this cert would be my best bet to get a foothold in the offensive cybersecurity industry.

> **Note:** I started my OSCP coursework in October 2022, a couple of months before Offensive Security's switch to the [PEN-200 2023](https://www.offsec.com/offsec/pen-200-2023/) version of the course. This section talks about the PEN-200 2022 coursework but focuses on the PEN-200 2023 version of the exam. 
{: .prompt-info }

OSCP is considered by many to be the industry-standard penetration testing certification. This is because OSCP is one of the only certifications that requires a practical implementation of the knowledge learned through the course. The test taker has 24 hours to achieve a minimum of 70 points to pass. This section talks about the OSCP exam and what I did to pass. I paid for the LEARN ONE subscription tier, giving myself one full year of access to the PEN-200 course.

### PEN-200 2023 Exam Format
The exam environment consists of three (3) standalone machines, where there is an intended initial access and Local Privilege Escalation (LPE) vector to be exploited. The initial-access (local.txt) portion for each standalone machine is worth 10 points and LPE (proof.txt) is also worth 10 points. Therefore, a total of 60 points can be achieved with the standalone machines.

There is also a simulated Active Directory (AD) environment within the exam worth 40 points. The AD environment contains three machines: an externally facing host, an internal middle-host, and the Domain-controller (DC). To get the 40 points the test taker must show proof of compromising the entire domain (i.e. Administrator Level access on the DC).

### PEN-200 2022 Course Content
I cannot speak much about the current course content and exercises as I completed the 2022 version of the course. While in school, I spent about 4-months fully digesting the PEN-200 course content and completing each section’s practical exercises. By the end of January 2022 I had completed 100% of the course content and exercises. I decided to start focusing on machines from the infamous [Tj Null](https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/edit#gid=665299979) list of OSCP relevant boxes.

## PWK & HTB Labs
Starting to practically apply my knowledge through compromising these boxes is where I felt that I prepared myself the best for tackling the OSCP. I would say I went a little overkill when it came to this. From TJ Null's list I fully compromised 11 PWK Windows machines, 9 PWK Linux machines, 15 HTB Windows machines, and 12 HTB Linux machines for a total of 47. These are just the machines I completed from the TJ Null list, there are other machines I did for additional learning and fun. By this time I started to form a solid methodology for tackling machines, including improving my note taking. 

## PEN-200 Challenge Labs
The PEN-200 Challenge Labs provide three network pentest challenges MedTech, Relia, and Skylark. These are great for honing in your PenTesting methodology and note taking, but are not like the OSCP exam environment. I started by completing MedTech and then Relia, but never got around to Skylark. I would recommend completing these if there is enough time, as they are fun challenges.

The most important labs in my opinion are the three OSCP exam challenges OSCP-A, OSCP-B, and OSCP-C. Each OSCP-A,B,C challenge lab provides a simulated OSCP exam, including three standalone machines and an entire AD environment. I completed the OSCP-A,B,C Active Directory environments twice, they are very good at setting expectations for the actual exam. 

For these machines I brokedown my notes into two main sections Standalone machines and Active Directory (I used [Notion](https://www.notion.so/notes) to keep all my notes). The picture below shows my OSCP-A notes breakdown. I used the same template for the actual OSCP exam.

![OSCP-A Notes Breakdown](/assets/zero-to-oscp/OSCP-notes.png)
_My OSCP-A Notes Breakdown_

## How I Passed the OSCP
I scheduled my first exam attempt for September 17th, 2023. After staying awake for the full 24 hours (everyone I know who has done this has failed, looking back I should have taken ample breaks) I passed the OSCP exam with: 

- 40 pt AD + 1 proof.txt + 1 local.txt + bonus points == 70-points total

![OSCP-Badge](/assets/zero-to-oscp/OSCP-Badge.png)
_OSCP Badge_

I could not get a foothold on two of the standalone machines. It sucked having to use the bonus points as a crutch for passing. After the exam I used my notes to find that I was missing a very common enumeration step on a certain protocol. I'm almost certain that if I did what I now know I would have gotten at least an initial foothold. A pass is still a pass! Just keep in mind [HackTricks](https://book.hacktricks.xyz/) is your friend.  

### [Ligolo-ng](https://github.com/nicocha30/ligolo-ng) Proxying
Im not sure if they make mention of Ligolo-ng in the current PEN-200, but for me they recommended using Chisel. To make your life so much easier when tunneling to the internal network use Ligolo-ng. This [article](https://kentosec.com/2022/01/13/pivoting-through-internal-networks-with-sshuttle-and-ligolo-ng/) helped me understand how to utilize the tool.  

### Note Taking
Prior to the exam I created a template for all my notes. As seen in the screenshot below I break each machine down into various sections. Each protocol identified during the initial reconnaissance phase gets a seperate page to detail my enumeration steps and thought process at every step of the way.   

![OSCP-A Crystal Machine Notes Breakdown](/assets/zero-to-oscp/OSCP-A_crystal.png)
_Individual Machine Notes Breakdown_


# Wrap Up
This has been a very high-level walkthrough of how I went from zero cybersecurity knowledge to obtaining my OSCP certification in a little over 2-years. Im currently studying for Certified Red Team Operator (CRTO), so hopefully I can make a post about that in due time. Thank you for taking the time to read! 
