 (The ongoing process commenced on 20.1.2024, and was successfully completed on 23.1.2024)

./> First, try the following step before proceeding to the other steps below:
- This may resolve the issue immediately for you, or you may need to follow the entire guide to identify the actual problem if this step doesn't work.   
- In my case, the issue stemmed from either drivers or a corrupted Windows update. 

- I identified the issues — I discovered a corrupted Windows update hiding in the downloaded Windows update folder. I resolved it by deleting all the corrupted Windows update files, then proceeded to redownload the Windows update. After that, I successfully updated my laptop, and now everything is working perfectly!


0./> Navigate to "Troubleshoot" > "Advanced options" > "Startup Settings" > (Press F7) - 7) Disable driver signature enforcement.
- This allowed me to boot into Windows normally, enabling me to attempt manual fixes.

Note! - I attempted all the options provided below before disabling driver signature enforcement. If this step doesn't produce immediate results for you, please follow my guide and execute all the previous options before resorting to this one.

[When Windows boots up normally]

./> Navigate to "C:\Windows\SoftwareDistribution\Download", and delete all the files related to Windows Update within the "Download" folder.

./> Do not restart the computer; instead, use the shutdown option and then boot it back up.

./> Afterward, please wait to redownload Windows updates and proceed to update drivers, BIOS, etc.

NOTE! - My laptop is an Asus ROG Flow X13, so your situation may vary. Feel free to reach out if you're uncertain about what steps to take, whether you're dealing with similar or different issues.


![IMG_5258](https://github.com/iJCLEE/BSOD-Error-0xc000021a-fix-guide-/assets/61095429/15f86720-f3b5-40eb-b058-db12dd0a6d13)

My laptop was functioning well until Friday evening after I initiated a "Restart."
- Encountered a BSOD (Blue Screen of Death) and got stuck at 100% while collecting data.
(I attempted to reboot several times, hoping it would resolve the issue, but unfortunately, no luck.)

The error displayed was "0xc000021a," indicating that a critical boot file is corrupt.

[If you're facing the same issue, don't panic. Stay calm and follow my guide.]


NOTE! - My guide is not intended for disassembled laptops. If you have a built PC or a laptop where you've changed SSD, RAM, or any other hardware components, your issues might be different. Always check the error code and either search for the reason on Google or inspect the logs after using "Startup Repair."

---------------------------------------------------------------------------------------------------------------------------------------------------------

How did i manage to fix this?

1./> Initially, I tried accessing "Troubleshoot" > "Advanced Settings" > "Startup Settings" > 4) Safe Mode or 5) Safe Mode with Networking. [check photo]   
- However, each time I selected one of these options, it rebooted to the same Blue Screen of Death (BSOD). 

