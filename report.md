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

In this step i used Directory/File Enumeration using ffuf tool with a common wordlist.

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.20.09.png)

from ffuf result i can see that there is a robots.txt i check it and found a dissalowed dirctory i check it too and found secrect.txt .

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.22.33.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.23.03.png)

the secret.txt contain encrypted data after i decode it with base64 tool i got ssh privet key and this is a sestive data sherd publicy i can use it to ssh to the server remotly using `ssh -i option` becuse i already know the username ,
after access to the server now we are in but only as oscp user we want go as root so we will check if the is any way to upgrade Privilege to root so this is the next step.

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.25.49.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.26.22.png)

## Privilege Escalation

After  accsees to machine we will check how we can do Privilege Escalation ,
there is a command i used to check if there is suid blalba .
`find / -perm -u=s 2> /dev/null`

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.28.16.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.30.16.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.30.29.png)

as we can see there is some binarys as suid we can check GTObin how we can use this binary to escale privilege to root , from the result we can see that bash in the list and from gtofbin that bash can escale privilage using `bash -p`

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.31.00.png)

and finly we are root.

# sar 192.168.239.89

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


In this machine i did all the same steps as the first machine but in the step 
<b>Web Enumeration </b> I didn't find any sensitive data i found only a diroctry to sar2HTML i check that dirocrty and i got a sar service runing on server i check if the version of sar if it expots and from explots db i can see there is a vournably i can use it to run shell code exuction as a revers shell what i did first i check ls command and whoaim after that i run server with nc lesing in my ip and port i provide and setablish a connection using a socket with help of python command finaly i have shell form the server I'm now as www-data user .

## Privilege Escalation

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.44.19.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.44.47.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.44.56.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.45.10.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.52.00.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.52.47.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.57.14.png)

![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2001.05.16.png)

In this machine also i check if there is any binary to use to escale privilage but unfornaty i did not fine any so i checked crontab i found there is a script called finally.sh run every 5 minit with root permition so find this script location i cat it and got that script called onther script called write.sh but write.sh owned by www-data and finnaly.sh with root user so i can use this to make crontab run somting for me as root by changing the write.sh so what i did i removed the write.sh from server i and i create new now in my machine with `paython blabla` and run a http server from my machine and download it to the server using wget after that i changed permtion to make it exuxtbale bacause the finnaly script call write script with ./write it need permmtion to excyte now all i need to wait 5 minte to get revese shell with as root .