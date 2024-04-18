### Active Directory/Splunk -Lab
This lab is designed around utilizing virtual environments through VMware to create an active directory Windows server as well as a domain. A victim machine(also Windows) will be linked to the active directory server and Sysmon will be utilized for logging purposes. Splunk will be used as the SIEM of choice as data concerning both the server and victim will be forwarded to Splunk for analysis. The attacking machine will be a Kali Linux VM and all these machines will be connected over a custom NAT with a network address of 192.168.10.0/24. The goal of the lab is to create telemetry between the attacking and defending machines and to analyze the resulting telemetry in the Splunk hub. This will be an exciting lab to complete and a wonderful learning opportunity to become more familiar with active directory as a whole :)  

### As a disclaimer, the original idea for this lab comes from Steven of MyDFIR. For the purposes of streamlining the lab several setup parts have been omitted including the virtual machine set up process, program downloads/installs, and NAT set up. But for anyone wishing to replicate this lab and many others I recommend checking Steven out at both his youtube channel and website linked below. 

[myDFIR](https://www.mydfir.com/)

Youtube: MyDFIR - YouTube

# Network Diagram 

![network topography](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/e02423de-fc8c-4fd7-af28-7209abc72869)


### Objective

The learning objectives for this lab include learning more about the SIEM Splunk. Splunk is one of the most popular SIEM’s available on the market now and as a part of the lab I will be configuring rules and analyzing the telemetry created by the both victim and attacking machine will allow me become more familiar with the SIEM as a whole and get accustomed to setting custom monitoring rules up on Splunk. Creating my own active directory on a Windows server will also be wonderful hands-on experience. On the red team side of things utilizing Kali to launch custom attacks on the victim machine creates many new learning opportunities in itself by understanding from an attacker's point of view how attacks are launched and the process of exploiting vulnerabilities.    


### Tools Used


- VMWare to host the Virtual Environments. 
- Splunk to act as the SIEM. 
- Linux VM to serve as the attacking machine.
- Ubuntu to act as the Splunk host(accessed through IP on victims machine)
- Windows Server VM to act as the Active Directory and Domain
- Windows VM to act as the victim machine connected to the Servers Active Directory
- Crowbar to act as the attack framework utilized by the Kali Linux machine 

  ### Steps: Part 1: Configuring Windows Active Directory and Domain

1. After setting up the VM’s, Splunk, and network we can now see two hosts on the Splunk monitoring section. Clicking on the hosts tab will display the names of the two hosts which should read, ADDC01, and Target-PC. 

![1 Splunk setup](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/58fce036-2088-40ca-891f-2b8be8427503)

2. Next we will switch over to the Windows Server to begin setting up the Active Directory and Domain.

3. Once the server manager dashboard has loaded up I need to click on the top right, “Manage” icon and select, “Add Roles and Features”.

4. It is important to ensure that the, “Roles Based” option is selected

5. After that the only thing left to do is click on Active Directory Domain Services under server roles and keep clicking next until I get to the install option.
      b. The server will need to restart soon after
      
![1 Active Directory Domain Service](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/37130d90-b5cc-4e7c-a4f4-e6b2130de30a)

6. After the system has restarted and I am logged back there will be a yellow alert symbol on the top of the dashboard. Once clicked on I need to press the “Promote to Server” option. 

![2 Promote to domain controller](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/19563574-1143-4f6b-9b89-51502411fec1)

7. Under the Deployment Configuration I will click on “Add new forest” and name the new domain name, Lantern.local

![3 add new forest](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/675a70a3-2633-4353-bcc3-3613d97e9589)

8. The domain needs to have a top level domain so I cannot simply name it Lantern

9. Everything else can be left as default and I only need to input a password for the rest of the process.

10. Like any Active Directory it will need some users. The next steps will involve creating 2 new users by clicking on the  tools icon, and then “Active Directory Users and Computers”.

![4 New Active Directory Domain](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/587c2312-67d2-4c7d-b151-5425075f664c)

11. Next I will navigate to mylantern.local, and under the folders on the left hand side right click, new, create a new folder and name it “IT”. similar to how a real IT team will have its own Organizational Unit(OU) in an Active Directory. 

12. The new user will be named Jenny Smith and a password will be assigned

![5 New OU and User](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/034457af-7b33-4eaa-867a-4e069364be1c)

13. Now the Windows server is acting as the domain controller and the active directory has been set up. The next step is to configure the victim machine to become linked to the newly created domain.


### Steps: Part 2: Linking Victim Machine to Newly Created Domain(Lantern.local)

1. First I will need to login into the the Victim’s machine and click on Settings > About > Advanced system settings > Computer Name > Change > Domain

2. Now I will input the domain just created under the name of Lantern.local

![1 Add to Domain](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/bbd6419e-c42d-429a-8146-1cf6ac02e91a)

3. Once the changes have been made the machine will restart 

4. Now under the login screen I will log in using the newly created user, Jenny. As proof that the Victim machine has joined the new domain under the input password screen there will be a short sentence saying, “Sign in to: Lantern.local”

![2 Sign into new Domain](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/a72e87d5-0788-4954-8cad-cc303ff58d13)

### Steps: Part 3: Attacking Victim Machine 

1. Switching over the attacking machine I will create a custom wordlist using the 20 common passwords taken from the famous wordlist, rockyou.txt. Adding the password of the user Jenny Smith for the purposes of the lab. 

![3 password list](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/bde71905-5bfa-48d2-b96f-8b76d897c9e5)

2. The newly made password list will be saved under the passwords.txt file

![3 password list](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/f4b894c1-78ed-4f9e-9e01-dd8f6795f64f)

3. Switching over to the victim machine I will temporarily enable RDP to allow the attacking machine to successfully brute force attack the victim's machine. This will be allowed for the newly made users created from the previous steps in part 1. 

![4 allow rdp for users](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/9f3374ee-50b3-446c-8df6-546627359730)

4. Going back to the Kali machine I will now initiate a crowbar and launch a brute force attack on the victim machine. We can see that the attack was successful. Now the fun begins as we can see how this telemetry will look in the Splunk dashboard.

![5 successful rdp brute force](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/43cf3400-d303-4a2b-ad4e-dc2ba4a2a781)

5. Logging back into the Splunk dashboard and going over to the monitoring center I will search for the “endpoint” detection index created for the lab. Under the user jsmith and altering the detection time we can see the attempted attacks under the event id of 4625.

![6 splunk event codes](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/55add1fb-2716-4f0e-9293-dd592094738f)

6. A tip for detecting brute force attacks is as seen in the event logs. The time frame in which all of the failed logins were essentially at the same time. Now does this seem like a human simply forgetting their password(hint it does not)?

![7 Event ID 4625](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/fdc6c371-aa22-4c56-a17a-081afb8c6587)

![8 time indicators](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/28797552-5934-4ef9-afca-b36597a18b6a)


7. To dive deeper into the attack we can click on the event to learn more information about the event. Key information such as the workstation name, source IP, and account name used can help SOC analysts better understand and detect the nature of potential attacks.

![9 Attack machine data splunk](https://github.com/Lantern76/Active-Directory-and-Splunk-Lab/assets/119342094/06059531-8eee-489b-8479-b37e78658191)

### This was a fun and adventurous way for me to gain a better understanding of both Active Directories/Domains, and Splunk(a great red teaming simulation as well). I had a blast configuring the new domain and now have a much better understanding of how powerful Splunk is. I plan on diving much deeper into the Splunk tool and will hopefully connect it to a firewall for perhaps a more advanced project at a later date. Thanks for reading :) 
