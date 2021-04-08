# WindowsAnsible
This script is designed to be run on a new machine running Windows 10 latest release. In order to prep this machine for use with Ansible, the WinRM Listener needs to be configured on the Windows node. We also will require a local Administrator account for Ansible to perform it's magic. I have setup a student user with the password student for this deployment. WinRM setup can be accomplished with the following steps:
1. Open PowerShell as an Administrator
2. Run the following commands:
>     $url = "https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"
>     $file = "$env:temp\ConfigureRemotingForAnsible.ps1"
>     (New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)
>     powershell.exe -ExecutionPolicy ByPass -File $file

3. Once Complete, check the listener and verify it is listening and on what IP address you can connect together on. 
>     winrm enumerate winrm/config/Listener
You will need to add this IP address to your /etc/hosts file so your machine can connect to the Windows machine by name. Here is an example output:
>     Listener
>     Address = *
>     Transport = HTTP
>     Port = 5985
>     Hostname
>     Enabled = true
>     URLPrefix = wsman
>     CertificateThumbprint
>     ListeningOn = 10.0.2.15, 127.0.0.1, 192.168.56.155, ::1, fe80::5efe:10.0.2.15%6, fe80::5efe:192.168.56.155%8, fe80::
>     ffff:ffff:fffe%2, fe80::203d:7d97:c2ed:ec78%3, fe80::e8ea:d765:2c69:7756%7

>     Listener
>     Address = *
>     Transport = HTTPS
>     Port = 5986
>     Hostname = SERVER2016
>     Enabled = true
>     URLPrefix = wsman
>     CertificateThumbprint = E6CDAA82EEAF2ECE8546E05DB7F3E01AA47D76CE
>     ListeningOn = 10.0.2.15, 127.0.0.1, 192.168.56.155, ::1, fe80::5efe:10.0.2.15%6, fe80::5efe:192.168.56.155%8, fe80::
>     ffff:ffff:fffe%2, fe80::203d:7d97:c2ed:ec78%3, fe80::e8ea:d765:2c69:7756%7

As you can see from this example, you would add the ip 192.168.56.155 to the /etc/hosts file on the control node.

sudo vim /etc/hosts

>   192.168.56.155 <name_chosen>

Make sure any name or names of your windows nodes are added to your inventory so it will properly deploy.

4. It may also be needed to adjust the firewall settings. The script you run on the nodes starts the listener and creates some firewall rules. I had to set the network interface to use the private firewall policy profile to gain connectivity to the windows hosts. 
