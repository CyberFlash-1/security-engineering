## Install Active Directory Domain Services
What is a Domain Controller?
A Domain Controller (DC) is a server that runs Active Directory. It is the brain of the entire identity system. When a user logs in anywhere on the domain, their credentials are checked against the Domain Controller. When you create a user account, you create it on the Domain Controller. There is usually more than one in an enterprise for redundancy, but we are building one here. Everything that joins your network will trust this server to make authentication decisions.


<img width="1237" height="898" alt="image" src="https://github.com/user-attachments/assets/dbc8d892-a6ac-4f51-a87b-d01cd912b14e" />

In Server Manager, click Manage → Add Roles and Features. Click Next through the wizard until you reach Server Roles. Check Active Directory Domain Services. When prompted, click Add Features to include the management tools. Click Next through the remaining pages and click Install. Wait for installation to complete — takes 2–3 minutes. When complete, click Close — do not restart yet

# Alternatively, run this in PowerShell to install the role:
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Run this in PowerShell immediately after installing AD DS
```
Install-WindowsFeature -Name GPMC
```
# Wait for it to complete, then close and reopen Server Manager

## Promote the Server to a Domain Controller
- In Server Manager, click the yellow warning flag at the top right
- Click Promote this server to a domain controller
- Select Add a new forest
- Set Root domain name to: lab.local
- Click Next — set a DSRM password (write it down — needed for disaster recovery only) - Administrator123!
- 
- Click through DNS Options and NetBIOS pages — accept the defaults
- Click Install — the server will automatically restart when complete

<img width="1358" height="750" alt="image" src="https://github.com/user-attachments/assets/fc9a7662-c7ce-46fe-acaa-903ad17eccb0" />

## Build the Organizational Structure
OUs to organise users, computers, and groups by department, location, or function — whatever makes sense for your organization. The real power of an OU is that you can link a Group Policy to it. That means every user or computer inside that OU automatically gets the policy applied.
```
# PowerShell — create all 5 OUs at once
New-ADOrganizationalUnit -Name "IT"        -Path "DC=lab,DC=local"
New-ADOrganizationalUnit -Name "Finance"   -Path "DC=lab,DC=local"
New-ADOrganizationalUnit -Name "HR"        -Path "DC=lab,DC=local"
New-ADOrganizationalUnit -Name "Sales"     -Path "DC=lab,DC=local"
New-ADOrganizationalUnit -Name "Computers" -Path "DC=lab,DC=local"
```
<img width="1304" height="926" alt="image" src="https://github.com/user-attachments/assets/0395e1f6-80bd-49a7-a1ba-2ffd752a23be" />

## Create Security Groups 
A Security Group is a container that holds user accounts. Instead of granting access to a resource (like a file share or an application) to individual users one by one, you grant access to a group
```
# PowerShell — create all 4 security groups
New-ADGroup -Name "IT_Admins"     -GroupScope Global -GroupCategory Security -Path "OU=IT,DC=lab,DC=local"
New-ADGroup -Name "Finance_Users" -GroupScope Global -GroupCategory Security -Path "OU=Finance,DC=lab,DC=local"
New-ADGroup -Name "HR_Users"      -GroupScope Global -GroupCategory Security -Path "OU=HR,DC=lab,DC=local"
New-ADGroup -Name "Sales_Users"   -GroupScope Global -GroupCategory Security -Path "OU=Sales,DC=lab,DC=local"
```

## Create User Accounts 
```
# Step 1 — define the password variable first
$password = ConvertTo-SecureString "Welcome@2026!" -AsPlainText -Force
 
# Step 2 — create all 4 users (Name parameter is required)
New-ADUser -Name "alice.chen" -GivenName "Alice" -Surname "Chen" `
  -SamAccountName "alice.chen" -UserPrincipalName "alice.chen@lab.local" `
  -Path "OU=IT,DC=lab,DC=local" -AccountPassword $password -Enabled $true
 
New-ADUser -Name "bob.patel" -GivenName "Bob" -Surname "Patel" `
  -SamAccountName "bob.patel" -UserPrincipalName "bob.patel@lab.local" `
  -Path "OU=Finance,DC=lab,DC=local" -AccountPassword $password -Enabled $true
 
New-ADUser -Name "carol.jones" -GivenName "Carol" -Surname "Jones" `
  -SamAccountName "carol.jones" -UserPrincipalName "carol.jones@lab.local" `
  -Path "OU=HR,DC=lab,DC=local" -AccountPassword $password -Enabled $true
 
New-ADUser -Name "david.smith" -GivenName "David" -Surname "Smith" `
  -SamAccountName "david.smith" -UserPrincipalName "david.smith@lab.local" `
  -Path "OU=Sales,DC=lab,DC=local" -AccountPassword $password -Enabled $true
 
# Step 3 — add each user to their department group
Add-ADGroupMember -Identity "IT_Admins"     -Members "alice.chen"
Add-ADGroupMember -Identity "Finance_Users" -Members "bob.patel"
Add-ADGroupMember -Identity "HR_Users"      -Members "carol.jones"
Add-ADGroupMember -Identity "Sales_Users"   -Members "david.smith"
```
## Configure Group Policy Management
Group Policy is how you enforce settings across every machine and user in the domain without logging into each one individually. Open Group Policy Management from the Tools menu in Server Manager.

Expand Forest: lab.local → Domains → lab.local in Group Policy Management
Right-click the IT OU → Create a GPO in this domain and link it here
Name it: IT Security Policy
Right-click the new GPO → Edit
Configure the settings in the table below

<img width="1457" height="966" alt="image" src="https://github.com/user-attachments/assets/0cf89e04-f765-43da-8db7-0d2c5fd4e7c1" />
<br>

<img width="637" height="273" alt="image" src="https://github.com/user-attachments/assets/5aff4339-0cde-4cdc-afcb-bb0c37e4249e" />

### Defining the minimum password length
<img width="1176" height="892" alt="image" src="https://github.com/user-attachments/assets/a96c1146-706b-4592-94d4-7051bf9b0c09" />
<br>
### All removable storage classes;deny all access
<img width="1390" height="1116" alt="image" src="https://github.com/user-attachments/assets/02da0840-ed07-4fa8-8771-2603ba6e8e99" />
Test your GPO: Join the second VM to lab.local, move its computer account into the IT OU, run gpupdate /force, log in as alice.chen and verify the screen lock policy takes effect.

Make sure link is enabled and that the policy in enforced.


<img width="791" height="335" alt="image" src="https://github.com/user-attachments/assets/cfa396b0-9673-4f50-804d-148f8ae1d396" />
```
Still show that the lockout policy wasn't enforced
```
<br>
Tried the following command below instead 
```
net accounts /lockoutthreshold:5 /domain
```
<img width="1076" height="421" alt="image" src="https://github.com/user-attachments/assets/88e684cf-cb18-4119-b8d8-b3d237d33fd2" />

### GPO is successfully being enforce 
<img width="867" height="604" alt="image" src="https://github.com/user-attachments/assets/9c59ae74-7e45-404b-8ef4-8da6d3462288" />


# SPLUNK BIND
Creating the bind user for Splunk LDAP configuration
##  Add to built-in read-only group
```
Add-ADGroupMember -Identity "Read-Only Domain Controllers" -Members "svc-splunk-bind"
```
