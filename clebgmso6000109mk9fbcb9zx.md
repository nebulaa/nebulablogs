---
title: "Web Application Penetration Testing: How Do You Get Started? - Part 2"
seoTitle: "web application penetration tester beginner's guide"
datePublished: Sun Feb 19 2023 14:04:24 GMT+0000 (Coordinated Universal Time)
cuid: clebgmso6000109mk9fbcb9zx
slug: web-application-penetration-testing-how-do-you-get-started-part-2
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/fVUl6kzIvLg/upload/ef14fddf6f35ef00848170ad2ad41925.jpeg
tags: security, testing, hacking, application-security, cybersecurity-1

---

The resources for learning the four fundamental skills were introduced in [Part 1](https://nebulablogs.com/web-application-penetration-testing-how-do-you-get-started-part-1).

In this part, we'll focus on the following topics:

1. OWASP Top 10
    
2. OWASP Testing guide
    
3. Web applications to perform testing
    
    1. DVWA - Damn Vulnerable Web Application (Server side - application)
        
    2. OWASP Juice Shop - (Client Side Application)
        
    3. OWASP crAPI and vAPI (API testing)
        
4. Burp Suite's Learning Path
    
5. Supplementary skills
    

## OWASP Top 10

*The Open Worldwide Application Security Project® (*[*OWASP*](https://owasp.org/)*) is a nonprofit foundation that works to improve the security of software.*

The OWASP Top 10 is an excellent resource for learning about the most common vulnerabilities discovered in apps in recent years. The most recent report came out in 2021. Here is an excerpt from the main page.

![Mapping](https://owasp.org/www-project-top-ten/assets/images/mapping.png align="left")

*Image Source:* [*https://owasp.org/www-project-top-ten/*](https://owasp.org/www-project-top-ten/)

To learn about the individual topics, such as "[A01:2021-Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)," I highly recommend visiting the OWASP Top 10 project's website and reading about them. The guide has a description of the findings, example attack scenarios, and prevention methods.

Youtube Resource: [A Starters Guide to Pentesting with OWASP](https://www.youtube.com/watch?v=AO_sqXb-gKE)

Another interesting resource about common vulnerabilities is: [*CWE Top 25 List*](https://cwe.mitre.org/top25/archive/2022/2022_cwe_top25.html)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1676811991907/229598de-1052-4485-ac5b-29da5cc71b1e.png align="center")

## OWASP Testing guide

The testing approach can be adjusted with some hands-on experience; however, I recommend starting with the OWASP testing guide. The current release (version 4.2) is found here: [https://owasp.org/www-project-web-security-testing-guide/v42/](https://owasp.org/www-project-web-security-testing-guide/v42/)

## **Web applications to perform testing**

* ### DVWA - Damn Vulnerable Web Application:
    
    In Kali Linux OS, you can run the below commands to install, start and stop DVWA.
    

```plaintext
sudo apt install dvwa
dvwa-start
dvwa-stop
```

A walkthrough from Hackersploit is available here: [Ethical Hacking 101: Web App Penetration Testing - a full course for beginners](https://youtu.be/2_lswM1S264)

* ## OWASP Juice Shop - (Client Side Application):
    
    Juice Shop is a newer project compared to DVWA and has a lot more room to practice client-side attacks.
    
    I recommend using Docker to install Juice Shop in the Linux VM. Installation guide [here](https://pwning.owasp-juice.shop/part1/running.html).
    
    Youtube resources with OWASP Juice shop walkthrough:  
    [Web Application Ethical Hacking - Penetration Testing Course for Beginners](https://youtu.be/X4eRbHgRawI)
    
    [How to hack OWASP Juice Shop - A Guided Walkthrough](https://youtube.com/playlist?list=PL8j1j35M7wtKXpTBE6V1RlN_pBZ4StKZw)
    
* ## OWASP crAPI and vAPI (API testing):
    
    Testing APIs is a highly sought-after talent since it requires a much more nuanced grasp of how various APIs work, and it is difficult to secure APIs using traditional vulnerability assessments.
    
    The new course presented by Corey Ball (who also wrote a book about the topic of hacking web APIs) is an excellent resource: [https://university.apisec.ai/apisec-certified-expert](https://university.apisec.ai/apisec-certified-expert)
    

## Burp Suite's Learning Path

The final, and by far the best, resource that is currently available to consolidate all the above topics into one course is the free training offered by Burp Suite.

The course contains a plethora of learning materials with labs and offers an integrated progress tracker on the platform.

Both the learning materials and the labs are found here: [https://portswigger.net/web-security](https://portswigger.net/web-security)

## Supplementary skills

Another important skill that should be prioritized is *report writing*. It is important to explain to an audience with different levels of understanding how the vulnerability could be exploited.

The final piece of the puzzle in learning about web application penetration testing is getting hands-on experience. A great place to do that is definitely through *bug bounty* programs. Many YouTube content creators, such as [Nahamsec](https://www.youtube.com/@NahamSec), post content tailored for bug bounty hunters, and it is very useful to learn from their testing methodologies.