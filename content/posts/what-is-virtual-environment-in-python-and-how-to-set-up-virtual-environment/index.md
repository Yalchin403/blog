+++
date = '2025-08-22T22:44:47+02:00'
draft = false
title = 'What Is Virtual Environment in Python and How to Set Up Virtual Environment'
tags = ["Python", "Virtual Environment"]
+++

A bit of theory first...

If you are using Python, you will always need to use a virtual environment if you want to keep the packages you installed as organized and neat as possible. So basically virtual environment will make it possible to have different versions of the same library or framework for each project. Let's say you are a great Django developer (I believe you are :D), and for god knows whatever reason you want to have _Django version 3.0.0_ for one project and _version 3.2_ for another. So in this case, what you can do is you can create a separate virtual environment for each project and use different versions for packages. Another advantage of using a virtual environment for me is to have the ability to create a _requirements.txt_ file that can be used in the project setup. Well, you can tell me that you can create it without a virtual environment but if you do so, _requirements.txt_ file will contain all of the packages that have been installed globally on your PC. You can see the content by executing:

`pip freeze`

### Enough of theory

We can now take a look at the practical part...

To move on further, the operating system should first be considered. I will cover the content for both Linux and Windows OS.

### For Linux/Unix:

Well, the conventional way to create a virtual environment is:

`python3 -m venv venv`

You can change the last argument (_venv)_ as it stands for the name of the virtual environment and you can name it whatever you want.

After you have created the virtual environment, you need to activate it before installing any package inside it. To activate it (assuming its name is _venv_):

`source venv/bin/activate`

and use the below snippet to deactivate it:

`deactivate`

To be honest, activation snippet has always seemed really long to me, as well as creating. But in Linux, you can give _Alias_ to almost any command you like. Just go to _the ~/.bashrc_ file _(\_might be _~/.zshrc),\_ and add alias-es for both commands, namely creating and activating:

```sh
# create alias
alias cvenv="python -m venv venv"
# activate alias
alias activate="source venv/bin/activate"
```

From now on, you can just do _cvenv_ to create and _activate_ to activate the virtual environment.

Goodbye Linux user, now it's time for Windows users (I know you might not like this part, but you can always drop a "I use Arch btw" comment in the comment section :D.

### For Windows:

To create the virtual environment use the below command:

`python -m venv venv`

You can change the last argument (_venv)_ as it stands for the name of the virtual environment and you can name it whatever you want.

To activate it:

`venv\Scripts\activate`

If commands look really long for you, you can use [_virtualenvwrapper-win_](https://pypi.org/project/virtualenvwrapper-win/) package to simplify the process. To install it:

`pip install virtualenvwrapper-win`

You can now create and activate virtualenv as below:

```sh
# to create virtual environment
mkvirtualenv venv

# to activate
workon venv
```

If you use this specific tool, bear in mind that, virtual environment directory might not be stored in the same directory that you have executed the commands above.

You might have a question in your mind that, well, if it's stored in another directory, how I can manage them. Well, you can always list all the virtual environments you created so far by executing:

`lsvirtalenv`

and delete any of them by executing:

`rmvirtualenv venv`

_venv_ above is the name of the virtual environment.

### Creating _requirements.txt_ File

You can always first see the content of _requirements.txt_ by running:

`pip freeze`

To echo it into a file you can run:

`pip freeze > requirements.txt`

This will freeze out the names of all installed packages of your virtual environment into _requirements.txt_ file with their exact version.

To install from _requirements.txt_ you can execute the command below:

`pip install -r requirements.txt`

Thanks for reading!
