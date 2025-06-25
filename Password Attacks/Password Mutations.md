
## HashCat

We can use a very powerful tool called [Hashcat](https://hashcat.net/hashcat/) to combine lists of potential names and labels with specific mutation rules to create custom wordlists.

Some mutations in HashCat

| **Function** | **Description**                                   |
| ------------ | ------------------------------------------------- |
| `:`          | Do nothing.                                       |
| `l`          | Lowercase all letters.                            |
| `u`          | Uppercase all letters.                            |
| `c`          | Capitalize the first letter and lowercase others. |
| `sXY`        | Replace all instances of X with Y.                |
| `$!`         | Add the exclamation character at the end.         |
official [documentation](https://hashcat.net/wiki/doku.php?id=rule_based_attack) of Hashcat -> all mutations possible

password.list + custom.rule = mut_password.list

each rule in custom.rule applied to each word of password list to give new mutated password

#### Generating Rule-based Wordlist

```shell-session
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```


most used rules is `best64.rule`


#### Hashcat Existing Rules


```shell-session
ls /usr/share/hashcat/rules/
```


## CeWL

We can now use another tool called [CeWL](https://github.com/digininja/CeWL) to scan potential words from the company's website and save them in a separate list.

combine this list with the desired rules and create a customized password list that has a higher probability of guessing a correct password.

Some parameters:
- depth to spider (`-d`)
- minimum length of the word (`-m`)
- storage of the found words in lowercase (`--lowercase`)
- file where we want to store the results (`-w`).

#### Generating Wordlists Using CeWL

```shell-session
cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
```

```shell-session
wc -l inlane.wordlist
```


## Practice Lab


### Catch: Try FTP, as faster than ssh (unencrypted)