![BSOD1](https://github.com/iJCLEE/BSOD-Error-0xc000021a-fix-guide-/assets/61095429/b804def4-8fbc-45d7-930f-d4b8026e8683)

---------------------------------------------------------------------------------------------------------------------------------------------------------

2./> Following that, I initiated "Troubleshoot" > "Advanced Settings" > "Startup Repair."
- Despite its attempt to diagnose and repair, the issue persisted.
- I then captured a photo of the log file location: "C:\Windows\System32\Logfiles\Srt\SrtTrail.txt" to investigate the cause of the BSOD. [check photo]

![BSOD2](https://github.com/iJCLEE/BSOD-Error-0xc000021a-fix-guide-/assets/61095429/7c2fdceb-6c46-4e67-991a-75a6fb50dcfd)

---------------------------------------------------------------------------------------------------------------------------------------------------------

3./> Subsequently, I accessed "Troubleshoot" > "Advanced Options" > "Command Prompt."

Commands:   
$ C: 

- Navigating to the C: directory, which houses the system32 folder, I encountered Bitlocker blocking access. 
To proceed, it required either the "Windows password (not PIN)" or a "Recovery Key." 

NOTE! The Recovery Key can be obtained from the Microsoft website by logging in with your Windows-associated email account.)   
 
---------------------------------------------------------------------------------------------------------------------------------------------------------
 
4./> After successfully unlocking the C: directory, I disabled Bitlocker entirely to facilitate uninterrupted repair.

"Locate to system32 directory ( C: ), and type" > $ manage-bde -off

Commands:   
$ C:

$ manage-bde -off

or

$ manage-bde -off C:

- Bitlocker is a security feature designed to protect data in case of theft or loss. It can be re-enabled after resolving the boot issues.
 
---------------------------------------------------------------------------------------------------------------------------------------------------------
 
5./> With Bitlocker disabled, I opened the Command Prompt and used the following commands to open and examine the SrtTrail.txt file:

$ notepad 

(In Notepad, navigate to "File" > "Open..." > Locate "C:\Windows\System32\Logfiles\Srt\SrtTrail.txt" and open the file to check for the section indicating the cause.) [check photo]

![BSOD3](https://github.com/iJCLEE/BSOD-Error-0xc000021a-fix-guide-/assets/61095429/1f37b509-4b69-4958-9c7a-7f642c47e923)

---------------------------------------------------------------------------------------------------------------------------------------------------------

6./> I attempted the common file system fix command:

$ sfc /scannow 

Results: "Windows Resource Protection Could Not Perform the Requested Operation."
- The possible reason could be a corrupted file preventing Windows Resource Protection (WRP) from functioning.
 
---------------------------------------------------------------------------------------------------------------------------------------------------------
 
7./> Next, I ran the following command to check and fix errors on the disk:

$ chkdsk /f /r 

Results: Wait until the scan and fix are complete. Reboot and check if the issues are resolved. If not, continue reading. 
(This did not fixed for me yet.)

Commands info:   
? /f = fix (Fix errors)   
? /r = recover (Locates "bad" sectors on the disk, and "recovers readable information")
 
---------------------------------------------------------------------------------------------------------------------------------------------------------
 
8./> I attempted to revert pending actions and restore health using DISM:
Commands:

$ Dism.exe /image:C:\ /cleanup-image /revertpendingactions

Results: Wait for the "revertpendingactions" operation to complete. Then next commands:

$ Dism.exe /image:C:\ /cleanup-image /restorehealth

Results: Wait for the "restorehealth" operation to complete.
- Try rebooting the computer. If issues persist, continue reading.
(This did not fixed for me yet.)

Commands info:   
? Dism.exe = Deployment Image Service and Management (tool)   
? /image:(directory):\ = choose your system32 directory (mine is C:)   
? /cleanup-image = This command reduce the size of the WinSxS folder 
 
---------------------------------------------------------------------------------------------------------------------------------------------------------
 
9./> I ran a modified sfc command with specific offbootdir and offwindir parameters:

$ sfc /scannow /offbootdir=C:\ /offwindir=C:\Windows

Results: Allow the system to complete the scan operation. 
- Fortunately, this alternative sfc command worked for me, it says "Windows Resource Protection did not find any integrity violations". 
- This indicates that the system files are presently in a healthy and uncorrupted state. It is generally considered a positive outcome, as it suggests that the essential core files required for the Windows operating system remain intact and uncompromised.
-  However, it did not fixed BSOD in my case. [check photo]

![BSOD4](https://github.com/iJCLEE/BSOD-Error-0xc000021a-fix-guide-/assets/61095429/f55f6cfb-150d-4e4d-8751-a1ccfd118aba)

---------------------------------------------------------------------------------------------------------------------------------------------------------

10./> I haven't given up yet, and I'm going to run two additional commands:

$ bootrec /scanos

Results: Wait for the "scanos" operation to complete. Then next commands:

$ bootrec /rebuildbcd

Results: Wait for the "scanos" operation to complete. [check photo]

![IMG_5291](https://github.com/iJCLEE/BSOD-Error-0xc000021a-fix-guide-/assets/61095429/cf14a54f-256f-4ba0-ac72-c56fdc95ac64)

Commands info:   
? bootrec /scanos = scans all disks on the computer for Windows installations and displays any that are found.   
? bootrec /rebuildbcd = rebuilds the Boot Configuration Data (BCD) store. The BCD is a database that contains boot-related information, such as the installed operating systems and their boot parameters.
 
---------------------------------------------------------------------------------------------------------------------------------------------------------
 
11./> After executing all the commands mentioned above, I continued to encounter the Blue Screen of Death (BSOD) upon reboot. 
- As a final attempt, I opted for the last available option in "Startup Settings" - "7) Disable driver signature enforcement." This allowed my laptop to reboot normally, and I regained access to my laptop. Even after several subsequent reboots, my laptop continued to start up without issues.
- However, after leaving my laptop overnight and waking up in the morning, I faced the BSOD once again. 
- Fortunately, using "7) Disable driver signature enforcement" proved effective in resolving the issue temporarily for me. 

./> Unfortunately, despite the steps taken thus far, the file corruptions persist.

./> I identified the issues — I discovered a corrupted Windows update hiding in the downloaded Windows update folder. I resolved it by deleting all the corrupted Windows update files, then proceeded to redownload the Windows update. After that, I successfully updated my laptop, and now everything is working perfectly!

./> If you have any questions or need assistance understanding this guide, feel free to leave a comment or reach out to me directly for support.



