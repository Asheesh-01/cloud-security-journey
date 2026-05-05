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