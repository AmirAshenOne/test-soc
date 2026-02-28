# Phase 2 : Core AD & Service

---

## **Tasks:**

- desgin OU structure
- create users/groups and Delegation of Control
- configure LDAP/DNS/GPOs

## 1-Desgin OU structure

in Active Directory Users and Computers right click on securinetsenit.local forest 

![image.png](Phase%202%20Core%20AD%20&%20Service/image.png)

we gonna create this structre using Orgnizational Unit until we get this structure

```bash
securinetsenit.local
├── 📁 OU_COMPUTERS
│   ├── 📁 Servers
│   └── 📁 Workstations
│       └── 💻 [Win10-Client](move it from the computers to here)
├── 📁 OU_SERVICE_ACCOUNTS
└── 📁 OU_USERS
    ├── 📁 Admins
    │   ├── 📁 DomainAdmins
    │   └── 📁 Technical Team
    └── 📁 Departments
        ├── 📁 Finance
        ├── 📁 Marketing
        └── 📁 IT
```

## 2-Create users/group and Delegation of Control

here list of users for every part 

```bash
└── 📁 OU_USERS
    ├── 📁 Admins
    │   ├── 📁 DomainAdmins : da_user:password123!(jhon)
    │   └── 📁 Technical Team : tt_user:password123!(alex)
    └── 📁 Departments
        ├── 📁 Finance :fn_user:password123!(Eloise)
        ├── 📁 Marketing :mkt_user:password123!(Frank)
        └── 📁 IT :it_user:password123!(steve)
```

and for groups 

```bash
└── 📁 OU_USERS
    ├── 📁 Admins
    │   ├── 📁 DomainAdmins : GRP_DomainAdmins
    │   └── 📁 Technical Team : GRP_TechnicalTeam
    └── 📁 Departments
        ├── 📁 Finance :GRP_Finance
        ├── 📁 Marketing :GRP_Marketing
        └── 📁 IT :GRP_IT
```

now we gonna add every user to his own group 

![image.png](Phase%202%20Core%20AD%20&%20Service/image%201.png)

now for every group we gonna set up delegation of control right click on Admins **Delegate Control**

![image.png](Phase%202%20Core%20AD%20&%20Service/image%202.png)

search for the group of department then hit ok

![image.png](Phase%202%20Core%20AD%20&%20Service/image%203.png)

and for every group we gonna chose from the following tasks

![image.png](Phase%202%20Core%20AD%20&%20Service/image%204.png)

**DomainAdmins:**

- no need cause they have everything already

 **Technical Team:**

- create,delete and manage user accounts
- Reset user password and force password change at next logon
- Read all user information
- Create , delete , and manage groups
- Modify the membership of a group
- Generate Resultant Set of Policy(Planning)

**IT Department:**

- Reset user passwords and force password change at next logon
- Read all user information
- Modify the membership of a group
- Generate Resultes set of policy( Planning)

**Finance Department:**

- Rest user passwords and force password change at next logon
- Read all user information

**Marketing Department:**

- Reset user password and force password change at next logon
- Read all user information

## 3-Configure LDAP/DNS/GPOs

### A-Configure LDAP

first of check if the LDAP port 389 is open in the PowerShell by

![image.png](Phase%202%20Core%20AD%20&%20Service/image%205.png)

the result **`TcpTestSucceeded:True`** shows that the port is perfectly listening 

with that we can sign in with administrator account in the client pc 

![image.png](Phase%202%20Core%20AD%20&%20Service/image%206.png)

now we test `whoami /fqdn`  command to shows LDAP path(distinguished names)

![image.png](Phase%202%20Core%20AD%20&%20Service/image%207.png)

now for other user like steve from it department

![image.png](Phase%202%20Core%20AD%20&%20Service/image%208.png)

working well as it should 

### B-Configure DNS

we gonna create a reverse lookup zone to turn IPs into dns

in the **DNS manager** on DC 

![image.png](Phase%202%20Core%20AD%20&%20Service/image%209.png)

select primary Zone and then “store the zone in Active Directory”

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2010.png)

chose to all DNS servrers are running on domain controllers in this domain :sercurinetsenit.local then IPv4 Reverse look up Zone

put the subnet **`192.168.77`**in the Network ID

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2011.png)

make sure to Allow only secure dynamic updates

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2012.png)

and finish .

now in **DNS manager** right click then **proprties** head to **Forwarders** add th public dns like 8.8.8.8 to ensure client can still browse 

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2013.png)

and to clean up the **client DNS** path we gonna run **`ipconfig /registerdns`**  in the client pc 

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2014.png)

<aside>
<img src="https://www.notion.so/icons/info-alternate_green.svg" alt="https://www.notion.so/icons/info-alternate_green.svg" width="40px" />

make sure to run it as administrator user so wont gives this error

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2015.png)

</aside>

now in **DNS manager** in **DC Reverse Lookup Zone the client pc will apear next to it it IP address**

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2016.png)

the **PTR record**  will allos the **DC** to verify the client identity by its ip address

### C-Configure GPOs

in the **Server Manager Dashboard** head torward **Tools** then **Group Policy Management**

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2017.png)

to begin with we gonna edit the **Default Domain Policy** for the **Domain Root**

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2018.png)

we gonna face two list first **Computer Configuration** and second **User Configuration**

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2019.png)

- **Computer Configuration** : applies During the computer’s boot-up process,before anyone log in.
- **User Configuration :** applies During the login process ,after the user enters their credentials.

for example to change the password policy 

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2020.png)

