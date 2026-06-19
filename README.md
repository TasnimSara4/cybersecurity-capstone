```markdown
# [Identifying The Border]
### Cybersecurity Capstone Technical Documentation


* **Student Name:** Fahmida Islam and Tasnim Sara
* **Date:** 06/02/26
* **Course/Period:** [Cybersecurity Period 4]
* **Selected Pathway:** [Option 1: Expansion Pack]
* **Project Demo Link:** [https://www.youtube.com/watch?v=rOJjpiQpGLA]

---


## 1. Executive Summary & Objective
*This project builds on the SYN Flood and ARP Spoofing labs by examining how much malicious traffic a network can handle before normal communication is affected. The goal is to identify the point at which network performance begins to degrade and services become unreliable.*




* **Project Purpose:** [The purpose of this project is to measure the impact of SYN Flood and ARP Spoofing attacks on a network and evaluate at which point the network becomes unstable.]
* **Cybersecurity Relevance:** [Denial-of-service attacks can disrupt networks and make services unavailable to users. Understanding their impact and testing defenses helps cybersecurity professionals improve network resilience and reduce the risk of service outages.]


---


## 2. Prerequisites & System Requirements
*Instructions:* 
 -To understand how SYN Flood attacks affect network performance
 -To observe how ARP Spoofing changes or redirects network traffic
 -To compare normal network behavior with attack conditions
 -To measure changes in speed, delay, and connection stability
 -To use Wireshark and other tools to detect and analyze attacks*


* **Hardware Requirements:** [Minimum 8GB RAM, 20GB free disk space, and sufficient CPU resources to run three virtual machines simultaneously.]
* **Operating System(s):** [Host operating system capable of running virtualization software (Windows, macOS, or Linux). Kali Linux VM (Attacker), Kali Linux VM (Target), and Ubuntu Linux VM (Packet Capturing and Monitoring)]
* **Software/Tools Needed:**
  * [Wireshark (network traffic analysis)]
  * [hping3 (SYN Flood testing)]
  * [arpspoof (ARP Spoofing testing)]
  * [Terminal/Command Prompt]
  * [ping]
  * [arp]
  * [netstat]
  * [Virtual network connecting all three virtual machines]
* **Required Baseline Knowledge:** [All virtual machines must be connected to the same virtual network. Each machine must have a valid IP address. Connectivity between machines should be verified using the ping command before testing begins.Wireshark should be installed and configured to capture traffic on the target systems.]


---


## 3. Environment Setup & Architecture
*Instructions:
Kali Linux (attacker and target systems)
 Ubuntu Linux (monitoring system)
 Wireshark (packet analysis)
 hping3 (SYN Flood simulation)
 arpspoof (ARP Spoofing simulation)
 ping (connectivity testing)
 curl (HTTP response testing)
 s / netstat (connection monitoring)*


* **Network/Lab Layout:** [ Student 1 Kali Linux VM (Attacker) and Student 2 Kali Linux VM (Target) both on the same isolated virtual network. A third monitoring VM, Ubuntu Linux, will be used by Student 2 to capture traffic or verify service availability.]
* **Initial Configurations:** [ 
  * Set all VMs to the same host-only or internal network.
  * Assign static IP addresses so the attacker and target stay predictable.
  * Confirm connectivity using `ping` from attacker to target before starting tests.
  * Start Wireshark on the target or monitor VM to capture traffic during each test. ]


---


## 4. Step-by-Step Implementation Guide


### Phase 1: [Environment Setup]


#### Step 1.1: [Start Virtual Machines]
* **Action:** [Start Kali Linux VM's on both students' devices. Student 2 must start Ubuntu Linux VM as well.]
* **Command/Code:** [Not applicable.]
* **Expected Output:** [All virtual machines boot successfully to the desktop.]
* **Plain-English Explanation:** [This step starts the virtual machines so the network test can begin.]


#### Step 1.2: [Identify IP Addresses]
* **Action:** [Check the IP address of each virtual machine using command-line tools.]
* **Command/Code:**
  ```bash
  ip a
  ```
* **Expected Output:** [Each virtual machine displays an IPv4 address (for example: 10.15.112.184 on Kali, and 10.15.90.248 on Ubuntu), which identifies it on the network.]
* **Plain-English Explanation:** [This step identifies the network addresses of each virtual machine so they can communicate with each other during testing.]


### Phase 2: [Baseline Measurement]


#### Step 2.1: [Verify Network Connectivity]
* **Action:** [Confirm the attacker can reach the target and the target is responsive.]
* **Command/Code:**
  ```bash
  ping <S2 IP Address> -c 5
  ```
* **Expected Output:** [Five successful replies with round-trip times.]
* **Plain-English Explanation:** [We test basic network connectivity first so any later failures are caused by the attack, not misconfiguration.]


#### Step 2.2: [Start a Baseline Service]
* **Action:** [Student 2 will run a simple service as the target to measure availability.]
* **Command/Code:**
  ```bash
  sudo python3 -m http.server 8080
  ```
* **Expected Output:** [`Serving HTTP on 0.0.0.0 port 8080 ...`]
* **Plain-English Explanation:** [A basic web service gives us a consistent target to check during the attack.]


#### Step 2.3: [Start Wireshark Monitoring (Student 2)]
* **Action:** [Open Wireshark on the Ubuntu monitoring VM through a Terminal and begin capturing packets before any attacks are launched. Once Wireshark opens, press the blue fin symbol in the upper left corner of the Wireshark tab.]
* **Command/Code:**
  ```bash
  sudo wireshark
  ```
* **Expected Output:** [Wireshark opens and begins to display live network traffic.]
* **Plain-English Explanation:** [Capturing traffic before the attack creates a baseline for comparison. During later phases, Wireshark will be used to observe TCP SYN packets, ARP packets, packet rates, and abnormal traffic patterns caused by the attacks.]


### Phase 3: [Determine the SYN Flood Failure Threshold]


#### Step 3.1: [Launch a Low-Intensity SYN Flood]
* **Action:** [Begin with a low-rate SYN flood against the target web service. Also, to get out of hping3 (applies to all steps), Hold Ctrl and press C ( Ctrl + C )]
* **Command/Code:**
  ```bash
  sudo hping3 -S -p 8080 -i u100000 <S2 IP Address>
  ```
* **Expected Output:** [Continuous packet transmission from hping3. Should see flags=SA, where SA = SYN-ACK]
* **Plain-English Explanation:** [This test introduces a small amount of attack traffic while still allowing the service to stay mostly operational. It serves as the first data point for measuring network resilience.]
* **Data to Observe:** [Packet loss percentage, web service availability, and the number of SYN packets observed in Wireshark]


#### Step 3.2: [Verify Web Service Availability]
* **Action:** [Student 2 must confirm whether the Python web server can be reached after launching the attacks on a new tab in the Ubuntu Terminal. Important: Perform this step again after each intensity SYN flood.]
* **Command/Code:**
   ```bash
   curl http://<S2 IP Address>:8080
   ```
* **Expected Output:** ['<!DOCTYPE HTML> ...']
* **Plain-English Explanation:** [This step confirms that the web server is functioning normally before any attack traffic is introduced. Creating a working baseline allows for later performance issues to be attributed to the attack rather than a configuration problem.]


#### Step 3.3: [Launch a Medium-Intensity SYN Flood]
* **Action:** [Increase the attack rate and observe changes in performance.]
* **Command/Code:**
  ```bash
  sudo hping3 -S -p 8080 -i u10000 <S2 IP Address>
  ```
* **Expected Output:** [Faster SYN packet generation.]
* **Plain-English Explanation:** [This step increases network load and helps identify early signs of service degradation.]
* **Data to Observe:** [Packet loss percentage, web service availability, and the number of SYN packets observed in Wireshark]


#### Step 3.4: [Launch a High-Intensity SYN Flood]
* **Action:** [Increase the SYN packet rate further.]
* **Command/Code:**
  ```bash
  sudo hping3 -S -p 8080 -i u1000 <S2 IP Address>
  ```
* **Expected Output:** [Rapid SYN packet generation.]
* **Plain-English Explanation:** [This step is designed to push the target closer to failure and identify when performance becomes noticeably degraded. Performance degradation may become a bit noticeable when running curl.]
* **Data to Observe:** [Packet loss percentage, web service availability, and the number of SYN packets observed in Wireshark]


#### Step 3.5: [Launch Flood]
* **Action:** [Generate SYN packets at the maximum possible rate.]
* **Command/Code:**
  ```bash
  sudo hping3 --flood -S -p 8080 <S2 IP Address>
  ```
* **Expected Output:** ['hping in flood mode, no replies will be shown']
* **Plain-English Explanation:** [This test represents the maximum attack intensity and is used to determine the point where normal communication becomes unavailable.]
* **Data to Observe:** [Packet loss percentage, web service availability, and the number of SYN packets observed in Wireshark]


#### Step 3.6: [Analyze SYN Flood Results]
* **Action:** [Compare the results from all four attack levels.]
* **Expected Output:** [Able to identify the attack intensity at which the target system first experiences significant performance degradation, such as increased latency, packet loss, service delays, or a large increase in SYN_RECV connections.]
* **Plain-English Explanation:** [By comparing the effects of each attack level, we can identify the point where the network begins to struggle under the SYN Flood attack. This threshold represents the maximum amount of malicious traffic the target can handle before performance starts to degrade.]


### Phase 4: [ARP Spoofing Analysis]


#### Step 4.1: [Capture Baseline ARP Traffic (Student 2)]
* **Action:** [Start Wireshark on the Ubuntu monitoring VM and observe normal ARP traffic before the attack begins.]
* **Command/Code:**
  ```bash
  sudo wireshark
  ```
* **Expected Output:** [Wireshark displays normal ARP requests and replies between devices on the network.]
* **Plain-English Explanation:** [This establishes a baseline so that changes caused by ARP spoofing can be identified later.]


#### Step 4.2: [Analyze Initial ARP Table (Student 2)]
* **Action:** [Inspect the ARP table on the Ubuntu monitoring VM before the ARP spoofing attack begins.]
* **Command/Code:**
   ```bash
   arp -n
   ```
* **Expected Output:** [The ARP table displays the IP addresses and corresponding MAC addresses currently known to the Ubuntu monitoring VM.]
* **Plain-English Explanation:** [This step records normal ARP table entries before any malicious activity occurs. Comparing these entries to later results helps determine whether the attack altered the device to MAC address mappings.]


#### Step 4.3: [Launch ARP Spoofing Attack (Student 1)]
* **Action:** [Student 1 sends forged ARP replies to redirect traffic.]
* **Command/Code:**
  ```bash
  sudo arpspoof -i eth0 -t <S2 IP Address> <Ubuntu IP Address>
  ```
* **Expected Output:** [The terminal continuously displays ARP reply messages being sent to the target.]
* **Plain-English Explanation:** [ARP spoofing works by sending forged ARP replies onto the network. These replies attempt to convince other devices to associate a legitimate IP address with the attacker's MAC address. If successful, network traffic may be redirected or disrupted.]


#### Step 4.4: [Verify ARP Table Changes (Student 2)]
* **Action:** [Inspect the ARP table on the target system before and during the attack.]
* **Command/Code:**
  ```bash
  arp -n
  ```
* **Expected Output:** [The ARP table may display modified MAC address mappings compared to the baseline recorded before the attack.]
* **Plain-English Explanation:** [ARP spoofing attempts to alter how devices associate IP addresses with MAC addresses. Comparing the ARP table before and during the attack helps determine whether the spoofing attempt affected network communications. In some environments, the ARP table may remain the same although forged ARP packets are being transmitted.]


#### Step 4.5: [Analyze ARP Traffic in Wireshark (Student 2)]
* **Action:** [Observe ARP packets captured on the Ubuntu monitoring VM and compare them to the baseline traffic captured earlier.]
* **Command/Code:**
  ```bash
  Wireshark Display Filter: arp
  ```
* **Expected Output:** [Wireshark displays ARP requests and ARP replies associated with the spoofing activity. Additional ARP traffic may be observed compared to the baseline capture.]
* **Plain-English Explanation:** [Packet captures provide visual evidence of ARP spoofing activity. By comparing traffic before and during the attack, students can observe how forged ARP messages affect network communications and identify signs of abnormal behavior.]


#### Step 4.6: [Identify the Borderline Point]
* **Action:** [Compare the observations made from the SYN Flood and the ARP Spoofing phases.]
* **Expected Output:** [Should be able to identify the point where the network performance becomes noticeably degraded, such as abnormal ARP activity or communication disruptions.]
* **Plain-English Explanation:** [This step summarizes the findings from the experiment and identifies the threshold where normal network behavior begins to break down under malicious traffic. This threshold represents the "border" referred to in the project title.]


---


## 5. Verification: How to Test Your Setup
*Instructions: How does the reader know they successfully completed your lab? Provide a quick test or checklist to verify success.*


* **Success Criteria 1:** [Student 1 can successfully ping Student 2's Kali VM before any attacks are launched.]
* **Success Criteria 2:** [Student 2's web server is accessible before testing.
* **Command/Code:**
  ```bash
  curl http://<S2 IP Address>:8080
  ```
  The command should return the default Python HTTP server page.]
* **Success Criteria 3:** [Wireshark on the Ubuntu monitoring VM captures normal network traffic before the attack and displays TCP SYN packets during the SYN Flood attack.]
* **Success Criteria 4:** [During medium, high, or flood-mode testing, Student 2 observes increased latency, packet loss, or a growing number of `SYN_RECV` connections using:
  ```bash
  netstat -atn -c
  ```
  ]


---


## 6. Troubleshooting & Common Pitfalls
*Instructno do ions: Think back to the hurdles you faced while building this. List at least 2 or 3 common errors a user might encounter and exactly how to fix them.*


* **Issue 1:** ['hping3' reports:
  ```
  hping in flood mode, no replies will be shown
  ```]
  * **Fix:** [This is normal behavior. In flood mode, hping3 sends packets as fast as possible and does not display responses. Use Wireshark, ping, or netstat on the target system to observe the effects of the attack.]
* **Issue 2:** [ARP Spoofing commands do not produce the expected ARP table changes.]
  * **Fix:** [Confirm that the correct IP addresses and interface name are being used. Use:
      ```bash
      ip a
      ```
      to identify the active interface and:
      ```bash
      arp -n
      ```
      to inspect the ARP table before and after the attack.]


---


## 7. Conclusion & Security Takeaways
*Instructions: Wrap up your documentation by summarizing what was achieved and discussing the defensive or offensive takeaways.*


* **What was learned:** [This project examined the effects of SYN Flood and ARP Spoofing attacks on a small virtual network. By slowly increasing the intensity of SYN Flood attacks, we were able to observe how network performance changed as malicious traffic increased. Packet captures collected through Wireshark showed a significant rise in TCP SYN traffic as attack intensity increased, while service availability tests using a Python web server helped identify when normal communication began to degrade. During the ARP Spoofing phase, forged ARP reply packets were observed and ARP tables were analyzed to demonstrate how attackers can attempt to manipulate network communications through false address mappings. While developing this project, we noticed that in some environments, the mappings remained the same while in other trials, the mappings changed. Although the ARP table did not always show different mappings, we noticed that forged ARP packets were consistently shown in Wireshark, indicating the presence of spoofing activity on the network. Ultimately, our results demonstrated that networks have a limit to the amount of malicious traffic they can handle, which in our testing occurred at a high intensity SYN flood (u1000), before performance begins to suffer. From comparing different intensities in attacks, we were able to identify the border between normal operation and noticeable service degradation.]
* **Defensive Mindset:** [Administrators can defend against SYN Flood and ARP Spoofing attacks by monitoring network traffic, detecting unusual activity, and using security tools that block malicious packets. Understanding how these attacks work helps organizations identify threats earlier and reduce the risk of service disruptions.]


---


## 8. References & Citations
*Instructions: List any text resources, documentation pages, GitHub repos, or Google Classroom modules you utilized to research or build this project.*


1.  [Denial of Service Lab Series] - [Lab 8.2.1 and Lab 8.1.2]
2.  [Kali Linux Tools - hping3] - [https://www.kali.org/tools/hping3/]
3.  [Linux Config] - [https://linuxconfig.org/how-to-check-my-local-and-external-ip-address-on-kali-linux]
