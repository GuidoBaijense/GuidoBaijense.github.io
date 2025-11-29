---
title: 'I tried building my own Windows 365 Link and it was a failure'
author: Guido
categories: [Microsoft, DIY]
tags: [Intune, Windows 365, Raspberry, diy]
image: ../assets/img/I-tried-building-my-own-Windows-365-Link-and-it-was-a-failure/image.png
published: false
---

You've probably heard of Windows 365 Link: A zero-client for Windows 365 that was announced during Microsoft at Ignite 2024. I've always seen the pro's of having a Cloud PC and zero or thin-clients have intrigued me as well. Because, why would you buy hardware to use a virtual PC while you can also just use the hardware you just bought as a fat client?

And then, Windows 365 Link was announced at Ignite. I must get my hands on one. But it's currently still only available in preview in a few countries that I don't live in. So, let's built one myself. Because, how difficult can it be?

## The hardware

I have a spare *Raspberry Pi 4b*. That should be sufficient, right? It's ARM based, has 2 GB of RAM, and according to the Windows on R-project, it should be able to boot Windows 11.

## Windows 365 Boot

If I want to mimic Windows 365 Link as far as possible, it should straight boot into Windows 365 sign in screen. That's where Windows 365 Boot comes in. This feature is available since Windows 11 22H2 (22621.3374) and presents the users a sign in screen for Windows 365 right after starting up their device.

Windows 365 Boot is available on Windows 11 Enterprise, Windows 11 Professional and Windows IoT Enterprise. Ah, IoT, that sounds promising.

## The software

I checked the system-requirements for Windows 11 IoT Enterprise and I concluded that it *might* work:

✅ Windows 11 IoT Enterprise LTSC

| </br>Component    | PREFERRED</br>Minimum&nbsp;Requirements   | OPTIONAL </br> Minimum&nbsp;Requirements  | My Raspberry Pi 4B specs |
| ---------------------- |:---------------------------------:|:---------------------------------:|:------------------:|
| Processor</sup>1<sup>             | 1&nbsp;GHz,&nbsp;2 Cores          | 1&nbsp;GHz,&nbsp;2&nbsp;Cores     | 1,8 GHz, 4 Cores |
| System&nbsp;Memory     |  4 GB                             |  2 GB                             | 2 GB |
| Storage&nbsp;Size      | 64 GB                             | 16 GB                             | 20 GB |
| Storage&nbsp;Type      | Solid&#x2011;State&nbsp;Drive&nbsp;(SSD) | Solid&#x2011;State&nbsp;Drive&nbsp;(SSD) </br> Hard&nbsp;Disk&nbsp;Drive&nbsp;(HDD)</br> Hybrid&nbsp;Hard&nbsp;Drive&nbsp;(SSHD) </br> Flash&nbsp;(eMMC,&nbsp;SD,&nbsp;USB)  | Solid State Drive (SSD) over USB 3.0.
| System&nbsp;Firmware   | UEFI                              | BIOS               | UEFI |
| TPM                    | TPM 2.0                           | Optional                          | What are you talking about? |
| Secure Boot            | Enabled                           | Optional                          | It might work |

## The process

I digged up my Raspberry from the basement and read through the Windows on R project's website. It's less promising as I thought as the latest supported build of Windows 11 is 22621. A bit to close for my comfort. But we can try.

I downloaded the Windows on R software and went on. We need an image-file and I thought I could get it from the Visual Studio-subscription page or what it's called. But that resulted in 22621.516 or something similar. Without the correct updates, so to say.