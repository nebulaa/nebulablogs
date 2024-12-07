---
title: "Sub-domain Enumeration Tool with SecurityTrails API - Web Application Testing"
datePublished: Sat Apr 01 2023 18:20:14 GMT+0000 (Coordinated Universal Time)
cuid: clfyatqhu000509l34madaake
slug: sub-domain-enumeration-tool-with-securitytrails-api-web-application-testing
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1680370740087/c22a2721-43c7-4e5e-8523-4d2a7e9a15fb.png
tags: python, security, cybersecurity-1

---

Update: I found a new job, and I'll be working in a DevSecOps/Cloud Security kind of role.

Recently, I've spent some time learning to code faster in Python by practicing on [HackerRank.com,](https://www.hackerrank.com/) and I completed APISec's [*API Penetration Testing Course*](https://www.apisecuniversity.com/courses/api-penetration-testing)*.*

To combine the knowledge from both places, I decided to write a new tool in Python to find and scan thousands of domains by querying the [SecurityTrails API](https://securitytrails.com/corp/api) first and scanning each new domain to check the HTTP response code.

```python
import requests
from tqdm import tqdm
import multiprocessing
import json

def get_sub_domains(domain,filepath):
  url = "https://api.securitytrails.com/v1/domain/"+domain+"/subdomains"
  querystring = {"children_only":"false"}
  headers = {
  'accept': "application/json",
  'apikey': "ENTER_YOUR_API_KEY_HERE"
  }
  API_response = requests.get(url, headers=headers, params=querystring)

  result_json=json.loads(API_response.text)

  sub_domains=[i+'.'+domain for i in result_json['subdomains']]
  f=open(filepath,'w+')
  for i in sub_domains:
    f.write("https://"+i+'\n')
  f.close()

  with open(filepath, 'r') as f:
    links_all = f.read().splitlines()

  return links_all

def get_resp(link):
    try:
        headers = {'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.0.0 Safari/537.36'}
        requests.packages.urllib3.disable_warnings()
        response = requests.get(link, timeout=3, verify=False, headers=headers)
        status_code = str(response.status_code)
        values = link + " returns " + status_code
    except:
        values = link + " returns nothing"
    return values

def run():
    results=[]
    dom = input("\nEnter Domain name to find its sub-domains : ")
    file = input("\nOutput Filename to save all domain names from API response : ")
    links = get_sub_domains(dom,file)

    with multiprocessing.Pool() as pool:
        for result in tqdm(pool.imap(get_resp, links), total=len(links)):
            results.extend([result])

    output_file = str(dom) + "_response_code.txt"
    with open(output_file,'w') as tfile:
	    tfile.write('\n'.join(results))

if __name__ == "__main__":
    run()
```

In order to use the tool, we need an active account from SecurityTrails, and we enter the API key on line 11.

The script takes the user's input for the domain name to search the corresponding sub-domains and the name of the output file.

The function '*get\_sub\_domains*' queries the SecurityTrails API, concatenates the response value to the domain name and adds "https://" as a prefix.

This output is written to a text file with the filename specified in the user's input.

The file is again used to parse the URLs and passed to the *run* function to check the site's HTTP response code. The tool uses a common user-agent string set, doesn't verify the SSL certificate validity (so it can find all possible sub-domains), and sets the HTTP connection timeout to 3 seconds.

The final (second) output file has the suffix "*response\_code.txt*" with the URL and its corresponding HTTP status code in the response.

While benchmarking with [the top 1000 domains list](https://gist.github.com/jgamblin/62fadd8aa321f7f6a482912a6a317ea3), the script was able to scan all of them in 3 minutes. Without multiprocessing enabled, the tool was slow and ineffective.

However, the caveat to using the high-speed scanner is that, while scanning a list of subdomains of a specific company, the chances of our IP being blocked or the requests being throttled due to rate limiting are high.

If the requests are throttled, we could use the same list of sub-domains from the first output file and pass it to a modified script that sends fewer requests per second to fly under the radar of any web-based rate limiters.

Ultimately, this tool serves as a proof of concept, and it was an interesting project to work on for a short time. I would be using this tool to collect and scan targets for bug bounty programs and other security research-related tasks.