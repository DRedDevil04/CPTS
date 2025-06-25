
#### Download All File Extensions

```shell-session

curl -s https://fileinfo.com/filetypes/compressed | html2text | awk '{print tolower($1)}' | grep "\." | tee -a compressed_ext.txt
```

with `tar`, the tool `openssl` or `gpg` is used to encrypt the archives.

---
## Cracking Archives

## Cracking ZIP

#### Using zip2john

```shell-session
zip2john ZIP.zip > zip.hash
```

#### Viewing the Contents of zip.hash

```shell-session
cat zip.hash 
```

#### Cracking the Hash with John

```shell-session
john --wordlist=rockyou.txt zip.hash
```

```shell-session
john --wordlist=rockyou.txt zip.hash
```

---
## Cracking OpenSSL Encrypted Archives

```shell-session
file GZIP.gzip 

GZIP.gzip: openssl enc'd data with salted password
```

Therefore, the safest choice for success is to use the `openssl` tool in a `for-loop` that tries to extract the files from the archive directly if the password is guessed correctly.

#### Using a for-loop to Display Extracted Contents

```shell-session

for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done
```

Look in current folder for cracked files

---
## Cracking BitLocker Encrypted Drives

[BitLocker](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10) is an encryption program for entire partitions and external drives.

available since Windows Vista and uses the `AES` encryption algorithm with 128-bit or 256-bit length.

If the password or PIN for BitLocker is forgotten, we can use the recovery key to decrypt the partition or drive

The recovery key is a 48-digit string of numbers generated during BitLocker setup that also can be brute-forced.

`bitlocker2john`

[Four different hashes](https://openwall.info/wiki/john/OpenCL-BitLocker) will be extracted, which can be used with different Hashcat hash modes. For our example, we will work with the first one, which refers to the BitLocker password.

#### Using bitlocker2john

```shell-session
bitlocker2john -i Backup.vhd > backup.hashes
```

```shell-session
grep "bitlocker\$0" backup.hashes > backup.hash
```

```shell-session
cat backup.hash
```

The Hashcat mode for cracking BitLocker hashes is `-m 22100`

#### Using hashcat to Crack backup.hash

```shell-session
hashcat -m 22100 backup.hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt -o backup.cracked
```
#### Viewing the Cracked Hash

```shell-session
cat backup.cracked 
```

The easiest way to mount a BitLocker encrypted virtual drive is to transfer it to a Windows system and mount it


