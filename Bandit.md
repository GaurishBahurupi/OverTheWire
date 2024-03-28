# Walkthrough/Write-ups


**SSH Information**     
Host: bandit.labs.overthewire.org   
Port: 2220


----------------------------------------------
## [bandit0](https://overthewire.org/wargames/bandit/bandit0.html)

https://overthewire.org/wargames/bandit/bandit0.html
> "The goal of this level is for you to log into the g    ame using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1."

Here the ``` ssh ``` command should be used to login as the user ``` bandit0 ``` into the specified host and port, followed by the corresponding password.

```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```


----------------------------------------------

## [bandit1](https://overthewire.org/wargames/bandit/bandit1.html)

Login with ``` ssh bandit0@bandit.labs.overthewire.org -p 2220 ```

Perform a the ``` cat ``` command on the file ``` readme ```, the file contains the password to the next level. To check out all files or directories present in the current working directory use ``` ls ```. 

```
bandit0@bandit:~$ ls
readme
bandit0@bandit:~$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```

```cat``` is one of the most frequently used Linux command that is short for concatenate, and is used to view a single or multiple file contents.


----------------------------------------------
## [bandit2](https://overthewire.org/wargames/bandit/bandit2.html)

Login with ``` ssh bandit1@bandit.labs.overthewire.org -p 2220 ```

It's always a good idea to do an ```ls``` once you login to a machine, to see what you can deal with. Doing so gives us a single file '-'.
```
bandit1@bandit:~$ bandit1@bandit:~$ ls
-
bandit1@bandit:~$ ls -l
total 4
-rw-r----- 1 bandit2 bandit1 33 Oct 16  2018 -
```
Your first instict might be to do a ```cat -```, but doing so only makes the shell wait for user input.

