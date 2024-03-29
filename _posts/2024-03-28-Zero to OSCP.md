---
layout: post
date: 2024-03-27
title: "My Zero to OSCP Journey" 
categories: [General]
tags: [OSCP,Zero-to-Hero,My Journey,Cybersecurity]
---

## Brief Intro About Myself
My name is Ethan Tomford and I am an aspiring offensive security specialist. I am about to obtain my Bachelor of Science in computer information systems: cybersecurity. I wanted to write this blog post to cover my journey from having zero cybersecurity knowledge when I started college to obtaining my OSCP in 2-years.  

## Getting Into Cybersecurity
In high school, I was on the wrestling team and competed in powerlifting. I had a pre-built PC from Costco that I would play video games like Counter-Strike: Global Offensive (CSGO) or Rust. I wouldn't have considered myself a “computer” person, least of all someone who is into programming or cybersecurity. Although, I did have the general desire to get a job in a field related to technology. My Junior I looked at the only college at the time to offer a fully-fledged B.S. in Cybersecurity at the University of Akron (UA).

The coursework I took at UA’s cybersecurity program put a heavy focus on digital forensics. Freshman year, during my Computer Forensics Methods (CFM) course, is where my initial interest in the realm of cybersecurity began. I was getting hands-on experience with tools like FTK Imager, WinHex, VirtualMachines, and Linux. Although I was using these tools very basically, it intrigued me to see the practical application of them.

![WinHex Screenshot](/assets/zero-to-oscp/WinHex-Use.PNG)
_WinHex to Analyze JPEG File Header_