right click on Maximum password age then proprties

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2021.png)

and same for most of the others .

and to create one related to OU just right click on the OU you want to create and link GPO for 

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2022.png)

**Default Domain Policy :**

- **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy within the Default Domain Policy set Passwor must meet complexity requirements Enable**
- **Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Account Policies -> Password Policy within the *Default Domain Policy***. Edit the "**Minimum password length**" set 14
- **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy set Max Age 45 days**
- **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy in the Default Domain Policy set Account lockout threshold 5**
- **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Kerberos Policy set Maximum lifetime for user ticket 10h**

**User-Standard Security (one for all and another for non IT-users)for USERS/Departments :**

- **Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options and configure "Interactive logon: Machine inactivity limit" to 600 seconds**
- **User Configuration > Policies > Administrative Templates > Control Panel and enable the policy "Prohibit access to Control Panel and PC settings" set disable (non-IT)**
- **User Configuration > Policies > Administrative Templates > Desktop > Desktop > Desktop Wallpaper set to enable xxx**
- **User Configuration >Policies > Administrative Templates > System**, in the policy setting named "**Prevent access to the command prompt” set to Enable (non-IT)**

**Computer Hardening :**

- **Computer Configuration > Policies > Windows Settings > Security Settings > Windows Defender Firewall with Advanced Security** select **Properties**, and ensure the Firewall state is set to **On** for Domain, Private, and Public profiles. In the same editor, click on **Inbound Rules**, right-click to select **New Rule**, and choose **Port** to define specific inbound traffic to block
then in cmd run this command to save the changes

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2023.png)

- **Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options and set "Accounts: Guest account status" to Disabled**
- **Computer Configuration > Administrative Templates > Windows Components > AutoPlay Policies and enable the "Turn off AutoPlay" setting, selecting "All drives"**
- **Computer Configuration > Windows Settings > Security Settings > Local Policies > Security Options**. Locate and enable the setting: Domain member: Digitally encrypt or sign secure channel data (always).
- **Computer Configuration > Preferences > Control Panel Settings > Local Users and Groups**. Create a new **Local Group** object, set Action to **Update**, Group name to **Administrators (built-in)**

**Server- Security Baseline:**

- **Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Audit Policy**. Select "Audit object access" and enable success/failure
- Computer Configuration > Windows Settings > Security Settings > Local Policies > Security Options **Network access: Do not allow anonymous enumeration of SAM accounts and shares (or simply SAM accounts) to Enabled**
- Computer Configuration > Windows Settings > Security Settings > Local Policies > Security Options  configure the **Network security: LAN Manager authentication level** policy to "**Send NTLMv2 response only. Refuse LM & NTLM**"
- **Computer Configuration>Policies>Administrative Templates>Windows Components>Windows PowerShel navigate to the Turn on Script Execution policy setting enable and allow all scripts**
- **Computer Configuration > Policies > Administrative Templates > System > Windows Time Service > Time Providers in the Group Policy Management Editor**  Edit  **Configure Windows NTP client** to set the type to `NT5DS` for domain hierarchy synchronization.

**IT Admin Tools Policy:**

- **Computer Configuration>Policies>Administrative Templates>Windows Components>Windows PowerShell**  **Turn on Script Execution enable and all scripts**
- **Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Remote Management (WinRM) > WinRM Service and set "Allow remote server management through WinRM" to Enable**
- **Computer Configuration > Policies > Administrative Templates > System > Power Management > Sleep Settings**. Key settings include setting **Sleep Timeout** and **Hibernate Timeout** to 0 (never) and disabling **Allow Standby States**
- **Computer Configuration > Policies > Windows Settings > Security Settings > Event Log in the GPO editor**. Within this folder, configure the "Retention method for security log" overwrite events as needed
- Computer Configuration > Administrative Templates > System Look for the setting:**Specify settings for optional component installation and component repair set to Enabled and check the box "Download repair content and optional features directly from Windows Update instead of Windows Server Update Services (WSUS).”**

**Finance Application Control :**

- **Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Application Control Policies** > **AppLocker**

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2024.png)

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2025.png)

and …

- **Computer Configuration > Policies > Administrative Templates > System > Removable Storage Access**. Enable policies like "All Removable Storage classes: Deny all access" to block USBs, CDs, and other storage device
- **Computer Configuration > Policies > Windows Settings > Security Settings > Restricted Groups**. right click and add group then member of it and which group they belong to

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2026.png)

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2027.png)

**Marketing Environment Policy:**

- **User Configuration > Policies > Administrative Templates > Windows Components > Internet Explorer, and configuring "Disable changing home page settings"**
- Navigate to User Configuration > Policies > Administrative Templates > System and enable "Prevent access to registry editing tools".
- Navigate to Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > User Rights Assignment. Locate "Change the time zone" and remove authorized users or groups.
- Computer Configuration > Policies > Administrative Templates > Network > Network Connections **Prohibit installation and configuration of Network Bridge on your DNS domain network to enable**

**IT Workstations Policy:**

- **Computer Configuration > Policies > Administrative Templates > Windows Components > Windows PowerShell enable Turn on Script Execution**
- **Computer Configuration** > **Policies** > **Administrative Templates** > **Windows Components** > **Windows PowerShell**. enable **Turn on Module Logging**
    
    ![image.png](Phase%202%20Core%20AD%20&%20Service/image%2028.png)
    
- **Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Advanced Audit Policy Configuration** > **Audit Policies** > **Privilege Use**.

![image.png](Phase%202%20Core%20AD%20&%20Service/image%2029.png)

---