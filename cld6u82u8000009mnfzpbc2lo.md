---
title: "Passive Reconnaissance Techniques - API Pentesting"
datePublished: Sun Jan 22 2023 03:46:18 GMT+0000 (Coordinated Universal Time)
cuid: cld6u82u8000009mnfzpbc2lo
slug: passive-reconnaissance-techniques-api-pentesting
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/c4aT8MfEzdw/upload/b0dc201017f04aea52a8be6820a63a4d.jpeg
tags: pentesting, infosec-cjbi6apo9015yaywu2micx2eo, cybersecurity-1, osint, api-security

---

I've been studying API penetration testing at [APISec University](https://university.apisec.ai/), as I mentioned in the [previous post](https://nebulablogs.com/my-pentesting-journey-so-far).

I would be summarizing the information I've learned in the course and some of my general research on this topic.

### Passive reconnaissance

1. **Google Dorking**: Use google's advanced search filters and operators to identify APIs
    
    Some useful commands:
    
    *inurl*: Discover websites with a specific word in the URL.
    
    *intitle*: Search for pages with a particular term in the title.
    
    *intext*: Look for pages with a specific term (or words) present somewhere in the text.
    
    *filetype*: finds a certain filetype that is mentioned in the search.
    
    ![Example of a google dork to find potential API keys](https://cdn.hashnode.com/res/hashnode/image/upload/v1674352408969/86a9eb44-2ca4-4e65-8dc4-8dc01267b080.png align="center")
    
    The comprehensive list of Google Dork commands is found here: [https://gist.github.com/sundowndev/283efaddbcf896ab405488330d1bbc06](https://gist.github.com/sundowndev/283efaddbcf896ab405488330d1bbc06)
    
2. **GitHub Dorking**
    
    GitHub can be a useful source for learning about the target API's features, documentation, and hidden information, including API keys, passwords, and tokens.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674353437003/6cf45bc1-5948-4758-aa85-865f01f29e83.png align="center")
    
    Sample search includes:
    
    *filename*:swagger.json
    
    *extension*: .json
    
    "*Exposed API keys*" in Issues
    
3. **Shodan**
    
    Shodan can be used to identify external-facing APIs and obtain information about your target's open ports if you only have an IP address or the name of the organization to work with. However, the free version is limited to a few search pages.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674358362540/07b97195-68f7-42ea-ab94-003be2e38eaa.png align="center")
    
    Shodan's search query examples: [https://www.shodan.io/search/examples](https://www.shodan.io/search/examples)
    
4. **TruffleHog**
    
    As per kali.org, This (TruffleHog) package contains a utility to search through git repositories for secrets, digging deep into commit history and branches. This is effective at finding secrets accidentally committed.
    
    GitHub source: [https://github.com/trufflesecurity/trufflehog](https://github.com/trufflesecurity/trufflehog)
    
    `#sudo docker run -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --org="target name"`
    
    or install and run it locally
    
    `#trufflehog $git_url`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674357497553/c39e5d03-c170-4850-ba99-1b667e5d59c6.png align="left")
    
    ---
    
    *Other tools worth exploring:*
    
    [programmableweb.com](http://programmableweb.com)
    
    As of October 2022, this website has been retired, but it may still include data from older APIs.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674357751022/bd74ac37-fa9b-4b9c-90f4-d11b2d48fc74.png align="left")
    
    **The Wayback Machine**
    
    The Internet Archive, a nonprofit organization based in San Francisco, California, established *The Wayback Machine* as a digital archive of the World Wide Web.
    
    It can be used to look for archived data of the target API for possible information disclosure.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674359007286/4978a462-b832-4bb0-a741-8a31e2f99698.png align="center")