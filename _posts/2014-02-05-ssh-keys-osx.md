---
ID: 173
post_title: 'SSH keys &#038; OSX'
author: kral2
post_excerpt: ""
layout: post
permalink: http://bit.ly/1jdJlEu
published: true
post_date: 2014-02-05 02:43:11
---
Put your private & public key on folder ~/.ssh
All private keys must have 600 rights (-rw-------), or they will be ignored.

1. List your currently load keys:

		ssh-add -l

2. Load your keys on ssh-agent and store them on OSX Keychain for future use

		ssh-add -K /path/of/private/key

	Eventually type the SSH key's secret if prompted.

All your SSH keys should have been added to your "session" keychain for the application SSH.
From now on, when you need to connect with SSH, you can load all of your private keys at once with the following command

		ssh-add -A

 
## To go further

### Get speedy with a config file
You can setup a config file for your frequently accessed servers, and preconfigure all the connection details:

- user,
- ssh port,
- etc ...

### Reuse existing putty keys (*.ppk)

Make a good use of puttygen ... (To be described one day, maybe ...)

## References

I used the following informations found on the web and obviously the manual to get the whole thing working.    
Thanks you to each of the authors for their valuable informations.

[Adding a Private Key to Your Mac OSX Keychain](https://wiki.hpcc.msu.edu/display/hpccdocs/Adding+a+Private+Key+to+Your+Mac+OSX+Keychain)  
[Simplify Your Life With an SSH Config File](http://nerderati.com/2011/03/simplify-your-life-with-an-ssh-config-file/)    
[Using an SSH Config File](https://kb.mediatemple.net/questions/1625/Using+an+SSH+Config+File)