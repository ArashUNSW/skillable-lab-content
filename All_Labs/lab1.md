Lab 01 - BlueWave Clinic Environment Orientation and Case Study Setup
Estimated Time
60–90 minutes
---
Learning Objectives
By the end of this lab, you will be able to:
Identify the two virtual machines used in the BlueWave Clinic cyber operations lab.
Use Windows PowerShell to collect hostname, username, and IP address information.
Use Ubuntu Terminal to collect hostname, username, and IP address information.
Confirm basic network connectivity between the Windows endpoint and Ubuntu SOC server.
Create evidence folders on Windows and Ubuntu.
Review the preloaded lab file locations.
Create a Lab 01 evidence notes file.
Save basic environment information for later Elastic and Kibana labs.
---
Scenario
BlueWave Clinic is a small healthcare organisation that is building a basic Security Operations Centre, also called a SOC.
You are a junior cyber operations analyst. Your first task is to become familiar with the lab environment before collecting logs or investigating events.
The lab uses two virtual machines:
Machine	Role
WIN11-CLIENT	Windows 11 endpoint and analyst workstation
UBUNTU-SOC	Ubuntu SOC server that will later run Elastic, Kibana, and Fleet
In later labs, WIN11-CLIENT will send Windows and Sysmon logs to UBUNTU-SOC. You will then use Kibana to search and analyse those logs.
In this lab, you will collect basic system information, confirm the two machines can communicate, and create evidence folders for future investigation work.
> [!note]
> This lab does not use Elastic yet. Elastic and Kibana orientation begins in Lab 02.
> [!note]
> This lab does not require internet access.
---
Required Machines
Machine	Purpose
WIN11-CLIENT	Collect Windows system information and create Windows evidence files
UBUNTU-SOC	Collect Ubuntu system information and create Ubuntu evidence files
---
Required Files
No special files are required for this lab.
You will check that the following folders exist, but you will not run any tools from them yet:
Windows lab folders
```text
C:\LabFiles
C:\LabFiles\Tools
C:\LabFiles\Simulators
C:\LabFiles\Logs
C:\LabFiles\Templates
```
Ubuntu lab folders
```text
/home/student/labfiles
/home/student/labfiles/logs
/home/student/labfiles/scripts
/home/student/labfiles/templates
/home/student/labfiles/capstone
```
---
Key Terms
Term	Meaning
Endpoint	A user workstation or device that can generate logs
SOC	Security Operations Centre
Hostname	The name of a computer on a network
IP address	A network address used by computers to communicate
PowerShell	A Windows command-line tool
Terminal	A Linux command-line tool
Evidence	Information saved during a lab or investigation
Connectivity	The ability of two machines to communicate
Elastic	A platform used to store, search, and analyse events
Kibana	The web interface used to search and review Elastic data
---
Task 1 - Confirm You Are Signed In to WIN11-CLIENT
Open the WIN11-CLIENT virtual machine.
Sign in using the lab credentials provided by your instructor or Skillable environment.
Confirm that the desktop loads.
Select the Windows Start menu.
Search for:
```text
About your PC
```
Open About your PC.
Confirm that the operating system is Windows 11.
Close the Settings window.
Expected result:
```text
You should be signed in to the Windows 11 endpoint.
```
> [!note]
> WIN11-CLIENT is the endpoint that will generate Windows logs in later labs.
> [!hint]
> If the desktop is slow to load, wait a few moments before opening applications.
---
Task 2 - Open Windows PowerShell
On WIN11-CLIENT, select the Windows Start menu.
Search for:
```text
PowerShell
```
Open Windows PowerShell.
Expected result:
```text
A PowerShell window should open.
```
The prompt may look similar to this:
```text
PS C:\Users\student>
```
> [!note]
> You will use PowerShell to collect Windows hostname, username, and network information.
---
Task 3 - Identify the Windows Hostname
In the PowerShell window, run this command:
```powershell
hostname
```
Expected result:
```text
WIN11-CLIENT
```
If your hostname is different, write down the hostname shown in your lab environment.
> [!note]
> The hostname helps analysts identify which computer generated a log event.
---
Task 4 - Identify the Windows Logged-In User
In the same PowerShell window, run this command:
```powershell
whoami
```
Expected result:
```text
win11-client\student
```
Your output may be slightly different.
Record the exact username shown.
> [!hint]
> The `whoami` command shows the account currently signed in to Windows.
---
Task 5 - Confirm the Windows Computer Name Variable
Run this command in PowerShell:
```powershell
$env:COMPUTERNAME
```
Expected result:
```text
WIN11-CLIENT
```
This value should match the hostname from Task 3.
> [!note]
> In later labs, host information may appear in Kibana as fields such as `host.name`.
---
Task 6 - Identify the Windows IPv4 Address
In PowerShell, run this command:
```powershell
ipconfig
```
Look for the active network adapter.
Find the line named:
```text
IPv4 Address
```
Your IP address may look similar to one of these examples:
```text
10.1.1.20
172.16.1.20
192.168.1.20
```
Now run this command for a cleaner IPv4 view:
```powershell
Get-NetIPAddress -AddressFamily IPv4 | Where-Object {$_.IPAddress -notlike "127.*"} | Select-Object InterfaceAlias, IPAddress, PrefixLength
```
Expected result:
```text
InterfaceAlias        IPAddress       PrefixLength
--------------        ---------       ------------
Ethernet              10.1.1.20       24
```
Record the Windows IPv4 address.
> [!alert]
> Do not record `127.0.0.1` as the Windows lab IP address. That is the local loopback address.
> [!hint]
> If several adapters are listed, use the active Ethernet adapter. Ignore adapters that show `Media disconnected`.
---
Task 7 - Confirm You Are Signed In to UBUNTU-SOC
Open the UBUNTU-SOC virtual machine.
Sign in using the lab credentials provided by your instructor or Skillable environment.
Confirm that the Ubuntu desktop or terminal environment loads.
Expected result:
```text
You should be signed in to the Ubuntu SOC server.
```
> [!note]
> UBUNTU-SOC will later host Elasticsearch, Kibana, and Fleet.
---
Task 8 - Open Ubuntu Terminal
On UBUNTU-SOC, open Terminal.
Expected result:
```text
A Terminal window should open.
```
The prompt may look similar to this:
```text
student@UBUNTU-SOC:~$
```
> [!hint]
> If you cannot find Terminal, use the application search and type `Terminal`.
---
Task 9 - Identify the Ubuntu Hostname
In Ubuntu Terminal, run this command:
```bash
hostname
```
Expected result:
```text
UBUNTU-SOC
```
If your hostname is different, write down the hostname shown in your lab environment.
---
Task 10 - Identify the Ubuntu Logged-In User
Run this command in Ubuntu Terminal:
```bash
whoami
```
Expected result:
```text
student
```
Record the username shown.
---
Task 11 - Identify the Ubuntu Home Directory
Run this command in Ubuntu Terminal:
```bash
pwd
```
Expected result:
```text
/home/student
```
> [!note]
> The `pwd` command means print working directory. It shows your current folder location.
---
Task 12 - Identify the Ubuntu IPv4 Address
Run this command in Ubuntu Terminal:
```bash
hostname -I
```
Expected result:
```text
10.1.1.10
```
Your IP address may be different.
Now run this command to view more network detail:
```bash
ip addr show
```
Look for the active network interface.
Find the IP address after the word:
```text
inet
```
Example:
```text
inet 10.1.1.10/24
```
Record the Ubuntu IPv4 address.
> [!alert]
> Do not record `127.0.0.1` as the Ubuntu lab IP address. That is the local loopback address.
> [!hint]
> If more than one IP address appears, record the first non-loopback IPv4 address used by the lab network.
---
Task 13 - Test Connectivity from Windows to Ubuntu
Return to WIN11-CLIENT.
In PowerShell, run the following command.
Replace `<UBUNTU-SOC-IP>` with the Ubuntu IP address you recorded.
```powershell
ping <UBUNTU-SOC-IP>
```
Example:
```powershell
ping 10.1.1.10
```
Expected result:
```text
Reply from 10.1.1.10: bytes=32 time<1ms TTL=64
```
If ping succeeds, record that Windows can reach Ubuntu.
Now run this command:
```powershell
Test-NetConnection <UBUNTU-SOC-IP>
```
Example:
```powershell
Test-NetConnection 10.1.1.10
```
Expected result:
```text
PingSucceeded : True
```
> [!alert]
> Some lab environments block ping traffic. If ping fails, record the result exactly as shown. Do not change firewall settings in this lab.
---
Task 14 - Test Connectivity from Ubuntu to Windows
Return to UBUNTU-SOC.
In Terminal, run the following command.
Replace `<WIN11-CLIENT-IP>` with the Windows IP address you recorded.
```bash
ping -c 4 <WIN11-CLIENT-IP>
```
Example:
```bash
ping -c 4 10.1.1.20
```
Expected result:
```text
4 packets transmitted, 4 received, 0% packet loss
```
If ping fails, record the result exactly as shown.
> [!note]
> A failed ping does not always mean the lab is broken. Windows firewall settings may block ICMP traffic.
---
Task 15 - Create the Windows Evidence Folder
Return to WIN11-CLIENT.
In PowerShell, run this command:
```powershell
New-Item -Path "C:\BlueWave\Evidence" -ItemType Directory -Force
```
Confirm the folder exists:
```powershell
Test-Path "C:\BlueWave\Evidence"
```
Expected result:
```text
True
```
Open File Explorer and browse to:
```text
C:\BlueWave\Evidence
```
Expected result:
```text
The Evidence folder should open.
```
> [!note]
> You will save investigation notes, timelines, screenshots, and reports in this folder throughout the course.
---
Task 16 - Create the Ubuntu Evidence Folder
Return to UBUNTU-SOC.
In Terminal, run this command:
```bash
mkdir -p /home/student/bluewave/evidence
```
Confirm the folder exists:
```bash
ls -ld /home/student/bluewave/evidence
```
Expected result:
```text
drwxr-xr-x 2 student student 4096 May 15 10:00 /home/student/bluewave/evidence
```
> [!note]
> The exact date and time may be different.
---
Task 17 - Check the Windows Lab Files Folder
Return to WIN11-CLIENT.
In PowerShell, run this command:
```powershell
Test-Path "C:\LabFiles"
```
Expected result:
```text
True
```
Now list the folder contents:
```powershell
Get-ChildItem "C:\LabFiles"
```
Expected result:
```text
Tools
Simulators
Logs
Templates
```
> [!note]
> The course assumes tools and files are already preloaded. You should not download anything from the internet.
> [!alert]
> Do not run `BlueWaveActivitySimulator.exe` in this lab. The simulator is introduced later.
---
Task 18 - Check the Ubuntu Lab Files Folder
Return to UBUNTU-SOC.
In Terminal, run this command:
```bash
ls -la /home/student/labfiles
```
Expected result:
```text
logs
scripts
templates
capstone
```
If one or more folders are missing, record what is missing.
> [!note]
> You will use Ubuntu lab files in later Elastic, Kibana, and capstone activities.
---
Task 19 - Create the Lab 01 Evidence Notes File on Windows
Return to WIN11-CLIENT.
Open Notepad.
Copy the following template into Notepad:
```text
BlueWave Clinic Cyber Operations with Elastic
Lab 01 - Environment Orientation and Case Study Setup

Student Name:
Date:

1. Windows Endpoint

Windows hostname:
Windows logged-in user:
Windows COMPUTERNAME value:
Windows IPv4 address:
Windows network adapter:
Windows evidence folder:

2. Ubuntu SOC Server

Ubuntu hostname:
Ubuntu logged-in user:
Ubuntu home directory:
Ubuntu IPv4 address:
Ubuntu network interface:
Ubuntu evidence folder:

3. Connectivity Tests

Windows-to-Ubuntu command:
Windows-to-Ubuntu result:

Ubuntu-to-Windows command:
Ubuntu-to-Windows result:

4. Lab File Locations

Windows lab files path:
Windows lab folders seen:

Ubuntu lab files path:
Ubuntu lab folders seen:

5. Lab Summary

Write 3 to 5 sentences explaining what you learned about the BlueWave Clinic lab environment.
```
Fill in the missing information.
Save the file in this folder:
```text
C:\BlueWave\Evidence
```
Use this filename:
```text
Lab01-Environment-Notes.txt
```
---
Task 20 - Confirm the Windows Evidence Notes File Exists
In PowerShell, run this command:
```powershell
Test-Path "C:\BlueWave\Evidence\Lab01-Environment-Notes.txt"
```
Expected result:
```text
True
```
Open the file in Notepad and confirm your notes were saved.
> [!hint]
> If the command returns `False`, check that you saved the file in the correct folder and used the exact filename.
---
Task 21 - Create a Lab 01 Evidence Copy on Ubuntu
Return to UBUNTU-SOC.
In Terminal, run this command:
```bash
nano /home/student/bluewave/evidence/Lab01-Environment-Notes.txt
```
Enter the following template:
```text
BlueWave Clinic Cyber Operations with Elastic
Lab 01 - Ubuntu Evidence Copy

Windows hostname:
Windows IPv4 address:

Ubuntu hostname:
Ubuntu IPv4 address:

Windows evidence folder:
C:\BlueWave\Evidence

Ubuntu evidence folder:
/home/student/bluewave/evidence

Connectivity summary:
```
Fill in the missing information.
To save in nano:
Press Ctrl + O.
Press Enter.
Press Ctrl + X.
Confirm the file exists:
```bash
ls -l /home/student/bluewave/evidence/Lab01-Environment-Notes.txt
```
Expected result:
```text
/home/student/bluewave/evidence/Lab01-Environment-Notes.txt
```
Display the file contents:
```bash
cat /home/student/bluewave/evidence/Lab01-Environment-Notes.txt
```
Expected result:
```text
Your Ubuntu evidence copy should display in Terminal.
```
---
Task 22 - Capture Required Screenshots
Capture screenshots as directed by your instructor or Skillable platform.
You should capture evidence of the following:
Windows hostname and logged-in user.
Windows IPv4 address.
Ubuntu hostname and logged-in user.
Ubuntu IPv4 address.
Windows-to-Ubuntu connectivity test.
Ubuntu-to-Windows connectivity test.
Windows evidence folder.
Ubuntu evidence folder.
Windows lab files folder listing.
Ubuntu lab files folder listing.
Completed Windows evidence notes file.
> [!note]
> Screenshots help prove that you completed the work and saw the expected results.
---
Task 23 - Validate Your Work
On WIN11-CLIENT, run this command:
```powershell
Test-Path "C:\BlueWave\Evidence\Lab01-Environment-Notes.txt"
```
Expected result:
```text
True
```
On UBUNTU-SOC, run this command:
```bash
test -f /home/student/bluewave/evidence/Lab01-Environment-Notes.txt && echo "Ubuntu evidence file exists"
```
Expected result:
```text
Ubuntu evidence file exists
```
On WIN11-CLIENT, confirm that your evidence file includes:
Windows hostname.
Windows logged-in user.
Windows IPv4 address.
Ubuntu hostname.
Ubuntu logged-in user.
Ubuntu IPv4 address.
Connectivity test results.
Evidence folder locations.
Lab file folder check results.
A short lab summary.
---
Validation Checklist
Confirm each item before finishing the lab:
[ ] I signed in to WIN11-CLIENT.
[ ] I signed in to UBUNTU-SOC.
[ ] I opened Windows PowerShell.
[ ] I opened Ubuntu Terminal.
[ ] I recorded the Windows hostname.
[ ] I recorded the Windows logged-in user.
[ ] I recorded the Windows IPv4 address.
[ ] I recorded the Ubuntu hostname.
[ ] I recorded the Ubuntu logged-in user.
[ ] I recorded the Ubuntu IPv4 address.
[ ] I tested connectivity from Windows to Ubuntu.
[ ] I tested connectivity from Ubuntu to Windows.
[ ] I created `C:\BlueWave\Evidence`.
[ ] I created `/home/student/bluewave/evidence`.
[ ] I checked `C:\LabFiles`.
[ ] I checked `/home/student/labfiles`.
[ ] I created `Lab01-Environment-Notes.txt` on Windows.
[ ] I created `Lab01-Environment-Notes.txt` on Ubuntu.
[ ] I captured the required screenshots.
[ ] I reviewed my evidence notes for completeness.
---
Troubleshooting
Problem: PowerShell does not open
Open the Windows Start menu and search for:
```text
PowerShell
```
Open Windows PowerShell.
If Windows Terminal opens instead, that is acceptable if it gives you a PowerShell prompt.
---
Problem: Ubuntu Terminal does not open
Use the Ubuntu application search and type:
```text
Terminal
```
Open the Terminal application.
---
Problem: `hostname` does not show WIN11-CLIENT
Record the exact hostname shown.
Tell your instructor if the hostname is very different from the lab instructions.
---
Problem: `hostname` does not show UBUNTU-SOC
Record the exact hostname shown.
Tell your instructor if the hostname is very different from the lab instructions.
---
Problem: `ipconfig` shows many network adapters
Look for the active Ethernet adapter.
Ignore adapters that show:
```text
Media disconnected
```
Do not use:
```text
127.0.0.1
```
---
Problem: `hostname -I` shows more than one IP address
Record the first non-loopback IPv4 address.
You can also run:
```bash
ip addr show
```
Look for an address after:
```text
inet
```
Do not use:
```text
127.0.0.1
```
---
Problem: Ping from Windows to Ubuntu fails
Check that you typed the correct Ubuntu IP address.
Run the command again:
```powershell
ping <UBUNTU-SOC-IP>
```
If it still fails, ICMP may be blocked.
Record the result in your evidence file.
Do not change firewall settings in this lab.
---
Problem: Ping from Ubuntu to Windows fails
Check that you typed the correct Windows IP address.
Run the command again:
```bash
ping -c 4 <WIN11-CLIENT-IP>
```
If it still fails, Windows firewall may be blocking ping.
Record the result in your evidence file.
Do not change firewall settings in this lab.
---
Problem: The Windows evidence folder was not created
Run this command again in PowerShell:
```powershell
New-Item -Path "C:\BlueWave\Evidence" -ItemType Directory -Force
```
Then confirm it exists:
```powershell
Test-Path "C:\BlueWave\Evidence"
```
Expected result:
```text
True
```
---
Problem: The Ubuntu evidence folder was not created
Run this command again in Terminal:
```bash
mkdir -p /home/student/bluewave/evidence
```
Then confirm it exists:
```bash
ls -ld /home/student/bluewave/evidence
```
---
Problem: The Windows evidence notes file is missing
Check that you saved the file as:
```text
Lab01-Environment-Notes.txt
```
Check that you saved it in:
```text
C:\BlueWave\Evidence
```
Confirm with:
```powershell
Test-Path "C:\BlueWave\Evidence\Lab01-Environment-Notes.txt"
```
Expected result:
```text
True
```
---
Problem: The Ubuntu evidence notes file is missing
Create it again with:
```bash
nano /home/student/bluewave/evidence/Lab01-Environment-Notes.txt
```
Then confirm it exists:
```bash
ls -l /home/student/bluewave/evidence/Lab01-Environment-Notes.txt
```
---
Problem: `C:\LabFiles` is missing
Check the path carefully:
```text
C:\LabFiles
```
If it is still missing, record the issue and notify your instructor.
Do not download files from the internet.
---
Problem: `/home/student/labfiles` is missing
Check the path carefully:
```text
/home/student/labfiles
```
If it is still missing, record the issue and notify your instructor.
Do not download files from the internet.
---
Knowledge Check
Answer the following questions:
What is the role of WIN11-CLIENT in the BlueWave Clinic lab?
What is the role of UBUNTU-SOC in the BlueWave Clinic lab?
What PowerShell command shows the Windows hostname?
What Ubuntu command shows the current logged-in user?
Why should you avoid recording `127.0.0.1` as the lab IP address?
What folder is used to store Windows evidence?
What folder is used to store Ubuntu evidence?
What does a connectivity test help confirm?
Why is it important to record hostnames during an investigation?
Why should you not download tools from the internet in this course?
---
Deliverables
Submit or retain the following items as directed by your instructor:
Deliverable	Location
Windows Lab 01 evidence notes	`C:\BlueWave\Evidence\Lab01-Environment-Notes.txt`
Ubuntu Lab 01 evidence copy	`/home/student/bluewave/evidence/Lab01-Environment-Notes.txt`
Screenshot of Windows hostname and user	Submitted through Skillable instructions
Screenshot of Windows IPv4 address	Submitted through Skillable instructions
Screenshot of Ubuntu hostname and user	Submitted through Skillable instructions
Screenshot of Ubuntu IPv4 address	Submitted through Skillable instructions
Screenshot of Windows-to-Ubuntu connectivity test	Submitted through Skillable instructions
Screenshot of Ubuntu-to-Windows connectivity test	Submitted through Skillable instructions
Screenshot of Windows evidence folder	Submitted through Skillable instructions
Screenshot of Ubuntu evidence folder	Submitted through Skillable instructions
Screenshot of Windows lab files folder	Submitted through Skillable instructions
Screenshot of Ubuntu lab files folder	Submitted through Skillable instructions
---
Summary
In this lab, you completed the following tasks:
Reviewed the BlueWave Clinic two-machine lab environment.
Confirmed the role of WIN11-CLIENT.
Confirmed the role of UBUNTU-SOC.
Used PowerShell to collect Windows system information.
Used Ubuntu Terminal to collect Linux system information.
Identified hostnames, usernames, and IPv4 addresses.
Tested basic connectivity between the two virtual machines.
Created Windows and Ubuntu evidence folders.
Checked the preloaded lab file locations.
Created Lab 01 evidence notes.
You are now ready to begin Lab 02, where you will open Elastic and Kibana and learn the basic SOC interface.
---
Instructor Notes
Expected Answers
WIN11-CLIENT is the Windows 11 endpoint and analyst workstation.
UBUNTU-SOC is the Ubuntu SOC server that will later host Elastic and Kibana.
The Windows hostname command is:
```powershell
hostname
```
The Ubuntu logged-in user command is:
```bash
whoami
```
`127.0.0.1` is the loopback address and does not identify the machine on the lab network.
The Windows evidence folder is:
```text
C:\BlueWave\Evidence
```
The Ubuntu evidence folder is:
```text
/home/student/bluewave/evidence
```
A connectivity test helps confirm whether the two lab machines can communicate.
Hostnames help analysts connect events to the correct system.
Tools are preloaded in the Skillable environment, and the course does not require internet access.
---
Expected Evidence Files
Students should create:
```text
C:\BlueWave\Evidence\Lab01-Environment-Notes.txt
```
And:
```text
/home/student/bluewave/evidence/Lab01-Environment-Notes.txt
```
---
Expected Command Outputs
Windows hostname should usually be:
```text
WIN11-CLIENT
```
Ubuntu hostname should usually be:
```text
UBUNTU-SOC
```
Windows evidence folder validation should return:
```text
True
```
Ubuntu evidence validation should return:
```text
Ubuntu evidence file exists
```
---
Common Student Mistakes
Mistake	Guidance
Student records `127.0.0.1` as an IP address	Remind the student that this is the loopback address
Student runs Ubuntu commands in PowerShell	Remind the student to use Terminal on UBUNTU-SOC
Student runs PowerShell commands in Ubuntu Terminal	Remind the student to use PowerShell on WIN11-CLIENT
Student forgets to save the evidence file	Have the student reopen Notepad and save again
Student worries about failed ping	Explain that ICMP may be blocked and the result should be documented
Student runs the simulator early	Remind the student that simulator activity begins in a later lab
Student tries to access Elastic in Lab 01	Explain that Elastic orientation begins in Lab 02
---
Grading Guidance
Suggested grading allocation:
Criteria	Points
Windows hostname, user, and IP recorded	15
Ubuntu hostname, user, and IP recorded	15
Connectivity tests attempted and documented	15
Windows evidence folder created	10
Ubuntu evidence folder created	10
Lab file locations checked	10
Evidence notes completed clearly	15
Screenshots captured	10
Total	100
Do not heavily penalise failed ping results if the student used the correct commands and documented the result.
---
Elastic Basic License Reminder
Elastic is not used in this lab.
For the course, use only self-managed Elastic with the free Basic license.
Do not require Elastic Cloud.
Do not require paid subscriptions.
Do not require external internet access.
---
End of Lab 01.
