
#### Initiation of Subdomain Takeover

|**Step**|**Subdomain Takeover**|**Concept of Attacks - Category**|
|---|---|---|
|`1.`|The source, in this case, is the subdomain name that is no longer used by the company that we discovered.|`Source`|
|`2.`|The registration of this subdomain on the third-party provider's site is done by registering and linking to own sources.|`Process`|
|`3.`|Here, the privileges lie with the primary domain owner and its entries in its DNS servers. In most cases, the third-party provider is not responsible for whether this subdomain is accessible via others.|`Privileges`|
|`4.`|The successful registration and linking are done on our server, which is the destination in this case.|`Destination`|

#### Trigger the Forwarding

|**Step**|**Subdomain Takeover**|**Concept of Attacks - Category**|
|---|---|---|
|`5.`|The visitor of the subdomain enters the URL in his browser, and the outdated DNS record (CNAME) that has not been removed is used as the source.|`Source`|
|`6.`|The DNS server looks in its list to see if it has knowledge about this subdomain and if so, the user is redirected to the corresponding subdomain (which is controlled by us).|`Process`|
|`7.`|The privileges for this already lie with the administrators who manage the domain, as only they are authorized to change the domain and its DNS servers. Since this subdomain is in the list, the DNS server considers the subdomain as trustworthy and forwards the visitor.|`Privileges`|
|`8.`|The destination here is the person who requests the IP address of the subdomain where they want to be forwarded via the network.|`Destination`|


Subdomain takeover can be used not only for phishing but also for many other attacks. These include, for example, stealing cookies, cross-site request forgery (CSRF), abusing CORS, and defeating content security policy (CSP). We can see some examples of subdomain takeovers on the [HackerOne website](https://hackerone.com/hacktivity?querystring=%22subdomain%20takeover%22), which have earned the bug bounty hunters considerable payouts.

 [Previous](https://academy.hackthebox.com/module/116/section/1512)
