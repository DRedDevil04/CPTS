We can utilize several techniques to produce any character we want while avoiding the use of blacklisted characters.

## Linux

There are many techniques we can utilize to have slashes in our payload. One such technique we can use for replacing slashes (`or any other character`) is through `Linux Environment Variables` like we did with `${IFS}`. While `${IFS}` is directly replaced with a space, there's no such environment variable for slashes or semi-colons. However, these characters may be used in an environment variable, and we can specify `start` and `length` of our string to exactly match this character.

For example, if we look at the `$PATH` environment variable in Linux, it may look something like the following:

```shell
echo ${PATH}

/usr/local/bin:/usr/bin:/bin:/usr/games
```

```shell
echo ${PATH:0:1}
/
```


```
echo ${LS_COLORS:10:1}

;
```

```shell
127.0.0.1${LS_COLORS:10:1}${IFS}
```

---
## Windows

The same concept works on Windows as well. For example, to produce a slash in `Windows Command Line (CMD)`, we can `echo` a Windows variable (`%HOMEPATH%` -> `\Users\htb-student`), and then specify a starting position (`~6` -> `\htb-student`), and finally specifying a negative end position, which in this case is the length of the username `htb-student` (`-11` -> `\`) :

```sh
echo %HOMEPATH:~6,-11%

\
```
We can achieve the same thing using the same variables in `Windows PowerShell`. With PowerShell, a word is considered an array, so we have to specify the index of the character we need. As we only need one character, we don't have to specify the start and end positions:

```powershell
$env:HOMEPATH[0]

\
```

We can also use the `Get-ChildItem Env:` PowerShell command to print all environment variables and then pick one of them to produce a character we need. `Try to be creative and find different commands to produce similar characters.`

---
## Character Shifting
There are other techniques to produce the required characters without using them, like `shifting characters`. For example, the following Linux command shifts the character we pass by `1`. So, all we have to do is find the character in the ASCII table that is just before our needed character (we can get it with `man ascii`), then add it instead of `[` in the below example. This way, the last printed character would be the one we need:

```shell-session
man ascii 
```

```shell
echo $(tr '!-}' '"-~'<<<[)
```

We can use PowerShell commands to achieve the same result in Windows, though they can be quite longer than the Linux ones.

 Try to use the the character shifting technique to produce a semi-colon `;` character. First find the character before it in the ascii table, and then use it in the above command.


