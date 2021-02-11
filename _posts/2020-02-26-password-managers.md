---
layout: post
title: "Password managers"
categories: Password managers
author: "Michael Moese"
meta: "password-manager"
---

This post was originally written for the WordPress blog I ran in addition, but
I want to get rid of this. This is an updated English version of the original
post there.

It's such a thing with password managers. On the one hand, you want to use
them for storing individual passwords for each service. On the other hand,
though, you have to entrust all your data to some service. Over the past time,
I tried a lot of those, and I want to share my experience, concerns and
opinion on the tools and services I used.
Using generated passwords and not reusing them is one thing I really think
improves the overall security of all your accounts. You really want to use
this. And here are a few options for you.

## LastPass
LastPass [1] was the first service I tried, and I even re-visited the service
when writing the original post. Initially, I was using the premium version,
but after the trial, I continued the free version. It was actually working
quite well, including the checks for leaked passwords, across many devices.
IPhone and IPad were no surprise, like Windows - but even the browser
integration on Linux worked reasonably well. 
The password audits for weak and reused passwords came in handy, and the
service helps you change passwords quickly. In general, the company seems to
be quite responsive when they had security issues in the past, but this is of
course no audit of LastPass.

But now, let's go for the downside, and what is blocking me from using
LastPass as my personal driver. You cannot add an arbitrary number of URLs for
a password entry, to use it like on different services on the intranet. You can
only define equivalent domains, but those must not include subdomains. This is
a serious limitation for me, and maybe for you.

## 1Password
1Password is another commercial provider, but without a free model. You get a
trial period after which you have to buy a subscription. I was using this
service for a while after ditching LastPass, because the integration on my iOS
devices is rather nice, they also offer proper browser integration across all
platforms. They even provide a command line interface, but this was rather
complicated to use. Actually it's not a bad service, with good features, but I
found it was too expensive. 

## Bitwarden
On my journey, I encountered Bitwarden [3]. This software is open source, and
you can either get an account hosted by them, or you can host it yourself. In
both cases, you can get a paid option for extra features like sharing of
passwords etc. I would have been willing to spend that money to support open
source, especially as I can host my own server. From the feature side, this is
almost en par with LastPass and 1Password, with the big plus of hosting this
on your own infrastructure - which for me is something I want have. 

Update: 
I discovered a second implementation of the server component, which uses way
less ressources and seems to be quite usable [8].

## Pass
Pass [4] is completely different. Every password is just a plain text file with
meta-information like username, URL, etc. and your password in it. Each of
these files is encrypted with gpg [5], a technology widely used for securing
communication via e-mail (and a lot more, of course). At least for me, using
this was quite easy, as I am already using gpg a lot. 
But that's just half the thing - you want that store synchronized across your
devices. Pass is using git [6] to synchronize your passwords. In my case, with
a self-hosted service using Gitea [7] and a private repository.
Initial setup is a bit more challenging, you need to create keys for gpg and
for ssh, distribute them, etc. But then, this is working like a charm. On
Linux and iOS I never had any problems. The only problematic platform for me
was Windows, but this is only used for occasional gaming, anyway, so I don't
care too much.

## Conclusion
For me, there are a few considerations. The most important when it comes to IT
security is, in my opinion, trust. And I'm not sure how much you should trust
companies with your passwords. Current developments in Germany depict a
scenario where those providers could be forced to give your passwords to law
enforcement in plain text. I promise, when they need to have plain text
passwords stored, these will also leak to criminals.
For me, personally, a self-hosted open source solution is the only way to go
with this. For me, as Linux power user, Pass is the perfect solution.
If you are interested in a proper introduction/how to setup all this, send a
mail blog at frankenmichl dot de.
In case you are interested: I started a small project to convert my
1Password data into pass, you can find it [on my git
server](https://git.omglinux.de/michael/1pw2pass)

Even though I would still consider Pass the perfect solution, I'm using
Bitwarden in the meantime. It has proven itself to be rock solid and reliable,
it is easy to use on other operating systems (like Windows) and - that's the
main reason: my wife is finally using it now, too. I don't dare trying to
teach her to use Git..


|---|-------------------------------------------|
|[1]|https://lastpass.com/                      |
|[2]|https://1password.com/                     |
|[3]|https://bitwarden.com/                     |
|[4]|https://www.passwordstore.org/             |
|[5]|https://gnupg.org/                         |
|[6]|https://git-scm.com/                       |
|[7]|https://gitea.io/                          |
|[8]|https://github.com/dani-garcia/bitwarden_rs|
