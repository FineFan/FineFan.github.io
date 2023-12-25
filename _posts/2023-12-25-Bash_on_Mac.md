---
layout: post
title: "Bash on macOS Sonoma 14.2.1"
description: "I just wanna use Bash on MacOS's terminal but there are problems"
date: 2023-12-25
feature_image: images/frog_bash_on_mac.jpg
tags: [tips, mac]
---

Here is the thing that the *zsh* comes with MacOS terminal by default, but I am the personal who is familiar with *Bash* when I learn Linux.So I just want to change the default SHELL of MacOS's terminal  from *zsh* to *bash*

<!--more-->

Once you setup your mac will, then you open your terimal , try :
> echo $SHELL  


You will get _zsh_ there, it means the default SHELL of the terminal is zsh.  
What I wanna to do is to change the default SHELL into _bash_ .
So I google it on the Internet it says:

> chsh -s /bin/bash 

will help. It works, Once I finished the command running , and then open a new tab on the terminal , it got _"/bin/bash"_ after _echo $SHELL_. But the prompt sucks!!!  

So I just put below codes into **_~/.bashrc_** file to make the default Bash Prompt looks like the same old.


```javascript
# To add git branch into the prompt
#
parse_git_branch() {
     git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}

if [ "$UID" -eq 0 ];then
    export PS1="\u@\h \[\033[32m\]\W\[\033[33m\]\$(parse_git_branch)\[\033[00m\] # " 
else
    export PS1="\u@\h \[\033[32m\]\W\[\033[33m\]\$(parse_git_branch)\[\033[00m\] $ " 
fi
```

As for the past, it should give your a pretty nice shell prompt looks like:

![Fine favorite shell prompt](/images/fine_favorite_bash_prompt.jpg "Fine favorite bash prompt")

But I figure out there are some weird things:  
1. When I open a new tab in terminal it doesn't read it.
2. After I open a new tab in the terminal , and then running **bash** to get a subshell it will get it.

After searching around for about days ,and discussed with **Jianhong Yin** I finally got the solution:  
It is to edit your **/etc/profile** on your mac

```javascript
# System-wide .profile for sh(1)

if [ -x /usr/libexec/path_helper ]; then
	eval `/usr/libexec/path_helper -s`
fi

#if [ "${BASH-no}" != "no" ]; then
#	[ -r /etc/bashrc ] && . /etc/bashrc
#fi

if [ "${BASH-no}" != "no" ]; then
	[ -r /etc/bashrc ] && . /etc/bashrc
	[ -r ~/.bashrc ] && . ~/.bashrc     ##### Here is the line I added.

fi
```


