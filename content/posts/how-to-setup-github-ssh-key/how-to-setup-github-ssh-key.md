+++
date = '2025-05-15T12:27:47+02:00'
draft = false
title = 'How to Setup Github Ssh Key'
+++
![Banner](/images/blog/how-to-setup-github-ssh-key/banner.png)

**Github SSH Key set up**

This tutorial will help you to be able to set up SSH key connection to your github account on a **Linux** machine. You can also continue to read this tutorial if you want to set up **_Deploy Keys_**.

For now let's just assume that **you've successfully set up SSH key**, after that you need to make sure that you don't access your repo with HTTPS, but with ssh, so if you previously cloned your repo wirh https set it to ssh as below:

`git remote set-url origin git@github.com:yourUsername/nameOfRepo.git`

Now let's come back to SSH generation part...

Navigate to ~/.ssh directory

Execute:

`ssh-keygen -t rsa -b 4096 -C "yourGithubEmail"`

If you press enter:

By default id\_rsa named public and private keys will be generated.

You can also specify it

Then will be prompted to enter passphrase

You can again press enter and skip it (you won't have passphrase in this case), or you can set up passphrase by specifying

Now keys should be generated.

(execute ls to see them)

*   Copy content of public key (generated file with .pub extension)
*   Go to ssh key section of github in the settings
*   Click on new ssh key
*   Give a title
*   In the publick key box, paste what you've copied.
*   Save it.

Now come back to terminal, in the .ssh directory, create config file

You can create by either:

`touch config`

Or

`vi config`

Paste this into config file

```sh
Host github.com
User git
Port 22
Hostname github.com
IdentityFile ~/.ssh/nameOfYourPrivateKey
TCPKeepAlive yes
IdentiesOnly yes # if it throws error about this line delete or comment out
```  

_Here make sure that you changed IdentityFile's path with the path that points to your own private key_

**Save the file and exit**

Execute following commands:

```sh
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/nameOfYourPrivateKey
```

You should be good to go now :)

Test your ssh connection by:

`ssh -T github.com`

It should say you have successfully set up SSH key authentication if you have done so :)

Thanks for reading!