<p align="center">
<img src="https://i.imgur.com/aMMGyHQ.jpeg" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />

<h1>Creating Users, Group Policy, and Managing Accounts with Active Directory in Azure</h1>

 ### [YouTube Demonstration](https://youtube.com)

<h2>Description</h2>
This project will demonstrate how to configure Remote Desktop for non-administrative users, automate user creation with PowerShell, and manage group policies. Additionally, I’ll cover account lockouts and log monitoring to simulate a real-life IT environment. Join me as I dive deeper into deploying Active Directory and enhancing system management skills!  <br/>
<br />

This is a continuation of the [Active Directory: Deploying Active Directory in Azure](https://github.com/ericknguyen413/deploying-active-directory-in-azure) project. <br />
<br />
You can find the Powershell script I run in this project [here.](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) 
<br />


<h2>Environments and Utilities Used</h2>

- <b>Microsoft Azure</b>
- <b>Virtual Machines</b>
- <b>Remote Desktop Connection</b>
- <b>Active Directory</b>

<h2>Operating Systems Used </h2>

- <b>Windows Server </b>
- <b>Windows 10</b>

<h2>Project Walk-through:</h2>

To start, log into client-1 as the domain admin (jane_admin), using Remote Desktop Connection:

![image](https://github.com/user-attachments/assets/75f1d96a-cbf5-43da-8a2e-635ef44ae011)

I want to allow Remote Desktop for non-administrative users. So, right-click on the start button > System > on the right-side click "Remote Desktop" > "Select users that can remotely access this PC":

![image](https://github.com/user-attachments/assets/00c9a411-d20b-4bd2-b1a6-65fada57f5af)

Here, enter "Domain Users" > Check Names > OK > OK. Now all domain users can access this client via Remote Desktop:

![image](https://github.com/user-attachments/assets/704f1b86-6aa8-40e1-9f9b-c5b51b8772be)

Next, we'll create a bunch of users in Powershell. To start, log into the DC (Domain Controller) as our admin (jane_admin):

![image](https://github.com/user-attachments/assets/9e79c196-c2f7-4ae6-ae71-b1ae37e4d1a1)

Once logged in, open Powershell ISE as an admin and start a new script:

![image](https://github.com/user-attachments/assets/2f376174-14a3-4f60-a893-8a556e9fa79e)

Use Ctrl + S and save this like so:

![image](https://github.com/user-attachments/assets/533446c5-3dc9-48a5-88cf-3e212e39995f)

Now, we can copy and paste the user creation script in the top section and click the "Run" button which has a green play symbol located in the top bar:

![image](https://github.com/user-attachments/assets/de83f94d-34eb-482a-b069-64d25a75dfcd)

Powershell will begin to run the script and create random users and place them in the OU (Organizational Unit), we created in the previous section of this project, named "_EMPLOYEES". We can check by searching "Active Directory Users and Computers" > mydomain.com > _EMPLOYEES. Here we will pick a user and log into client-1 using this username and the password the script sets for all of these users which is "Password1":

![image](https://github.com/user-attachments/assets/608287ef-cb51-4c8a-8df6-9b1b00e13e30)

Now, log out of Jane's account on client-1:

![image](https://github.com/user-attachments/assets/e2726b83-d94e-4d49-b92f-56be0389dbcf)

We can log back into client-1 using our newly created user, making sure to specify the context of which we want to log in by adding "mydomain.com\" before the username:

![image](https://github.com/user-attachments/assets/bfa057b3-ee80-4861-8ab2-5bda600997b1)

When we're logged in we can see a user folder for our user if we click on file explorer > C: > Users. We can also see the folder for the other users that have signed into this client. However, we cannot access them, because we don't have those permissions as a user:

![image](https://github.com/user-attachments/assets/8b25f0b3-57ec-46e1-afd9-b76b96e13d5d)

Now, let's log out of this user, because we are going to set an account lockout group policy and attempt to lock this user out:

![image](https://github.com/user-attachments/assets/47e037f2-6ec5-4211-a319-133073bb90eb)

Go back to our DC and right click the start button > Run > type "gpmc.msc" to bring up the Group Policy Managment Console:



Expand Domains > mydomain.com > select Default Domain Policy:

Under Computer Configuration expand Policies > Window Settings > Security Settings > select Account Lockout Policy:

Here we'll select "Account lockout threshhold" and choose to make the account lockout after 5 invalid attempts. Make sure to hit Apply:

You can double-check this by going to the group policy settings and scrolling down to view the lockout policy:

The policy will update automatically, but it will take some time. We can force the update on client-1 by signing into it as our admin and running "gpupdate /force" in the command line:

Now, we can sign out of client-1 and attempt to sign back into it with our user with an invalid password 5 times. A lockout message will appear:

To unlock the users account, head back to the DC > Active Directory Users and Computers > mydomain.com > _EMPLOYEES > double-click on the locked out user > Account tab > check the "Unlock account" box:

(Alternatively you could right-click on the user > Reset Password and there will be a "Unlock users account" option. That way you can change the password at the same time you unlock it):

Now our user can sign into clinet-1 as normal:

To disable users, go to the DC and in Active Directory Users and Computers > mydomain.com > _EMPLOYEES > right-click on user you want to disable > Disable:

If we sign out of client-1 now, and try to sign back in using the disabled account, this message will appear:

To enable the user again go back to the DC and right-click again on the user and select Enable:

The user can now log into client-1, so we'll do that and once we are logged in we'll view some logs through the event viewer. To do so, search for "Event Viewer" in the start search bar > Windows Logs > Security. You'll notice we cannot view these:

This is because we are not an admin. Not to worry, we don't need to sign out of this whole client and sign in as Jane (our admin). We already know her credentials. We just need to close out of this window and open it again, but this time, as an administrator. It will have a pop-up asking for admin credentials:

Now, if we navigate back to the security log page, we can view them:

Here we can see a bunch of different information like successful and failed log on attempts, who attempted them and from what IP address, along with the date and time they happened:

<h2>Active Directory: Creating Users, Group Policy, and Managing Accounts in Azure is Now Complete </h2>

<b>We've successfully configured Remote Desktop for non-administrative users, automated user creation with PowerShell, and managed group policies. Additionally, we covered account lockouts and log monitoring to simulate a real-life IT environment!  </b>
