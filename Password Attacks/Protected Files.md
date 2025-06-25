## Hunting for Encoded Files

 A useful list can be found on [FileInfo](https://fileinfo.com/filetypes/encoded)
 
#### Hunting for Files

```shell-session

for ext in $(echo ".xls .xls* .xltx .csv .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

#### Hunting for SSH Keys

```shell-session
grep -rnw "PRIVATE KEY" /* 2>/dev/null | grep ":1"
```

#### Encrypted SSH Keys

```shell-session
cat /home/cry0l1t3/.ssh/SSH.private
```

- encrypted SSH keys are protected with a passphrase
- must be entered before use
- lightweight [AES-128-CBC](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) can be cracked.

---
## Cracking with John

#### John Hashing Scripts
```shell-session
locate *2john*
```

We can convert many different formats into single hashes and try to crack the passwords with this. Then, we can open, read, and use the file if we succeed.

`ssh2john.py` for SSH keys, which generates the corresponding hashes for encrypted SSH keys, which we can then store in files.

```shell-session
ssh2john.py SSH.private > ssh.hash
```
```shell-session
cat ssh.hash 
```

#### Cracking SSH Keys

```shell-session
john --wordlist=rockyou.txt ssh.hash
```

```shell-session
john ssh.hash --show
```

---
## Cracking Documents

- `office2john.py`

```shell-session
office2john.py Protected.docx > protected-docx.hash
```

```shell-session
cat protected-docx.hash
```

```shell-session
john --wordlist=rockyou.txt protected-docx.hash
```

```shell-session
john protected-docx.hash --show
```

#### Cracking PDFs

```shell-session
pdf2john.py PDF.pdf > pdf.hash
```

```shell-session
cat pdf.hash 
```

```shell-session
john --wordlist=rockyou.txt pdf.hash
```

```shell-session
john pdf.hash --show
```

