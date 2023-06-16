
2. Bandis 2

```
cat ./-
```

3. Bandis 3

    
4. Bandis 4

The password for the next level is stored in a hidden file in the inhere directory.

    
5. Bandis 5
The password for the next level is stored somewhere on the server and has all of the following properties:
    
    human-readable
    1033 bytes in size
    not executable

```
find . -size (1033)c -type f -exec cat {} +

```

6. Bandis 6

The password for the next level is stored somewhere on the server and has all of the following properties:

    owned by user bandit7
    owned by group bandit6
    33 bytes in size

```
find / -group bandit6 -user bandit7 -size 33c -exec cat {} +
```

7. Bandis 7

The password for the next level is stored in the file data.txt next to the word millionth
Commands you may need to solve this level

man, grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

```
grep millionth data.txt  |cut -d ' ' -f2

```

8. Bandis 8

The password for the next level is stored in the file data.txt and is the only line of text that occurs only once
Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd
```
sort data.txt | uniq -u
```

9. Bandis 9

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.


```
strings data.txt | grep "=="

```
10. Bandis 10

The password for the next level is stored in the file data.txt, which contains base64 encoded data

```
strings data.txt |base64 -d
```

11. Bandis 11

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

```
cat data.txt | tr '[a-z]' '[n-za-m]' | tr '[A-Z]' '[N-ZA-M]'

```

12. Bandis 12

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)

```
mkdir /tmp/tempfolder
cd /tmp/tempfolder

xxd -r  /home/bandit12/data.txt > data.dat
file data.dat


```
use tar -xf , gzip -d , bzip -d  to unzip 
```
Pass: wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw

```

13. Bandit 13

The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on

```
ssh -i sshkey.private bandit14@localhost -p 2220


```
14. Bandit 14

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

```
ssh -i sshkey.private bandit14@localhost -p 2220


```

15. Bandit 14->15 
The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

 - Get current password
```
cat /etc/bandit_pass/bandit14
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq

```
- nc to localhost 
```
nc localhost 30000
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
Correct!
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt

```

15. Bandit 15->16 


The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…

```
cat pass.txt 
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt

openssl s_client -connect localhost:30001


jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
Correct!
JQttfApK4SeyHwDlI9SXGR50qclOAil1

```
16. Bandit 16 -> 17

Level Goal

The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

 - Use nmap to scan 

 ```
 nmap -sS -p31000-32000 localhost

 # Nmap 7.80 scan initiated Mon Jun  5 10:06:25 2023 as: nmap -p31000-32000 -oN port1.txt localhost
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00018s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

 ```

 - Use openssl to scan port to port, resulte should be a private key
 - Save key to file. use chmode 600 to that file
 - ssh to bandit17 use -i key.sha

 17. Bandit 17-> 18
Level Goal

There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19





