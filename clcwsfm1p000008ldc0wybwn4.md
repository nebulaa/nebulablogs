---
title: "Practical Network Penetration Tester Certification (PNPT) — Preparation and Exam Review"
seoTitle: "PNPT exam"
datePublished: Sun Jan 15 2023 02:58:29 GMT+0000 (Coordinated Universal Time)
cuid: clcwsfm1p000008ldc0wybwn4
slug: practical-network-penetration-tester-certification-pnpt-preparation-and-exam-review
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/cckf4TsHAuw/upload/23c7908d6f1f1a449a0b1113c0c1f457.jpeg
tags: infosec-cjbi6apo9015yaywu2micx2eo, exam, information-security, pnpt

---

Background: I work in the field of cybersecurity as a security researcher, but I do not have any experience with real-world assessments.

I registered for the exam plus training using a discount code that was available in November 2021 and began reviewing the course materials.

The exam is structured around the contents of the course, and the recommended order of learning is also provided (shown below for reference).

![PNPT syllabus](https://miro.medium.com/max/700/0*g-CDkPnWilAr4wW_ align="center")

If you are a beginner like I was, I would strongly suggest taking the exam after completing the course and performing the attacks in a lab environment.

I learned a lot from the course material, especially from the parts about Active Directory.

I used Evernote, a note-taking app, to organize my notes and screenshots and write down all the tools and command syntax that could help me on the exam.

![Evernote snippet](https://miro.medium.com/max/700/0*kH5FDU7WG-L4f9qs align="center")

After entering their Discord giveaway in December 2021, I got The Mayor's (Joe Helle) Movement, Pivoting, and Persistence course for free. The course content was beyond the scope of the exam, but it was extremely helpful in reinforcing key concepts such as persistence techniques, pivoting from a compromised host, remoting via proxy chains, etc.

The course materials took me a month to complete, and I signed up for the exam right after.

On the day of the exam, I got an email with the rules and a description of what the external and internal assessments would cover.

I used [KeepNote](http://keepnote.org/) to organize my notes from the exam.

The initial OSINT and the exploiting of the external server were relatively straightforward.

However, I was at a dead end at this point and had no idea how to advance laterally or escalate to the domain controller.

I went through the course material and used other AD methods and cheat sheets from outside the course, such as the ones below:

* [https://book.hacktricks.xyz/windows-hardening/active-directory-methodology](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology)
    
* [PayloadAllThings — Active Directory Methodology](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md)
    

I failed the exam on my first try, so I retook it in July with my old exam notes. I quickly caught up to where I had left off.

Using the clue from the first try, I found the missing link in my attack chain and quickly moved on to the next workstation and then the domain controller.

I used the exam report template that TCM Security shared at the start of the exam.

![](https://miro.medium.com/max/700/0*bJwWCvX6ooIRmgLf align="center")

Sample exam report

The final debrief was about 10 minutes long, and I prepared a presentation to walk through the entire sequence of events to compromise the domain controller along with remediation steps.

Soon after the debrief, I received the certificate over email, and I was added to the Discord chat with other PNPT holders within TCM’s official Discord server.

![](https://miro.medium.com/max/700/0*XB-tskFok4M9-syE align="center")

Based on my experience, I would recommend the exam to anyone who wants to learn about real-world pentesting assessments.

* The exam environment was super stable.
    
* The first exam retake is free; subsequent attempts cost $100.
    
* There were no invasive tools that required me to share the screen or activate cameras.
    
* The TCM Security team was incredibly responsive and helpful throughout the entire process.
    
* The report-writing portion of the exam and the debrief were both excellent additions.
    

Thank you for reading!