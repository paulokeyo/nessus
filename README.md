## Lab Outline: Azure Vulnerability Management using Nessus

### Prerequisites
- Computer with Internet
- Microsoft Azure Account
  
### Create Free Azure Account
Azure offers $200 free credits for the 1 month. We will use them for this lab 

<img src= "https://github.com/paulokeyo/nessus/blob/main/assets/create%20free%20azure%20account.png?raw=true" />

1. Sign up: [Azure Free Account](https://azure.microsoft.com/en-us/free/)
2. Login: [Azure Portal](https://portal.azure.com)
   
### Prepare Vulnerability management scanner
<img src = "https://github.com/paulokeyo/nessus/blob/main/assets/prepare%20nessus.jpg?raw=true"/>

1. Go to [Azure Portal](https://portal.azure.com)
2. Navigate to the Marketplace and search for "Nessus"
3. Choose the "Start with a pre-set configuration" option and select the weakest configuration(since this is our personal project).
4. Click "Continue to Create VM"
5. Configure the VM:
   - Resource Group: Vulnerability-Management
   - VM Name: OpenVAS (Take note of the region and Vnet–consider East US 2)
   - Authentication: Username → azureuser / Cyberlab123!
   - Monitoring: Disable Boot Diagnostic
6. Click "Create" to create the VM.
7. Once the VM is created, SSH into it using PowerShell (Windows) or Terminal (MacOS) with the credentials created in step 5.
8. Wait until the deployment of OpenVAS is complete. Use the links provided on the terminal to access in the web app version of Nessus

### Create Client Virtual Machine and Make it Vulnerable
1. Go to Azure Portal
2. Search for Virtual Machines and create a new Virtual Machine.
3. Configure the VM:
   - Resource Group: Select “Vulnerability-Management”
   - VM Name: Win10-Vulnerable
   - Region: Same as the NessusVulnerabilityScanner VM (East US)
   - Virtual Network: Same as NessusVulnerabilityScanner
   - Image: Windows 10 Pro
   - Size: Any size with 2 vCPUs
   - Username: azureuser / Vulscanner1@#$
   - Networking: Same Vnet as NessusVulnerabilityScanner
4.	Create the VM.
5.	Once the VM is created, ensure you can RDP into it with the provided credentials.
6.	After logging in, make the VM vulnerable:
     - Disable the Windows Firewall
     - Gather up some [Old Software](https://drive.google.com/drive/folders/1n83ilCjZWZulbDdYnUe9wQPK2buY47_U?usp=sharing)
     - Install an Old Version of FireFox: Firefox Setup 97.0b5
     - Install an Old Version of VLC Player: vlc-1.1.7-win32
     - Install an Old Version of Adobe Reader: 10.0_AdbeRdr1000_en_US_1_
     - Restart the VM.
### Configure Nessus to Perform First Uncredentialed Scan against our Vulnerable VM
<img src = "https://github.com/paulokeyo/nessus/blob/main/assets/unauthenicated%20scan.jpg?raw=true"/>

1.	Login to Nessus Essentials and click on Create new Scan
2.	Among the scan templates, click on Basic Network Scan.
3.	Give the scan name “Azure VMs scan” and description of your choice.
4.	Input the Targets the private IP of the VM to be scanned or a range of IPs if several VMs in the network
5.	Take note of the credentials tab. We will add SMB credentials later.
6.	Save the Scan.
7.	Launch the scan – “Azure VMs scan".
8.	Once the scan is finished, click the scan name to see the vulnerabilities results.

### Make Configurations for Credentialed Scans (Within VM)
<img src = "https://github.com/paulokeyo/nessus/blob/main/assets/edit%20registry.jpg?raw=true"/>

1.	Disable Windows Firewall.
2.	Disable User Account Control.
3.	Enable Remote Registry.
4.	Set Registry Key:
     - Launch Registry Editor (regedit.exe) in "Run as administrator" mode.
     - Navigate to HKEY_LOCAL_MACHINE hive.
     - Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System key.
     - Create a new DWORD (32-bit) value with the following properties:
       - Name: LocalAccountTokenFilterPolicy
       - Value: 1
     - Close Registry Editor.
5.	Restart the VM.

### Make Configurations for Credentialed Scans (Nessus)
<img src="https://github.com/paulokeyo/nessus/blob/main/assets/authenicated%20scan.jpg?raw=true"/>

1.	Open the Nessus Essential web app and click on the scan we did earlier.
2.	Click on “Configure”.
3.	Under “Credentials” tab, select windows and input the your VM login credentials
      - Username: azureuser  password: Vulscanner1@#$
4.	Save.
5.	Navigate back to “My scans”
6.	Launch the scan

### Execute Credentialed Scan against our Vulnerable Windows VM
1.	Let the scan run to its completion
2.	After the scan is complete, Click on scan name “Azure VMs scan”, then click on “History” tab. Notice the two scans in the history
The first scan has 12 vulnerabilities while the second scan has 54 vulnerabilities

<img src= "https://github.com/paulokeyo/nessus/blob/main/assets/12%20vulnerabilities.jpg?raw=true"/>

<img src= "https://github.com/paulokeyo/nessus/blob/main/assets/56%20vulnerabilities.jpg?raw=true"/>

### Remediate Vulnerabilities
<img src = "https://github.com/paulokeyo/nessus/blob/main/assets/remediations.jpg?raw=true"/>

1.	Log back into your Win10-Vulnerable VM.
2.	Uninstall Adobe Reader, VLC Player, and Firefox.
3.	Restart the VM.
4.	Re-initiate the "Scan - Azure VMs Scan " scan and observe the results.

### Verify Remediations
<img src = "https://github.com/paulokeyo/nessus/blob/main/assets/remediated%20scan.jpg?raw=true"/>

1.	Note that there are no longer Vulnerabilities for FireFox, VLC Player, or Adobe Reader!

  - NOTE: Notice that we still have more Vulnerabilities (49) in the third scan after the remediation than the first scan. This is because we did not perform credentialed scan in the first scan (Credentailed scan provided Nessus more permissions and accesss into the VM). 
Remediate the remaining vulnerabilities to make the VM more secure. 




