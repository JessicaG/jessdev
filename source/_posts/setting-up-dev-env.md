---
title: Setting up your development environment from scratch
date: 2014-06-05
cover_index: /assets/computer-camera.jpg
cover_detail: /assets/computer-camera.jpg
---

It's my first week at Turing, hooray! We had to set up our machines to
get them ready for working in a development environment. I've only done this
a few times before now and I thought I should document it so moving
forward I have a reference and anyone else who may be coming after me
could have a resource!

## Software Updates
First, we want to check for any software updates, because even though
it's a new computer, there's almost always an update. Super fun.

- Check for any OS software updates on App Store

## Update sudoers file
There are a some installations that will stop part way through for a
sudo, and I find it obnoxious, so I usually change this off the bat.

```
sudo visudo
```

Sudo allows you to run commands as a user with higher access rights, which is why it prompts you for your password

Once in file, change
```
%admin ALL=(ALL) ALL
```
to
```
%admin  ALL=(ALL) NOPASSWD: ALL
```

## Xcode Install
Next up, our favorite thing, XCode! You can get by with just the command
line tools, but there are some instances where you may need the whole
thing EDIT(updated below when I started using MacVim).

```
xcode-select --install
```

## Install homebrew
If you don't know what homebrew is, I highly highly recommend using it.
Basically it allows you to install and update software via this magical
package manager rather than grab the URLs each time. You can read more about
HomeBrew here: [http://brew.sh]

- run command

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Once the installation is successful, run the following command:

```
brew doctor
```

If you get "Your system is ready to brew", you can move on to installing rvm and ruby.

Otherwise, try a few of these troubleshooting tips:

Most cases, Homebrew will provide helpful instructions for with warnings and errors and you can follow those step by step. Here are a few common ones:

- If you get:

```
Warning: /usr/bin occurs before /usr/local/bin
```

- run command

```
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bash_profile
```

This command takes everything between the single quotes and adds it (>>) to a file called .bash_profile in your user’s root directory (~/). Every time you open a new Terminal window or tab, .bash_profile is called. The export PATH line tells your system to look in /usr/local/bin first, since that’s where Homebrew installs tools.

- If you get:

```
No such file or directory - /usr/local/Cellar
```

- run command

```
mkdir /usr/local/Cellar
```

This creates the /usr/local/Cellar directory

mkdir stands for “make directory”, which you'll use when creating
folders from your command line (no gui!).

- If you get:

```
/usr/local/etc isn't writable
```
or
```
Cannot write to /usr/local/Cellar
```

- run command

```
sudo chown -R `whoami` /usr/local
```

This makes you the owner of the /usr/local directory, in addition to all nested directories.

chown stands for “change owner”, the -R flag applies this to all nested files and directories, and whoami is a variable that represents your OS X username. You should copy and paste the command above as is.

To learn more about any Unix command, type man (for “manual”), followed by the command.

For example:

```
man chown
```

If a manual is longer than a page long, it will display a : at the end to signify there is more to read. To display one additional line at a time, press return. To display an additional page at a time, press the space bar. To quit at any time, press q.

Hopefully you've had no trouble, and you're ready to move on!

## Install rvm and ruby
Next up, RVM and Ruby! RVM stands for [Ruby Version Manager](https://rvm.io) . This is my preference as I've been using it for a few years, but there is also a
[rbenv] (http://rbenv.org) and they essentially do the same thing, so it's a user preference. Both of these are used to manage different versions of Ruby, so you can have projects using 1.9.3 or 2.0.0.

- run command

```
\curl -sSL https://get.rvm.io | bash -s stable
```

- close terminal windows and reopen
- run command for ruby

```
rvm install 2.2.2
```
**Note, I've updated this to use the latest version of ruby, so be sure
to check which one is the most stable before installing.**

Check to see you've installed the correct version

- run command

```
ruby -v
```

This should give you the version number of ruby for whichever directory you are in.

## Install git
Wooo, Git! The best thing, really. Git, simply put, is a version control
system and you'll use it ALL the time and probably never fully
understand it.

- run command
```
brew install git
```

## Install mysql && postgres
I like to have both of these pending on the projects, I usually only
have postgres for cases of deploying to Heroku.

- run command(s) Mysql

```
brew install mysql
mkdir -p ~/Library/LaunchAgents
ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

- run command(s) Postgres

```
 brew install postgresql
 initdb /usr/local/var/postgres -E utf8
```

- Helpful gem for starting and stopping a server on postgres

```
gem install lunchy
```

Note, these versions will be specific to your machine, this is my version of Postgres that was installed on my machine. If the version number has changed since I’ve written this, using the command below won’t work for you, so you should make sure to copy the command from your Terminal output.

```
 mkdir -p ~/Library/LaunchAgents
$ cp /usr/local/Cellar/postgresql/9.2.1/homebrew.mxcl.postgresql.plist ~/Library/LaunchAgents/
```

Now you can start and stop postgres simply with these commands:

```
lunchy start postgres
```
and
```
lunchy stop postgres
```

## Install vim
You'll need this for editing commit messages and working with servers,
good to have even if it's not your preferred editor.

```
brew install vim
```

EDIT: Using MacVim months later and had to install all of Xcode

##Install MacVim
- install Xcode
```
brew install macvim
```

I like to use the theme 'jellybeans', but you can choose whichever is
easiest. MacVim was a nice ease into vim from my Sublime and Atom
editor.

EDIT: Now using Tmux for work and it's amazing for having everything in
one place.

##Install tmux

- run command

```
brew install tmux
```

##Setup tmuxinator

- run command

```
gem install tmuxinator
```

- see what $EDITOR is set to

```
echo $EDITOR
```

- if not set, set it

```
export EDITOR='vim' #add to bash_profile to make permanent
```

- open your 'project' and set it up

```
mux open project_name
```

- configure windows, panes, etc.

- to start working

```
mux project_name
```

## Install [Seil](https://pqrs.org/osx/karabiner/seil.html.en) to map Caps Lock to Esc

## Install janus for VIM Rails editing
```
curl -Lo- https://bit.ly/janus-bootstrap | bash
```
## Install Ack for code searching

```
brew install ack
```

And that's about it! You should be all set to go on your environment to
start coding plus some. Hope this helped, enjoy!