### Akron Cyber Defense Club 
During the fall semester of my sophomore year, I joined the Akron Cyber Defense Club (ACDC) and started my CCNA coursework. ACDC’s main purpose is to compete in the National Collegiate Cyber Defense Competition ([NCDC](https://www.nationalccdc.org/)), and I was lucky enough to be selected as one of the members of the competing team. If you do not know what NCCDC is it is a competition where college teams are tasked with protecting various servers and solving tasks. All the while professional hackers are attempting to break into our system and compromise them.

I was tasked with focusing on protecting the Windows 2012 Server, which I had no prior experience working with. I tried my best to learn as much as possible within the 2-week time frame. During the competition, I was the first machine to get pwned, and I ended up locked out of the server for the remainder of the competition. This was my first real look at how fun cybersecurity could be, even when in the face of absolute defeat.

### CCNA Coursework
Another large factor that helped me obtain a solid base in cyber was learning the basics of Networking and routing. I knew what an IP address was but nothing more. These two courses taught me how networks operate, and the various operational layers of the OSI model. I learned the information to get a passing grade, but it's vital to have a solid understanding of networking to be good at offensive security.

---

## Focus Shift Towards Offensive Security
ACDC's president at the time, [John Crynick](https://www.linkedin.com/in/john-c-387a61172/), first introduced me to the idea of offensive cybersecurity during one of our club’s meetings where he walked through an easy machine on [HackTheBox](https://app.hackthebox.com/home). I hadn't the slightest idea of anything he was doing, but I wanted to learn.

### TryHackMe
John had mentioned starting with The Cyber Mentor (TCM)'s free [Zero to Hero Pentesting](https://www.youtube.com/watch?v=qlK174d_uu8&list=PLLKT__MCUeiwBa7d7F_vN1GUwz_2TmVQj) course. I watched well over 14 hours of content through TCM’s online YouTube course. I would take notes and try to follow along with what the video was doing, but I found myself struggling to fully grasp the concepts. This is when I discovered TryHackMe [THM](https://tryhackme.com/), an "anyone" can learn cybersecurity platform.


I signed up to TryHackeMe and began their [Complete Beginner](https://tryhackme.com/paths) pathway. I was hooked and would spend the next two months going through the complete beginner and pre-security pathways. I used KeepNote (which I do not recommend using) to keep track of all my notes. I was beginning to become comfortable with Linux and using the command line. I started to understand the general core concepts when it comes to cybersecurity, PenTesting, Linux, Windows, vulnerabilities, etc.

![TryHackMe Badge](https://tryhackme-badges.s3.amazonaws.com/est15.png)
_My TryHackMe Profile Badge_


### OverTheWire, PicoCTF, and CodeWars
Other than TryHackMe I used many other resources to supplement my learning. I used [OverTheWire](https://overthewire.org/wargames/bandit/)'s Bandit to assist in learning general Linux command-line operations. [PicoCTF](https://play.picoctf.org/users/est15) to learn various security-related capture-the-flag (CTF) categories such as web exploitation, reverse engineering, forensics, general skills, and binary exploitation. [CodeWars](https://www.codewars.com/dashboard) to gain a base level of understanding in Python for scripting purposes.

![CodeWars Profile Badge](https://www.codewars.com/users/est15/badges/large)
_My CodeWars Profile Badge_ 


### HackTheBox
[My HTB Profile](https://app.hackthebox.com/profile/811699)

I went back and forth with all these resources to continue learning outside of my coursework. At the time, I hadn't transitioned to HackTheBox because of its lack of beginner-friendly machines. However, I would still attempt the easy boxes, when I couldn't figure out the next step I would reference a walkthrough. This helped me to create a methodology for approaching boxes. I completed roughly around 10 easy machines on HTB by the end of my Sophmore year.

The summer between my sophomore and junior year of college I had a general cybersecurity internship in Manhattan, New York. My studies came to a halt during this time. However, I getting real-world exposure to how Red Teams and PenTesters operate within a corporate environment. By the end of the internship, my desire to be a pentester had become even more strong. This is when I found out about the Offensive Security Certified Professional (OSCP) certification through John.


## Offensive Security Certified Professional
> **Note:** I started my OSCP coursework in October 2022, a couple of months before Offensive Security's switch to the [PEN-200 2023](https://www.offsec.com/offsec/pen-200-2023/) version of the course. This section talks about the PEN-200 2022 coursework but focuses on the PEN-200 2023 version of the exam. 
{: .prompt-info }

OSCP is considered by many to be the industry-standard penetration testing certification. This is because OSCP is one of the only certifications that require a practical implementation of the knowledge learned through the course. The test taker has 24 hours to achieve a minimum of 70 points to pass. This section talks about the OSCP exam, the coursework, and what I did to pass. I paid for the LEARN ONE subscription tier, giving myself one full year of access to the PEN-200 course.

### PEN-200 2023 Exam Format
The exam environment consists of three (3) standalone machines, where there is an intended initial access and a Local Privilege Escalation (LPE) vector. The initial-access (local.txt) portion for each standalone machine is worth 10 points and LPE (proof.txt) is also worth 10 points. Therefore, a total of 60 points can be achieved with the standalone machines.

There is also a simulated Active Directory (AD) environment within the exam worth 40 points. The AD environment contains three machines an externally facing host, an internal middle-host, and the Domain-controller (DC). To get the 40 points the test taker must show proof of compromising the entire domain (i.e. Administrator Level access on the DC).

### PEN-200 2022 Course Content
I cannot speak much about the current course content and exercises as I completed the 2022 version of the course. While in school and working roughly 20 hours/week at my internship, I spent about 4-months fully digesting the PEN-200 course content. This includes completing each section’s practical exercises. By the end of January 2022, I had completed 100% of the course content/exercises and decided to start doing machines from the infamous [Tj Null List](https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/edit#gid=665299979) of vulnerable machines.

## PWK & HTB Labs
With the course content completed, I decided to focus 100% on pwning machines within Offsec’s library. This is where I felt that I prepared myself the best for tackling the OSCP. I would say I did overkill when it came to this. The following are all of the TJ Null machines that I successfully pwned in preparation for the exam (in no particular order):

### PWK Windows Machines
2.  Kevin
3.  Slort
4.  AuthBy
5.  Hutch
6.  Jacko 
7.  Algernon
8.  DVR4
9.  Vault
10. Nickel
11. HelpDesk

### PWK Linux Machines 
1. ClamAV
2. Wombo
3. Nibbles
4. Postfish
5. ZenPhoto
6. Walla
7. Pelican
8. Snookums
9. Exfiltrated

### HTB Windows Machines
1. Legacy
2. Blue
3. Devel
4. Optimum
5. Bastard
6. Granny
7. Grandpa
8. Jerry
9. Forest
10. Bastion
11. Buff
12. Active
13. Love
14. Timelapse
15. Sauna

### HTB Linux Machines
1. Lame
2. Shocker
3. Bashed
4. Nibbles
5. Beep
6. Cronos
7. Valentine
8. Poison
9. Irked
10. Delivery
12. Paper

_these are just the machines I completed from the TJ Null list. I completed numerous other machines for additional learning/fun_

## PEN-200 Challenge Labs
The PEN-200 Challenge Labs provides three network pentest challenges (MedTech, Relia, and Skylar). These are great for honing in your PenTesting methodology and note taking; However, these challenge instances are not like the OSCP exam environment. I started by completing MedTech and then Relia, but I never started Skylark. I would recommend completing these if there is enough time, as they are fun challenges.

The most important, in my opinion, are the three OSCP exam challenges (OSCP-A, OSCP-B, and OSCP-C). Each OSCP-A,B,C challenge lab provides a simulated OSCP exam. Each includes three standalone machines and an AD portion.
**I cannot stress enough you NEED to complete each OSCP-A,B,C Active Directory environment**. The practice you get from completing these three is equal to all of the above machines. 

## Passing the OSCP
I scheduled my first exam attempt for the September 17th, 2023. After staying awake for the full 24 hours (everyone I know who has done this has failed, looking back I should have taken ample breaks) I passed the OSCP exam with: 

- 40 pt AD + 1 proof.txt + 1 local.txt + bonus points == 70-points total

![OSCP-Badge](/assets/zero-to-oscp/OSCP-Badge.png)
_OSCP Badge_

I could not get a foothold on two of the standalone machines. It sucked having to use the bonus points as a crutch for passing. I used my notes from the exam and did some research to find that I was missing a very common enumeration step on a certain protocol. I'm almost certain that if I did what I now know I would have gotten at least an initial foothold, and then passed without the bonus points, but a pass is a pass! 

### Recommendations for the Exam
1. Take VERY detailed notes of all your enumeration steps & exploitation steps 
2. Take breaks during the exam (I stayed awake for all 24 hours, but I did take short breaks to clear my head)
3. READ the instructions, once the exam is closed you cannot go back to make sure your report has the requirements such as the local or proof screenshots.

# Wrap Up
This has been a very high-level walkthrough of how I went from zero cybersecurity knowledge to obtaining my OSCP certification in a little over 2-years. Thank you for taking the time to read! 
