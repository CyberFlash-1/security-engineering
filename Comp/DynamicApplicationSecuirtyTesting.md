# Applied Live Lab: Dynamic Application Security Testing (DAST)

## Scenario
As a SecurityX cybersecurity professional at Structureality Inc., you are tasked with identifying weaknesses and vulnerabilities across the organization’s environment, including its software development lifecycle. Dynamic Application Security Testing (DAST) involves not only validating that files function correctly but also examining them for malicious activity. Malware can be introduced even in internally developed code through reused software components, third-party libraries, or online repositories.


Dynamic Application Security Testing (DAST) includes analyzing files to confirm that they function as expected, but also to evaluate them for malicious elements. Malware might be present, even in internally developed code, through the reuse of existing software products, software libraries, or online repositories.



  ## 1. Evaluating Malware Analysis Tools <br>
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/0e425091-329d-41ee-a9d3-67ce60407e89" /><br>

   **Joe Sandbox Cloud is an online version of the Joe Sandbox malware analysis tool that runs in the cloud. Users can upload files and URLs to be analyzed in a secure, cloud-based environment without requiring any local infrastructure. Joe Sandbox Cloud offers various analysis options such as static, dynamic, and hybrid analysis, which helps users to better understand the behavior of malware and develop effective countermeasures.**

   <img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/3dd0ded7-a42d-49cd-9604-1633c83d681d" /><br>

 **Example:** Analyzing the file `Zeus.x86`

- **Detection:** Classifies the threat level (e.g., malicious, suspicious, or clean) and identifies the specific malware (e.g., Mirai).  
- **Info:** Reports tactics observed during execution, such as generating network traffic or modifying Windows Registry entries.  
- **Class:** Categorizes general behavior, such as Trojan-like or ransomware-like activity.

**Linux Analysis Report:** The `Zeus.x86` report provides detailed insight into the malware’s operations in a Linux environment, including network interactions, system modifications, and other malicious behaviors. <br>


  <img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/85c8254f-adc9-4160-97b3-f3b0dba0d588" />

## 2. Performing Malware Analysis

To perform malware analysis using Joe Sandbox Cloud:

1. Upload the target file or URL to Joe Sandbox Cloud.
2. Choose the type of analysis:
   - **Static Analysis:** Examine code and file structure without execution.
   - **Dynamic Analysis:** Execute the file in a sandbox to observe runtime behavior.
   - **Hybrid Analysis:** Combine static and dynamic approaches for a comprehensive view.
3. Review the analysis report for:
   - Threat classification and specific malware identification.
   - Observed tactics, such as system modifications or network activity.
   - Behavior class, e.g., Trojan-like or ransomware-like activity.
4. Document findings and recommended mitigations for integration into the security posture.