As recommended [here](https://overthewire.org/wargames/bandit/bandit2.html), a quick Google search of ['dashed filename'](https://www.google.com/search?q=dashed+filename) gives you a lot to read and know about file naming conventions and stdin/stdout in Unix and Linux.

The main takeaway would be that the dash '-' specifies an option to be set to a linux command in the bash shell, but doesn't have any special meaning to the Unix/Linux kernel. This makes it okay to name a file with just '-', or even start with it. Also, '-' for ``` cat ``` is taken as a stdin/stdout, making it wait for user input. (https://unix.stackexchange.com/questions/16357/usage-of-dash-in-place-of-a-filename)

To work around this, using the absolute or relative path to '-' will do.
```
bandit1@bandit:~$ bandit1@bandit:~$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

B

----------------------------------------------

## [bandit3](https://overthewire.org/wargames/bandit/bandit3.html)

Login with ``` ssh bandit2@bandit.labs.overthewire.org -p 2220 ```

Doing an ``` ls ``` shows that the filename has spaces in it. To deal with passing it as a command parameter, it needs to be enclosed within quotes - " ".

i.e. ``` cat "spaces in this filename" ```.

```
bandit2@bandit:~$ ls
spaces in this filename
bandit2@bandit:~$ ls -l
total 4
-rw-r----- 1 bandit3 bandit2 33 Oct 16  2018 spaces in this filename
bandit2@bandit:~$ cat "spaces in this filename" 
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
bandit2@bandit:~$
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit4](https://overthewire.org/wargames/bandit/bandit4.html)

Login with ``` ssh bandit3@bandit.labs.overthewire.org -p 2220 ```

To view the hidden files in a directory the ``` -a ``` option for ``` ls ``` should be used, and to obtain the password for the next level, i.e. bandit5, a ``` cat ``` on that hidden file would do it.

Before that, we need to change into the ```inhere``` directory using the ``` cd ``` command.

```
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere/
bandit3@bandit:~/inhere$ ls
bandit3@bandit:~/inhere$ ls -a
.  ..  .hidden
bandit3@bandit:~/inhere$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```

Here ```.``` represents the current directory and ```..``` represents the parent directory.

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit5](https://overthewire.org/wargames/bandit/bandit5.html)

Login with ``` ssh bandit4@bandit.labs.overthewire.org -p 2220 ```

``` cd ``` into ``` inhere ``` and check what it contains.
```
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file0
```

You could manually ``` cat ``` each file to see which ones gives readable text, but that wouldn't be efficient nor something you would want to do if you have 100s of files to search from.

Now would be a good time to learn about the ``` file ``` command. This command shows what the given file's file type is.
```
bandit4@bandit:~/inhere$ file ./-file00
./-file00: data
bandit4@bandit:~/inhere$ file ./-file01
./-file01: data
```

Now again, doing it for each and every file is very inefficient. 
Here the Linux wildcard character asterisk (*) would be useful. It matches one or more occurrences of any character, including no character. So, using it to match all file names to check with ``` file ``` would help.
```
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
```

This shows that ``` -file07 ``` is ASCII text, which is human readable. A ``` cat ``` on it would give us the password to bandit5.
```
bandit4@bandit:~/inhere$ cat ./-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit6](https://overthewire.org/wargames/bandit/bandit6.html)

Login with ``` ssh bandit5@bandit.labs.overthewire.org -p 2220 ```

Looking at the description of this level, it would be very useful to lookup how the ```find``` command and its related options work. Finding the right options that sorts out the file with the required constraints should do the trick, which is as follows:

```
bandit5@bandit:~$ ls
inhere
bandit5@bandit:~$ cd inhere/
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere02  maybehere04  maybehere06  maybehere08  maybehere10  maybehere12  maybehere14  maybehere16  maybehere18
maybehere01  maybehere03  maybehere05  maybehere07  maybehere09  maybehere11  maybehere13  maybehere15  maybehere17  maybehere19
bandit5@bandit:~/inhere$ find . -readable -size 1033c
./maybehere07/.file2
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit7](https://overthewire.org/wargames/bandit/bandit7.html)

Login with ``` ssh bandit6@bandit.labs.overthewire.org -p 2220 ```

This requires a similar thought process to what [bandit6](./writeup.md##bandit6) required, but this time we search from the root directory - ```/```

The following options along with their respective value according to the constraints are given to ```find``` as follows:

```
bandit6@bandit:~$ bandit6@bandit:~$ find / -group bandit6 -user bandit7 -size 33c
find: ‘/root’: Permission denied
find: ‘/home/bandit28-git’: Permission denied
find: ‘/home/bandit30-git’: Permission denied
find: ‘/home/bandit5/inhere’: Permission denied
find: ‘/home/bandit27-git’: Permission denied
find: ‘/home/bandit29-git’: Permission denied
find: ‘/home/bandit31-git’: Permission denied
find: ‘/lost+found’: Permission denied
find: ‘/etc/ssl/private’: Permission denied
find: ‘/etc/polkit-1/localauthority’: Permission denied
find: ‘/etc/lvm/archive’: Permission denied
find: ‘/etc/lvm/backup’: Permission denied
find: ‘/sys/fs/pstore’: Permission denied
find: ‘/proc/tty/driver’: Permission denied
find: ‘/proc/7485/task/7485/fd/6’: No such file or directory
find: ‘/proc/7485/task/7485/fdinfo/6’: No such file or directory
find: ‘/proc/7485/fd/5’: No such file or directory
find: ‘/proc/7485/fdinfo/5’: No such file or directory
find: ‘/cgroup2/csessions’: Permission denied
find: ‘/boot/lost+found’: Permission denied
find: ‘/tmp’: Permission denied
find: ‘/run/lvm’: Permission denied
find: ‘/run/screen/S-bandit20’: Permission denied
find: ‘/run/screen/S-bandit23’: Permission denied
find: ‘/run/shm’: Permission denied
find: ‘/run/lock/lvm’: Permission denied
find: ‘/var/spool/bandit24’: Permission denied
find: ‘/var/spool/cron/crontabs’: Permission denied
find: ‘/var/spool/rsyslog’: Permission denied
find: ‘/var/tmp’: Permission denied
find: ‘/var/lib/apt/lists/partial’: Permission denied
find: ‘/var/lib/polkit-1’: Permission denied
/var/lib/dpkg/info/bandit7.password
find: ‘/var/log’: Permission denied
find: ‘/var/cache/apt/archives/partial’: Permission denied
find: ‘/var/cache/ldconfig’: Permission denied
```
Now, in since we cant search through all the directories in the file system due to lack of permissions, we do have our resultant file in that output. To extract that, an inverse search with the string ```"find:"``` on the piped output with the ```grep``` command should work. ```v``` is the ```grep``` command option to do a sort of negation on the pattern match, i.e. inverse search.

```
bandit6@bandit:~$ find / -group bandit6 -user bandit7 -size 33c | grep -v "find:"
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit8](https://overthewire.org/wargames/bandit/bandit8.html)

Login with ``` ssh bandit7@bandit.labs.overthewire.org -p 2220 ```

This a simple pipe - ```|``` and ```grep``` on **data.txt**, with the search pattern being the string "_millionth_".

```
bandit7@bandit:~$ ls
data.txt
bandit7@bandit:~$ cat data.txt | grep millionth
millionth       cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit9](https://overthewire.org/wargames/bandit/bandit9.html)

Login with ``` ssh bandit8@bandit.labs.overthewire.org -p 2220 ```

A quick ```cat``` on **data.txt** gives out a lost of potential possible passwords for the next level. This does seem like a combo of commands and piping will be useful. 

Referring to the "Commands you may need to solve this level" section on the webpage, I checked out the ```man``` pages of ```sort``` and ```uniq```. 

```uniq``` has an option ```c``` which gives the count of a particular unique line in the given data if they are contiguous. The ```sort``` command helps us make repeated occurances of possible passwords sorted one after the other, i.e. contiguous. So, it has become a matter of piping the sorted data to ```uniq -c``` and then ```grep "1 "```.

```
bandit8@bandit:~$ ls
data.txt
bandit8@bandit:~$ sort data.txt | uniq -c | grep "1 "
      1 UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit10](https://overthewire.org/wargames/bandit/bandit10.html)

Login with ``` ssh bandit9@bandit.labs.overthewire.org -p 2220 ```

This is what the ```cat``` on **data.txt** gives.
```
bandit9@bandit:~$ cat data.txt
pE 0NNyzb[a}okJ#yf68~"SĬ72z8ϻ0˼5m)LAp=zFv]9DLW
```
Here, the string command is what has to be used, and this is what the man page for it say: ```strings - print the strings of printable characters in files.```. Which is pretty much the reason it has to be used for this level.

After looking at the the output from ```strings``` on **data.txt** the password for the next level is in the line that has ```===``` in it. So, piping that command with a ```grep``` on the string pattern should give us a clear view of the password.

```
bandit9@bandit:~$ strings data.txt | grep ===
2========== the
========== password
========== isa
========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit11](https://overthewire.org/wargames/bandit/bandit11.html)

Login with ``` ssh bandit10@bandit.labs.overthewire.org -p 2220 ```

Since the content in **data.txt** is base64 encoded we should use the command ```base64``` with the option ```-d``` to decode it.

Base64 is a way to encode binary data into an ASCII character set known to pretty much every computer system, in order to transmit the data without loss or modification of the contents itself. [reference](https://stackoverflow.com/questions/10315757/what-is-the-real-purpose-of-base64-encoding)

```
bandit10@bandit:~$ base64 -d data.txt
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit12](https://overthewire.org/wargames/bandit/bandit12.html)

Login with ``` ssh bandit11@bandit.labs.overthewire.org -p 2220 ```

Looking at the commands that might help section in the description of this level, I see the ```tr``` command. This automatically made me think it was a translation commands keeping in context the level's goal, since lower and uppercase letters need to be rotated/translated by 13 positions. 

Looking up the ```man``` page for ```tr``` tells us that's exactly what it does. It takes in sets of characters that are to be translated, which in our case means rotating the alphabet by 13 positions. 

Piping the file's content to ```tr``` with appropriate sets that map to ROT13 translation of the aplhabet should give us the password to the next level.

```
bandit11@bandit:~$ bandit11@bandit:~$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit13](https://overthewire.org/wargames/bandit/bandit13.html)

Login with ``` ssh bandit12@bandit.labs.overthewire.org -p 2220 ```
gzip, bzip2, tar

```

```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit14](https://overthewire.org/wargames/bandit/bandit14.html)

Login with ``` ssh bandit13@bandit.labs.overthewire.org -p 2220 ```

```

```

Back to [Index](./README.md##Index).

----------------------------------------------

## [bandit15](https://overthewire.org/wargames/bandit/bandit15.html)

Login with ``` ssh bandit14@bandit.labs.overthewire.org -p 2220 ```

```
bandit14@bandit:~$ nc localhost 30000
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit16](https://overthewire.org/wargames/bandit/bandit16.html)

Login with ``` ssh bandit15@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit17](https://overthewire.org/wargames/bandit/bandit17.html)

Login with ``` ssh bandit16@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit18](https://overthewire.org/wargames/bandit/bandit18.html)

Login with ``` ssh bandit17@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit19](https://overthewire.org/wargames/bandit/bandit19.html)

Login with ``` ssh bandit18@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit20](https://overthewire.org/wargames/bandit/bandit20.html)

Login with ``` ssh bandit19@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit21](https://overthewire.org/wargames/bandit/bandit21.html)

Login with ``` ssh bandit20@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit22](https://overthewire.org/wargames/bandit/bandit22.html)

Login with ``` ssh bandit21@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit23](https://overthewire.org/wargames/bandit/bandit23.html)

Login with ``` ssh bandit22@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit24](https://overthewire.org/wargames/bandit/bandit24.html)

Login with ``` ssh bandit23@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit25](https://overthewire.org/wargames/bandit/bandit25.html)

Login with ``` ssh bandit24@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit26](https://overthewire.org/wargames/bandit/bandit26.html)

Login with ``` ssh bandit25@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit27](https://overthewire.org/wargames/bandit/bandit27.html)

Login with ``` ssh bandit26@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit28](https://overthewire.org/wargames/bandit/bandit28.html)

Login with ``` ssh bandit27@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit29](https://overthewire.org/wargames/bandit/bandit29.html)

Login with ``` ssh bandit28@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit30](https://overthewire.org/wargames/bandit/bandit30.html)

Login with ``` ssh bandit29@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit31](https://overthewire.org/wargames/bandit/bandit31.html)

Login with ``` ssh bandit30@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit32](https://overthewire.org/wargames/bandit/bandit32.html)

Login with ``` ssh bandit31@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit33](https://overthewire.org/wargames/bandit/bandit33.html)

Login with ``` ssh bandit32@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
                        
## [bandit34](https://overthewire.org/wargames/bandit/bandit34.html)

Login with ``` ssh bandit33@bandit.labs.overthewire.org -p 2220 ```

```
to be written
```

Back to [Index](./README.md##Index).

----------------------------------------------
