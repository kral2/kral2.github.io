---
ID: 27
post_title: >
  Fusion and keyboard layouts for
  Windows/Linux/OSX guest VM
author: kral2
post_excerpt: ""
layout: post
permalink: >
  http://www.kral2.fr/fusion-and-keyboard-layouts/
published: true
post_date: 2013-10-24 23:25:57
---
<p style="text-align: justify;">Anyone using VMs hosted by a Mac have struggled one day or another with the keyboard layout of the guest VMs. Any special character you have to deal with (enter our beloved : '@', '#', '\', '|') could be the challenge of the day, specially with a non En-US keyboard. Then if you dare to get access to a remote system by iLO/iDRAC/IPMI/etc … well, you have to be Rain Man to type your password :D</p>

<p style="text-align: justify;">I don't know if it's a new feature of Fusion 6 (nor I remember having seen it before), or if I was blind for so long time, but today I have a really simple combination of settings which make the keyboard working like a charm, even with a nasty use case like this one : installing an ESXi through UCSM KVM via an RDP session from your windows VM (uh! inception-like), and trying to send F2, F12, or special characters for the root password ...</p>

<p style="text-align: justify;">On the virtual machine settings, under "keyboard and mouse", you just have to choose the profile "mac"… no tinkering needed with the keymap on the Windows Guest OS.</p>

<p style="text-align: justify;">If your guest OS is Mavericks, there is a little trick : be sure to select on the OS X settings the keyboard "PC" for  your language ...</p>

<p style="text-align: justify;">This works on Linux too (tested with a CentOS 6.4 only), except characters that are not directly accessible on a mac keyboard : like backslash and pipe. To access them on the Linux VM, simply the standard PC keyboard combination, with the right option key (alt) playing the role of Alt-GR. Yes on this last one, you still have to know your PC keyboard layout.</p>

<p style="text-align: justify;">Example : to type a pipe on your windows/linux guest OS, type "right alt" + "§" on your mac keyboard.</p>

<p style="text-align: justify;">As a reminder, backslash and pipe on the OS X host OS are accessible with the following combination : option + shift + (slash for backslash, and l for pipe).</p>