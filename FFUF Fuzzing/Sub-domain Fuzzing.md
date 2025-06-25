## Sub-domains

Wordlists : /opt/useful/seclists/Discovery/DNS/

```shell-session

ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/
```

