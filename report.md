# InfosecPrep 192.168.239.89

## Service Scanning
In this step i uesed <b> nmap </b>  with option -sV 
``` man
  -sV : Probe open ports to determine service/version info
```
* <b>result :</b>
  
![alt text](https://github.com/BleedTheFreak/oscp_report/blob/main/Screen%20Shot%202022-10-05%20at%2000.16.10.png)

as shown in the image below i got in result open port 80 running http server and 22 running ssh .
now i know that there is a webapp in this server i get into the webapp and found website hosted using wordpress and article in the website.
image to the web site 
after reading the article i can see small hint about user of this box.
now service scanning step done we can go to the next step
 ## Web Enumeration

 In this step i used Directory/File Enumeration using ffuf tool with a common wordlist.
 ffuf result image
from ffuf result i can see that there is a robots.txt i check it and found a dissalowed dirctory i check it too and found secrect.txt .
secrect image
the secret.txt contain encrypted data after i decode it with base64 tool i got ssh privet key and this is a sestive data sherd publicy i can use it to ssh to the server remotly using `ssh -i option` becuse i already know the username ,
after access to the server now we are in but only as oscp user we want go as root so we will check if the is any way to upgrade Privilege to root so this is the next step.

## Privilege Escalation

After  accsees to machine we will check how we can do Privilege Escalation ,
there is a command i used to check if there is suid blalba .
`find / -perm -u=s 2> /dev/null`
as we can see there is some binarys as suid we can check GTObin how we can use this binary to escale privilege to root , from the result we can see that bash in the list and from gtofbin that bash can escale privilage using `bash -p`

and finly we are root.

# sar 192.168.239.89

In this machine i did all the same steps as the first machine but in the step 
<b>Web Enumeration </b> I didn't find any sensitive data i found only a diroctry to sar2HTML i check that dirocrty and i got a sar service runing on server i check if the version of sar if it expots and from explots db i can see there is a vournably i can use it to run shell code exuction as a revers shell what i did first i check ls command and whoaim after that i run server with nc lesing in my ip and port i provide and setablish a connection using a socket with help of python command finaly i have shell form the server I'm now as www-data user .

## Privilege Escalation

In this machine also i check if there is any binary to use to escale privilage but unfornaty i did not fine any so i checked crontab i found there is a script called finally.sh run every 5 minit with root permition so find this script location i cat it and got that script called onther script called write.sh but write.sh owned by www-data and finnaly.sh with root user so i can use this to make crontab run somting for me as root by changing the write.sh so what i did i removed the write.sh from server i and i create new now in my machine with `paython blabla` and run a http server from my machine and download it to the server using wget after that i changed permtion to make it exuxtbale bacause the finnaly script call write script with ./write it need permmtion to excyte now all i need to wait 5 minte to get revese shell with as root .