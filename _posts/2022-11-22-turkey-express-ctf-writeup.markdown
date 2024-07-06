---
layout: post
title: Turkey Express Mini CTF Write up 
description: I got an email from AntiSyphon that they were having a mini CTF. This post is how I got most of the flags.
date:   2022-11-22 15:00:00 -0600 
image:  '/images/2022/11/22/header.jpg'
tags:   [ctf, security]
---

I got an email from [AntiSyphon](https://www.antisyphontraining.com/) (after having attended some of their training classes) that they were having a [mini CTF](https://metactf.com/f09eb17ad05cd43a). I've written this after capturing almost all the flags, which I probably won't do next time as this is taking much longer as I'm running through all the steps again to make sure the write-up is accurate. For the time being, I'm using an alias that's not associated with my default username list so you won't know who I am on the [scoreboard](https://compete.metactf.com/146/scoreboard).

This is also now the first time I've noticed that Ghost doesn't allow autocorrect to work when writing posts. I may end up moving to another CMS unless Ghost 5.0 fixes this.

* * *

## "Welcome to the Obfuscation Games!" - 125 points

### Prompt

> During a recent incident response investigation, we came across this suspicious command executed by an attacker, and we'd like you to analyze it. Malware authors like to obfuscate their payloads to make it harder, but we're sure you're up to the task. See if you can figure out what's happening without even running it!

> $s=New-Object IO.MemoryStream(,[Convert]::FromBase64String("H4sIAAR+eWMA/xXJMQrCQBCF4as8BtucQKwEO7uAZRict2Yh7oTMNCLefd3q4+d/HDU5rR4JmVeibPpCDdSG/Cfb042GXT+bq8kZp4IL5M7U63z7GsdfBu/9YMRi3viTDosm3BlXAAAA"));IEX (New-Object IO.StreamReader(New-Object IO.Compression.GzipStream($s,[IO.Compression.CompressionMode]::Decompress))).ReadToEnd();

### Answer

Looking at the line of (Powershell) code we're presented with, the original block in quotes is Base64 encoded (as hinted by 'FromBase64String') so I copied the bit in the quotes and navigated to CTF fan-favorite [CyberChef](https://gchq.github.io/).

From there, I threw '**H4sIAAR+eWMA/xXJMQrCQBCF4as8BtucQKwEO7uAZRict2Yh7oTMNCLefd3q4+d/HDU5rR4JmVeibPpCDdSG/Cfb042GXT+bq8kZp4IL5M7U63z7GsdfBu/9YMRi3viTDosm3BlXAAAA**' into the input box, and added 'From Base64' from the menu in CyberChef over to start the decode process; however, that's not all!

But that's not all! There's also 'GzipStream' and 'Decompress' -- I'm familiar with gunzip from some previous Linux work and as luck would have it CyberChef also has that step! Add that to the formula and viola, Powershell code with the flag:

#### Flag

~~~
Write-host "The flag is in the encoded payload"; $f = "MetaCTF{decode_decompress_done}"
~~~

I suppose one could throw the original Powershell code into Powershell on their local box, but running encoded code (even though it's just from a CTF) is a stupid idea.

* * *

## Staging in 1...2...3 - 150 points

### Prompt

> The Incident Response (IR) team identified evidence that a Threat Actor accessed a system that contains sensitive company information. The Chief Information Security Officer (CISO) wants to know if any data was accessed or taken.

> There was a suspicious file created during the timeframe of Threat Actor activity: [C:\xyz.tmp](https://metaproblems.com/89f75cd8a7cc6b82879c0821e00179e1/xyz.tmp). Can you check it out?

### Answer

So with this one I wasn't quite sure where to start with a temp file so I downloaded the file into my Kali VM and ran '[file](https://www.man7.org/linux/man-pages/man1/file.1.html) xyz.tmp' and it returned 'xyz.tmp: 7-zip archive data, version 0.4'
Surprisingly I've not really used 7-zip on a Linux system before so after a quick search '7za' looked to be the command for 7zip I was able to extract everything with '7za e xyz.tmp'. After extracting all the files into a new directory, one of the subfolders that was extracted with the flag.

#### Flag

~~~
MetaCTF{you_might_consider_using_encryption}
~~~

* * * 

## The Impossible Survey - 175 points

### Prompt

> Your company just developed a new product, and before releasing it to the public, they decided to gauge the reactions from their employees [through a survey](https://forms.gle/EXbMd2jyVKYfE3Wv9). As an incentive, the participants get a gift card after completing it. A company-wide email was sent a while ago, yet no one has submitted the survey or claimed the gift card code. Can you be the first one?

### Answer

I'm annoyed with myself at the time I took on this one. With the prompt, it obviously sounds like there's an error somewhere in the form that prevents people from completing it successfully.

The first page says it needs 50 characters which doesn't appear to be accurate but after spamming the URL a few times I'm able to proceed forward. The next page went without issue and that would bring me to the final page.

After clicking in the first radio box, I got the following error, "This question requires one response per row" which makes sense. I click in the next radio button in the same column on the row below and then get "Please don't select more than one response per column". Hmm...

This led me to believe the logic was broken and it required different inputs that couldn't be shared in each column, usually for ranking different choices meaning that for it to accept the inputs there needed to be another column. It's been a while since I've written HTML/CSS but I know enough so I spent longer than I cared to (half-assed) trying to add another column with the randomly named div's via messing with the source/using the [dev tools](https://firefox-source-docs.mozilla.org/devtools-user/). After a bit, I gave up trying to make it work and would come back to it later.

The next time I worked on it, I went ahead and explored the rest of the code via the dev tools and wouldn't you know it there was a script block that I didn't explore previously. Expanding that out and taking a quick scan, there it was hidden in the remaining code to eventually appear. If you were super lazy like me, you can even do a search and find it easier (though in the end it was easier to find the block, copy that to VS Code and then search in there).

#### Flag
~~~
"Thank you for filling out the survey, we appreciate you feedback!\nHere is you gift card code: MetaCTF{so_whos_going_to_report_this_bug_to_google}"
~~~

* * *

## Diving Into the Announcement - 200 points

### Prompt

> Vulnerabilities are patched in software all the time, and for the most serious ones, researchers work to build proof-of-concept (POC) exploits for them. As defenders, we need to continuously monitor when new public exploits drop, figure out how they work, and ensure we're protected against them. At some point, Microsoft announced CVE-2020-1472. Your task is to locate a public exploit for it and identify the vulnerable function that the POCs call. The flag will be the function's name.

### Answer

This was one of the easier ones, just gotta' search for CVE-2020-1472 and do a search for which function is vulnerable. The first page I got was from [M$RC](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2020-1472) that didn't have the word 'function' in it so I skipped it as well as the next few pages as well as they only had links to CVSS pages and [what not](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1472). Below those I saw a [write up from CrowdStrike](https://www.crowdstrike.com/blog/cve-2020-1472-zerologon-security-advisory/) and knew that had to have a solid write up. I searched for 'function' and it mentioned the problematic function.

#### Flag
~~~
ComputeNetlogonCredential
~~~

* * *

## A Confident Hash - 250 points

### Prompt
> Check out this hash. Can you crack it? Here's a wordlist you can use. You only have 5 attempts to get the flag right. **$2a$04$KMCaaiytS5OIsg2UZtthzugkZUPDqQ/Zyoys8XAY6AJVgirU/MWOS**

### Answer

This was the probably the second easiest flag to get, crack a hash. Just for grins, I tried [crackstation](https://crackstation.net/) but that hash type doesn't work there so I threw it in a file on my Kali VM and also created a wordlist from the supplied list. From there I cracked (ehhhh?) open a useful [cheat sheet](https://www.blackhillsinfosec.com/wp-content/uploads/2020/09/HashcatCheatSheet.v2018.1b.pdf) I've used in the past from BlackHills. That actually listed what some hashes start with and that led me to use bcrypt. Hash-Identifier didn't catch it either so I suppose it's something I'll recognize with future experience.

So with the known hash type (3200), hash and wordlist, off we go. If you're not sure of the proper hashcat structure, one could either 'man' it, --help it or check the cheat sheet. I ran 'hashcat -m 3200 hash.txt confident_dict.txt' (looking back I see I left out the attack mode but c'est la vie) and quickly cracked the hash.

#### Flag
~~~
$2a$04$KMCaaiytS5OIsg2UZtthzugkZUPDqQ/Zyoys8XAY6AJVgirU/MWOS:galactica_hash
~~~

* * * 

## King to Queen 3 - 300 points

### Prompt

> An employee on the network has been emailing these chess puzzles everyday to someone outside of the domain, and nobody really thought it was suspicious until they saw this weird string sent to that same person on the following day: 
> ~~~ 
> F^mY;L?t24Zk.m^-hnWl,[l)[ku 
> ~~~

> The SOC team has provided an [archive](https://metaproblems.com/4aab445f1b4caf923c6e2493811e7b3f/king_to_queen_3.zip) of the email attachments, and has tasked you to investigate the actual contents of the ciphertext. Can you figure out what they've been saying?

> Hint: The flag is wrapped in MetaCTF{}. This should be enough to help you figure out the encoding.

### What I Tried

I didn't get this one; I tried to find some help on this one but only ran into the answer and it's not one I would have figured out on my own since it's very CTFy (IMO).

I had two thoughts on this one – the weird string may have been encoded somehow, be it ROT or a weird hash OR with the pngs provided, they may be encoded via some steganography.

~~~
F^mY;L?t24Zk.m^-hnWl,[l)[ku
~~~


I threw this in Cyberchef and tried a variety of combinations to no avail. I spent a good amount of time trying to decipher this one without any luck. I also thought it may have potentially been a passcode used for the provided files.

> The SOC team has provided an [archive](https://metaproblems.com/4aab445f1b4caf923c6e2493811e7b3f/king_to_queen_3.zip)

So I thought something may have been encoded in the png's within the zip. I [binwalk](https://www.kali.org/tools/binwalk/)'d them all, repeatedly without any use. I also tried to check to check the [file](https://www.man7.org/linux/man-pages/man1/file.1.html) type if there was anything else as well as [hexdumping](https://www.systutorials.com/docs/linux/man/1-xxd/) them without any luck as well. Some other bits I tried when I was really grasping at straws was to decode the png's to Base64 and try some wizardry with those as well as importing the png's into Wireshark. 

Yeah, I didn't know that would work either until I was doing some research, but it was a worth a shot though it didn't help any. the dimensions weren't consistent on all the images as well so I thought may have been a lead but alas, nothing.

### Answer
Coming soon, *probably...*