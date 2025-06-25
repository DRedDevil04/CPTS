
Some of the common open-source tools that can assist us in XSS discovery are [XSS Strike](https://github.com/s0md3v/XSStrike), [Brute XSS](https://github.com/rajeshmajumdar/BruteXSS), and [XSSer](https://github.com/epsylon/xsser). We can try `XSS Strike` by cloning it to our VM with `git clone`:


```shell-session
git clone https://github.com/s0md3v/XSStrike.git
```
```shell-session
pip install -r requirements.txt
```
```shell-session
python xsstrike.py
```


Example:
```shell-session
python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test" 
```

## Manual Discovery

When it comes to manual XSS discovery, the difficulty of finding the XSS vulnerability depends on the level of security of the web application. Basic XSS vulnerabilities can usually be found through testing various XSS payloads, but identifying advanced XSS vulnerabilities requires advanced code review skills.

We can find huge lists of XSS payloads online, like the one on [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md) or the one in [PayloadBox](https://github.com/payloadbox/xss-payload-list)

 XSS can be injected into any input in the HTML page, which is not exclusive to HTML input fields, but may also be in HTTP headers like the Cookie or User-Agent (i.e., when their values are displayed on the page).
 
This is why it is not very efficient to resort to manually copying/pasting XSS payloads, as even if a web application is vulnerable, it may take us a while to identify the vulnerability, especially if we have many input fields to test. This is why it may be more efficient to write our own Python script to automate sending these payloads and then comparing the page source to see how our payloads were rendered.

This can help us in advanced cases where XSS tools cannot easily send and compare payloads.

We are unlikely to find any XSS vulnerabilities through payload lists or XSS tools for the more common web applications.

For such cases, manual code review may reveal undetected XSS vulnerabilities, which may survive public releases of common web applications.

These are also advanced techniques that are out of the scope of this module. Still, if you are interested in learning them, the [Secure Coding 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript) and the [Whitebox Pentesting 101: Command Injection](https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection) modules thoroughly cover this topic.

