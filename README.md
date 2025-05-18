# 🧪 Windows Event Log Analysis Lab

## 📝 Introduction

This lab simulates a real-world brute-force attack scenario. The investigation began with a focus on Windows systems, but as the analysis progressed, attention shifted to a compromised Linux-based system (`linux-programatic-vr-ena`). By following this suspicious login story arc, I was able to demonstrate end-to-end investigative thinking and KQL-based detection capabilities across different operating systems.

---

## 🛠️ Tools I Used
- Microsoft Sentinel
- Azure Virtual Machines (Windows & Linux)
- KQL (Kusto Query Language)


---

## 🔍 Suspicious Login Story Arc: A Lab Walkthrough

### Step 1: 🚫 Brute-Force Login Attempts
> I started by identifying multiple failed login attempts from the same account within a short window.

- ✅ Query: [Brute Force Login Detection](queries/brute_force_login.kql)
- 🔍 Outcome: Over **100 failed login attempts** detected from account `root` across multiple **Linux-based systems**, and over **70 failed attempts** against the `administrator` account on several **Windows-based virtual machines (VMs)**.

- 🧠 Insight: This pattern suggests possible brute-force activity. These types of attacks are commonly used in initial access attempts. They highlight the importance of disabling unused accounts, enforcing strong passwords, and using account lockout policies.
🖼️ Screenshot of query and results:
![Brute Force Query Result](brute_force_query_result.png)

### Step 2: ✅ Successful Login After Failures

After detecting brute-force login attempts, I used this query to confirm whether any accounts eventually logged in successfully—a strong signal of potential compromise.

- ✅ Query: [Successful Login After Failures](queries/successful_login_after_failures.kql)
- 🔍 Outcome: The `root` account on device `linux-programatic-vr-ena` failed **100 times** and then successfully logged in at **3:55 PM**, just 4 minutes after the last failed attempt at **3:50 PM**.
- 🧠 Insight: This confirmed a likely brute-force compromise. In a SOC environment, this would trigger escalation to incident response for containment and further investigation.
🖼️ Screenshot of query and results:
![Successful Login After Failures](successful_login_after_failures.png)

### Step 3: 🧨 Suspicious Post-Login Command Execution
After confirming a successful login by the `root` account, I analyzed system activity to determine whether the account executed any suspicious commands. This step investigates whether the attacker took further action after gaining access.

- ✅ Query: [Suspicious Process Execution](queries/suspicious_process_execution.kql)
- 🔍 Outcome: The `root` account on device `linux-programatic-vr-ena` launched multiple instances of `bash` and `sh` within minutes of logging in successfully. Several of these processes were tied to the `waagent` directory, indicating a possible attempt to execute scripts or tamper with system services.
- 🧠 Insight: The repeated shell activity by `root` post-login suggests a high likelihood of attacker-driven command execution. The attacker may have been staging payloads or using the Azure guest agent path for post-exploitation tasks. In a real-world SOC environment, this pattern would justify immediate host isolation and forensic investigation.
🖼️ Screenshot of query and results:
![Suspicious Process Execution](suspicious_process_execution.png)


### Step 4: 🌐 External Network Communication

After observing post-login shell activity, I investigated whether the compromised `root` account attempted to communicate with external systems. This step helps identify signs of data exfiltration, malware downloads, or command-and-control behavior.

- ✅ Query: [External Network Communication](queries/external_network_communication.kql)
- 🔍 Outcome: The `root` account on device `linux-programatic-vr-ena` established outbound connections over port 443 and made HTTP-based requests to external IPs. These connections occurred shortly after shell activity, suggesting potential payload retrieval or communication with an attacker-controlled host.
- 🧠 Insight: Outbound connections to external IP addresses following a successful login and shell execution strongly indicate lateral movement or command-and-control activity. In a SOC setting, this would justify blocking the destination IPs, isolating the host, and capturing forensic data.

🖼️ Screenshot of query and results:  
![External Network Communication](external_network_communication.png)

---

## 🧠 Lessons Learned

This lab simulates a real-world brute-force attack followed by system compromise, post-login exploitation, and external communication. By following the suspicious login story arc, I was able to demonstrate end-to-end investigative thinking and KQL-based detection capabilities.

### Key Takeaways:
- 🔐 **Login Events Matter**: Unusual login patterns—especially high-volume failures followed by success—can be early signs of a brute-force attack.
- 💻 **Post-Login Activity Is Critical**: Even after a successful login, tracking command execution is vital to uncover attacker actions.
- 🌐 **External Communications Tell the Final Story**: Outbound connections by privileged users can reveal malware downloads, command-and-control channels, or data exfiltration.

### Analyst Mindset:
This exercise reinforced how a SOC analyst must pivot between data sources (LogonEvents → ProcessEvents → NetworkEvents) and correlate timelines to understand the full impact of an incident. It also showed the importance of documenting not just detections, but **the story behind them**.

---

✅ This lab reflects my ability to:
- Think like a threat hunter
- Use KQL in practical investigation scenarios
- Tell a clear and concise incident response story
- Use Microsoft Sentinel tools like an entry-level SOC analyst


