## Topic 1 —
Recall questions:

What is the difference between a monolithic kernel and a microkernel? Which does Linux use and what is the security tradeoff?
What is Ring 0 and Ring 3? What hardware enforces this separation?
What is a system call? Give three examples of security-relevant system calls and what each does.
What is the difference between a hardware interrupt and a software interrupt?
What is the IDT and what attack targets it?
What is UEFI Secure Boot? What does it prevent and what does it not prevent?
What is the difference between MBR and GPT? Why is MBR a bootkit target and GPT is not?
What is a UEFI firmware rootkit? Why does it survive OS reinstallation?

Scenario questions:
9. SCENARIO: You find an unexpected entry in efibootmgr -v output pointing to an .efi file you do not recognize. Walk through your investigation.
10. SCENARIO: A server boots to a root shell instead of the normal login prompt. What does cat /proc/cmdline likely show and what does that tell you?



## Topic 2 — 
What is the MFT? What forensic information does it contain about deleted files?
What is $UsnJrnl? What does a ransomware attack look like in this journal?
What is an Alternate Data Stream? How do you detect one using the command line?
What is the difference between DACL and SACL?
Deny beats Allow in NTFS permission evaluation — explain why and give a real scenario where this matters.
What is a writable service binary path and why is it a privilege escalation risk?
SCENARIO: You run icacls C:\Program Files\VulnerableApp\ and see BUILTIN\Users:(OI)(CI)(W). What does this mean and what attack does it enable?

## Topic 3 — 
    1. What is the SAM database? Where is it stored and why cannot a normal user read it? 
    2. What is the difference between SYSTEM and Administrator? Which is more powerful and why? 
    3. Why is the built-in Administrator account dangerous even when renamed? 
    4. What is LAPS and what specific attack does it prevent? 
    5. What forensic information does a LNK file in the Recent folder contain? 
    6. Why is AppData a preferred malware installation location? 
    7. SCENARIO: You find an executable file in C:\Users\john\AppData\Local\Temp\ that was created 3 hours ago. John's account is a standard user. Walk through your investigation steps. 
    8. SCENARIO: reg query HKLM\...\ProfileList shows a SID with no corresponding folder in C:\Users. What does this indicate and what do you investigate next?

## Topic 4 

What is the difference between a process and a thread? What does each have independently?
What is PPID and why do unexpected parent-child relationships indicate malware?
What is process hollowing? How does it evade process-list-based detection?
What is an unquoted service path? Walk through the exploitation step by step.
What is the difference between HKLM Run and HKCU Run from a privilege and persistence perspective?
What does the Winlogon Userinit value normally contain? What does it mean if something extra is appended?
What is IFEO and how is it abused with accessibility tools to get a pre-authentication shell?
SCENARIO: You run Autoruns on a suspected machine and see an unsigned entry in the HKCU Run key pointing to C:\Users\john\AppData\Local\Temp\update.exe. Walk through your investigation.