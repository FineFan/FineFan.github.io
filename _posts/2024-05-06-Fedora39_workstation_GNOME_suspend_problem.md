---
layout: post
title: "Fedora 39 workstation suspend problem"
description: "Hit and fix the Fedora 39 workstation suspend problem."
date: 2024-05-06
feature_image: images/2024-05-06-Fedora39_workstation_GNOME_suspend_problem/fedora_39_suspend.png
tags: [FineMind]
---
When I update my laptop from Fedora 37 to Fedora 39, There comes the problem: No matter you disabled the "suspend" or not , it will always fall into suspend statue after about 15 minutes.   
当我把我的笔记本从Fedora 37 更新到  Fedora 39 之后，就遇到了一个问题，就是无论你在GNOME 的设置里面是否禁用了Suspend，它总会让GNOME 保持 15分钟后 休眠的 一个状态。  


<!--more-->


# Play with Fedora 39 . 与Fedora 39 一起玩耍。

When you install your laptop with Fedora 39 workstation , here comes to this problem . No matter the setting in GNOME Power setting , the laptop will always suspend itself after about 15 minutes.

当你的笔记本安装了Fedora 39以后，你会发现一个问题，无论你在GNOME 的电源设置里面如何设置，它总会在15分钟后自动休眠。  

![01](/images/2024-05-06-Fedora39_workstation_GNOME_suspend_problem/fedora39_suspend_gnome_setting.png "01")  

The problem is that when I leave my laptop power on in the office, it will be unable to connect with when I get home.  
这样就会给我带来一个问题，我将电脑开机放在办公室的桌面上，当我回到家的时候就发现机器连不上了。  

This one never happened when I using Fedora 37.  
这种情况在我使用Fedora 37的时候就没有发生过。  


#  Solution , 解决方案

After searching about 20 minutes in the morning after the holiday, I think I finally find the solution:  
在假期回来的早上花了20分钟，我想我终于找到了解决的方法:  

> https://discussion.fedoraproject.org/t/gnome-suspends-after-15-minutes-of-user-inactivity-even-on-ac-power/79801  

It is that: disable the suspend setting by using below command line:  
最后的方案其实就是，使用下面的命令行，将suspend 这个功能给禁用掉：  

To check  current setting:  
检查一下当前的系统设置：  

```shell
fine@thinkpadt480s ~ $ sudo -u gdm dbus-run-session gsettings list-recursively org.gnome.settings-daemon.plugins.power | grep sleep
org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 900
org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'suspend'
org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 900
org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type 'suspend'
fine@thinkpadt480s ~ $ 
```


To disable the suspend setting when AC power on:  
禁用通电情况下的休眠设置：
```shell
fine@thinkpadt480s ~ $ sudo -u gdm dbus-run-session gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 0
dbus-daemon[5855]: [session uid=42 pid=5855] Activating service name='ca.desrt.dconf' requested by ':1.0' (uid=42 pid=5856 comm="gsettings set org.gnome.settings-daemon.plugins.po" label="unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023")
dbus-daemon[5855]: [session uid=42 pid=5855] Successfully activated service 'ca.desrt.dconf'
fine@thinkpadt480s ~ $ echo $?
0
fine@thinkpadt480s ~ $ 

```

To disable the suspend setting when using battery:  
禁用电池状态下的休眠设置：

```shell
fine@thinkpadt480s ~ $ sudo -u gdm dbus-run-session gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 0
dbus-daemon[5899]: [session uid=42 pid=5899] Activating service name='ca.desrt.dconf' requested by ':1.0' (uid=42 pid=5900 comm="gsettings set org.gnome.settings-daemon.plugins.po" label="unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023")
dbus-daemon[5899]: [session uid=42 pid=5899] Successfully activated service 'ca.desrt.dconf'
fine@thinkpadt480s ~ $ echo $?
0
fine@thinkpadt480s ~ $
```

Check the setting again in the end:  
最后再检查一下系统设置  

```shell
fine@thinkpadt480s ~ $ sudo -u gdm dbus-run-session gsettings list-recursively org.gnome.settings-daemon.plugins.power | grep sleep
org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 0
org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'suspend'
org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 0
org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type 'suspend'
fine@thinkpadt480s ~ $ 
```

Then you good to go.  
到这里就解决这个问题了。  



