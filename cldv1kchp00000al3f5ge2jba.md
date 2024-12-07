---
title: "Creating an inventory of internet facing websites - Securing websites and applications"
datePublished: Wed Feb 08 2023 02:18:16 GMT+0000 (Coordinated Universal Time)
cuid: cldv1kchp00000al3f5ge2jba
slug: creating-an-inventory-of-internet-facing-websites-securing-websites-and-applications
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/hpjSkU2UYSU/upload/b6c4e780575e2a49f30731fe828e574e.jpeg
tags: python, security, pentesting, infosec-cjbi6apo9015yaywu2micx2eo, cybersecurity-1

---

To protect any organization's assets, the first step is to identify and keep an inventory of all its public-facing websites, web applications, and APIs.

One of the ways to track this is by extracting the A, AAAA, and CNAME records from the DNS zone files and performing a quick nmap scan to check if the websites are responding on port 80 or 443 (which is our primary focus).

We can use the existing zonefile\_parser python package to do the heavy lifting for us and get the intended output, which contains the record types and their contents.

```python
import zonefile_parser
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("input_path", type=str)
args = parser.parse_args()


def run(input_path):
    
    with open(input_path, 'r') as stream:
        content = stream.read()
        records = zonefile_parser.parse(content)

        for record in records:
            print(record.rtype, record.name)
    
if __name__ == "__main__":
    run(
        input_path = args.input_path,
    )
```

Using additional post-processing steps like removing the record types and adding the domain to the beginning of the record contents, we can make a list of hostnames like the one below:

```plaintext
test.abc.com
api.abc.com
www.abc.com
example.abc.com
```

Using the above list as input, we can run the nmap scans on ports 80 and 443 and capture the output in the desired format.

For example:

```plaintext
$nmap -iL input_urls.txt -p80,443 -T2 -oA output
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675821348053/b04122db-a7a6-4fd5-92c9-dd214cd43abb.png align="center")

"-iL" flag specifies the input file with the list of URLs to scan.

"-p" specifies the port. We could also use "-p-" to scan all ports instead.

"-T2" is used to reduce the aggressiveness of the scan from the default value (useful for testing sites deployed in production).

"-oA" outputs the results in all formats supported by nmap.

Parsing the nmap output, we can find the sites that are responding on port 80 or 443.

If an inventory of the internal sites is needed, we need to perform the scan while connected to the internal network over the corporate VPN and ensure that the DNS resolutions are working as expected.

After parsing the internet-facing sites, we can perform manual web application security testing to identify any weaknesses or vulnerabilities.