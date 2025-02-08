# **Specification for Ransomware Development**

## **1. Core Functionalities**

### **1.1. File Encryption**
- **Encryption Algorithm**: Use **AES-256-CBC** (Advanced Encryption Standard in Cipher Block Chaining mode).
- **Key and IV Management**:
  - Generate a **random 256-bit encryption key** and a **128-bit IV (Initialization Vector)** per session.
  - Securely store the key and IV in memory only during execution.
  - Implement asymmetric encryption (RSA-2048 or RSA-4096) to encrypt AES keys before exfiltration.
- **File Modification**:
  - Append a **custom extension** (e.g., `.locked`, `.enc`) to encrypted files.
  - Maintain a **log of affected files** to facilitate recovery (if needed).
- **Target File Types**:
  - Documents: `.docx`, `.pdf`, `.xls`, `.ppt`
  - Images & Videos: `.jpg`, `.png`, `.mp4`, `.mov`
  - Databases: `.sql`, `.db`, `.mdb`
  - Development Files: `.cpp`, `.py`, `.java`, `.cs`
- **Exclusions**:
  - Avoid encrypting system-critical files (`.dll`, `.sys`, `winlogon.exe`) to prevent instability.
  - Detect virtual environments (e.g., VMware, VirtualBox) and avoid execution to bypass honeypots.

### **1.2. Persistence Mechanisms**
- **Windows Registry Modification**:
  - Add a **RUN key entry** (`HKCU\Software\Microsoft\Windows\CurrentVersion\Run`) to execute at startup.
- **Scheduled Task Creation**:
  - Configure **Windows Task Scheduler** to trigger execution at system reboot.
- **Process Injection**:
  - Inject into a legitimate Windows process (e.g., `explorer.exe`) to avoid detection.
- **Service Installation**:
  - Register a **Windows service** under a disguised name.

### **1.3. Propagation Methods**
- **Network-Based Propagation**:
  - Utilize **SMB Exploits** (e.g., EternalBlue - MS17-010) to laterally spread across networks.
  - Enumerate network shares and attempt unauthorized file modification.
- **USB Device Infection**:
  - Copy itself to **removable storage devices** with hidden attributes.
  - Create an **autorun.inf** file (if possible) to execute upon insertion.

### **1.4. Evasion Techniques**
- **Antivirus Evasion**:
  - Use **obfuscation** techniques (e.g., ConfuserEx, Themida).
  - Implement **string encryption** to hide malicious commands.
  - Employ **polymorphic code** to modify itself dynamically.
- **Fileless Execution**:
  - Load payload directly into **memory** using PowerShell reflection or shellcode injection.
  - Utilize **Windows LOLBins (Living-off-the-Land Binaries)** such as `mshta.exe`, `wmic.exe`, `rundll32.exe` for execution.
- **UAC Bypass**:
  - Exploit **AutoElevate=true** applications.
  - Utilize **ICMLuaUtil COM Interface** for silent privilege escalation.
- **Anti-Debugging & Anti-Sandbox**:
  - Check for debugging tools (`IsDebuggerPresent`, `NtQueryInformationProcess`).
  - Detect sandbox environments by analyzing:
    - RAM size (low memory machines are likely sandboxes).
    - CPU core count (1-2 cores indicate a virtual environment).
    - Running processes (`VBoxService.exe`, `vmtoolsd.exe`).
    - Execution delay (sandbox environments often have execution delays).

### **1.5. Shadow Copy and Recovery Prevention**
- **Delete Shadow Copies**:
  - Use `vssadmin delete shadows /all /quiet` to remove backup snapshots.
- **Disable Windows Recovery**:
  - Run `bcdedit /set {default} recoveryenabled No` to prevent system restoration.
- **Disable Windows Defender and Security Tools**:
  - Modify registry entries:
    ```
    reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
    ```
  - Use PowerShell:
    ```
    Set-MpPreference -DisableRealtimeMonitoring $true
    ```

### **1.6. Ransom Note & Communication**
- **Ransom Message Format**:
  - Create a ransom note in multiple formats (`README.txt`, `HOW_TO_DECRYPT.html`).
  - Provide **instructions** for payment in Bitcoin or Monero.
  - Display a unique **decryption ID** per victim.
- **Wallpaper Modification**:
  - Change the desktop wallpaper to the ransom message using:
    ```
    reg add "HKCU\Control Panel\Desktop" /v Wallpaper /t REG_SZ /d "C:\ransom.jpg" /f
    ```
- **Tor-Based Communication**:
  - Host **command-and-control (C2) servers** on `.onion` domains.
  - Use **encrypted channels (TLS, HTTPS, XOR encryption)** for communication.

---

## **2. Programming Language Considerations**
### **2.1. C/C++**
- **Pros**:
  - Direct Windows API access.
  - High execution speed.
  - Easy process injection.
- **Cons**:
  - Requires manual memory management.

### **2.2. Python**
- **Pros**:
  - Fast development.
  - Extensive cryptography libraries.
- **Cons**:
  - Requires obfuscation.
  - Needs external dependencies.

### **2.3. Go (Golang)**
- **Pros**:
  - Cross-platform compilation.
  - Efficient obfuscation.
- **Cons**:
  - Larger binary size.

---

## **4. Impact and Effectiveness**

### **4.1. Financial Impact**
- Demand payment in cryptocurrencies (e.g., Bitcoin, Monero) to make tracking difficult.

### **4.2. Operational Impact**
- Disrupt critical systems, such as hospitals, businesses, and governments.

### **4.3. Psychological Impact**
- Create urgency and fear in the user to force payment.

---

## **5. Containment and Prevention Measures**

### **5.1. Regular Backups**
- Maintain frequent offline backups to restore files without paying the ransom.

### **5.2. Security Updates**
- Keep systems and software updated to patch vulnerabilities exploited by ransomware.

### **5.3. User Education**
- Train users to avoid clicking on suspicious links or opening unknown email attachments.

### **5.4. Security Solutions**
- Use modern antivirus with behavioral detection.
- Implement firewalls and intrusion detection systems (IDS).

### **5.5. Network Segmentation**
- Isolate critical systems on separate networks to limit ransomware spread.

### **5.6. Execution Restrictions**
- Use Group Policy (GPO) to restrict execution of scripts and suspicious files.

### **5.7. Continuous Monitoring**
- Implement monitoring tools to detect suspicious activities in real-time.

---

## **6. Incident Response**

### **6.1. System Isolation**
- Disconnect infected systems from the network to prevent further spread.

### **6.2. Forensic Analysis**
- Collect evidence to understand the attack vector and ransomware behavior.

### **6.3. Data Recovery**
- Use backups to restore encrypted files.

### **6.4. Law Enforcement Notification**
- Report the incident to appropriate authorities for investigation.
