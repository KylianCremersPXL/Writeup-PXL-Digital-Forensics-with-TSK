#Rogue Server

## Task 1
No answer needed, just start your attackbox and the machine of the room


## Task 2

### What services have open ports on the server? Answer in alphabetical order in this format: Answer1, Answer2, Answer3, ...

gebruik een nmap scan

```
root@ip-10-10-14-6:~# nmap -sV 10.10.23.87
Starting Nmap 7.80 ( https://nmap.org ) at 2025-04-07 12:09 BST
Nmap scan report for 10.10.23.87
Host is up (0.00023s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
MAC Address: 02:46:08:19:93:07 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.68 seconds
```

Answer: ```http, ssh```

### When you gain deeper access into one of the services, you'll find a clue. What technology is used in the tool the clue mentions?
Eerst heb ik gobuster (```gobuster dir -u http://10.10.23.87 -w /usr/share/wordlists/dirb/common.txt```) gebruikt zodat ik alle ports op de website vond, hier kreeg ik dan /tasks te zien

'''
root@ip-10-10-14-6:~# gobuster dir -u http://10.10.23.87 -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.23.87
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 276]
/.htaccess            (Status: 403) [Size: 276]
/.hta                 (Status: 403) [Size: 276]
/index.html           (Status: 200) [Size: 10918]
/server-status        (Status: 403) [Size: 276]
/tasks                (Status: 301) [Size: 310] [--> http://10.10.23.87/tasks/]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
'''
in /tasks kan ik verder doorklikken naar tasks/tasks_todo.txt dit is de enigste die ik zie.

daar praten ze over dingen zoals permissions veranderen en port veranderen, maar ook iets over phpMyAdmin.

Answer ```php```

## Task 3

### As which user do you initially get access?
Ik heb online opgezocht wat de default ubuntu user zou kunnen zijn waarbij ik dan deze (website)[https://www.alibabacloud.com/blog/finding-out-what-user-apache-is-running-as_598512#:~:text=Apache%2C%20in%20its%20working%20process,to%20see%20the%20user%20configured.&text=You%20can%20see%20the%20user,data%20on%20the%20Ubuntu%20server.&text=Here%2C%20the%20user%20is%20apache%20on%20the%20CentOS%20server.] heb gevonden, hierin stond de user als www-data

Answer: ```www-data```


### What user has an account on the machine?


## Task 4
