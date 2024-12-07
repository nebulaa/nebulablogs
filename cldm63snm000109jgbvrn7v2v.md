---
title: "API Authentication Attacks - using Zap Proxy, BurpSuite and Wfuzz"
datePublished: Wed Feb 01 2023 21:15:27 GMT+0000 (Coordinated Universal Time)
cuid: cldm63snm000109jgbvrn7v2v
slug: api-authentication-attacks-using-zap-proxy-burpsuite-and-wfuzz
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/1bNQVGzuy0U/upload/7f6f214c3c3db49e68e6f904ab055f38.jpeg
tags: security, apis, kali-linux, api-testing, cybersecurity-1

---

To run the tests in a self-hosted environment, we can use vAPI, which is a vulnerable, adversely programmed interface that is a self-hostable API.

Running vAPI using a Docker container is fairly straightforward. Download the [vAPI GitHub repo](https://github.com/roottusk/vapi.git) into a folder and run:

```plaintext
$sudo docker-compose -f docker-compose.yml --compatibility up -d
```

vAPI should be up and running in the background, and it can be tested by navigating to http://127.0.0.1/vapi on your local machine or VM.

We are specifically trying to target api2 of vAPI, which allows user authentication by sending a POST request to http://127.0.0.1/vapi/api2/user/login with the content-type header set to *application/json* and the request body parameters (username and password).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675276808601/6415855e-4bd6-41a9-9f88-99cc35c41358.png align="center")

We can use the [FoxyProxy browser](https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/) extension to capture and relay the traffic through Zap and BurpSuite.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675277865260/8139d1ea-fa2e-4bb5-94a7-06eb79b4f0c5.png align="center")

## Zap Proxy

Using FoxyProxy to capture the requests and navigate to [http://127.0.0.1/vapi](http://127.0.0.1/vapi) we can use the GET request populated in Zap.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675278030623/f531b626-8ffd-4d80-9014-3e34c3a91b56.png align="center")

Right-click the request -&gt; Attack -&gt; Fuzzer to edit the request and turn it into a POST request with a content-type header of "*application/json"* and JSON parameters:

```plaintext
{
  "email": "test",
  "password": "test"
}
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675279438955/c4f04c7d-51d5-4b82-a385-fbcdc8748df3.png align="center")

The list of credentials for the attack is can be obtained here: [https://github.com/roottusk/vapi/tree/master/Resources/API2\_CredentialStuffing](https://github.com/roottusk/vapi/tree/master/Resources/API2_CredentialStuffing)

Parse the username and password fields and store them in separate text files. Highlight the "test" fields and add the corresponding filenames for the username and password.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675280470494/663a45ff-92ec-4396-907e-6a5b3063d4d3.png align="center")

Start the fuzzer and check to see if you're getting 200 or 401 response codes.

For this particular scenario, Zap will test one million different payload sets with every combination from the provided one thousand usernames and one thousand passwords.

Attacks similar to Burp's pitchfork feature can be performed using BurpSuite or Wfuzz. This is a more efficient way of dealing with this particular situation.

## BurpSuite

Burp's Pitchfork feature within the Intruder option is perfect for sending specific combinations of payloads from different lists in the same request. Our credential-stuffing attack requires a matched username and password list.

Using FoxyProxy, capture the request using BurpSuite and send the request to *Intruder* with Attack type: *Pitchfork*

Switch the GET to a POST request, change the path to the login endpoint, and add the content-type header and the JSON parameters. Add the "test" fields as the fuzzing positions and switch to the payloads tab.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675283518366/eb4b9464-0b6b-400f-b8cc-1be3401d50fc.png align="center")

Choose the username and password text files for each payload set and start the attack.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675283136726/f33d7254-6616-41f1-8ca4-80fe4f48b9b5.png align="center")

If you do not have BurpSuite Pro, the request speed will be heavily throttled, and running the attack at this speed is inefficient.

However, you can still use it as a proof of concept to perform similar attacks.

### Wfuzz

Wfuzz is the best way to perform the credential stuffing attack against this login API endpoint.

Wfuzz command to perform fuzzing similar to Burp's pitchfork attack is

```plaintext
$wfuzz -d '{"email":"FUZZ","password":"FUZ2Z"}' -H 'Content-Type: application/json' -z file,users.txt -z file,pass.txt -u http://127.0.0.1/vapi/api2/user/login --hc 401 -m zip
```

" -d " flag - sets the POST body

*FUZZ* and *FUZ2Z* \- specifies the payload position 1 and 2 within the POST body

" -H " adds the specified header to every request

" -z " sets the payload file for username and password

" -u " sets the URL to fuzz against

" -hc " hides the response code 401 (Unauthorized)

" -m " sets the iterator function, which combines the payloads from files 1 and 2 to create matched sets.

Official Wfuzz documentation regarding the iterator function: [https://wfuzz.readthedocs.io/en/latest/user/advanced.html](https://wfuzz.readthedocs.io/en/latest/user/advanced.html)

Output from Wfuzz with the credentials returning a 200 response code:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675285427502/e5f32637-f487-476f-9661-c8b5049d1b46.png align="center")

Wfuzz also completes the test at a much faster rate compared to Burp's community edition.

Overall, each of the aforementioned tools has advantages and disadvantages, and this exercise is useful for understanding how each one functions and when it can be applied.