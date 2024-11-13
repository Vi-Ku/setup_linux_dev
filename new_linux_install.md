# New Linux Install

## Create GitHub Account

## Adding Repositories in Other Distros (Debian)

```
sudo apt-get install software-properties-common
```

## Git
In terminal (to paste it in the terminal, you need to use Ctrl + Shift + V):
```
sudo apt install -y git
```

Install GitHub official CLI (Command Line Interface) with the following commands:


```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key C99B11DEB97541F0
sudo apt-add-repository https://cli.github.com/packages
sudo apt update
sudo apt install -y gh
```

To check that gh has been successfully installed on your machine, you can run:
```
gh --version
```

If you don't get a prompt saying gh version X.Y.Z (YYYY-MM-DD) with at least version 1.4, please refer to the documentation where they list some troubleshooting information.

## Text Editor - Sublime Text

```
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
```

This command will ask for your password with: sudo password for username:. Don't panick! Calmy type your password key by key. You won't have a visual feedback, that's perfectly normal, keep on typing. When you're done, hit Enter

```
sudo apt install -y apt-transport-https
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt update
sudo apt install -y sublime-text
```

Sublime Text is free without any time limitation but a popup will appear every ten saves to remind you there is a license to buy. You can hit Esc when this happens, but feel free to buy Sublime Text if you really like this one (there are alternatives).

## Fancy your Terminal - Oh-my-zsh
We will use the shell named zsh instead of bash, the default one.
```
sudo apt install -y zsh curl vim imagemagick jq
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# it will ask for your session password
```

To make this change stick, restart your laptop (or virtual machine):

```
sudo reboot
```

## GitHub

We need to generate SSH keys which are going to be used by GitHub and Heroku to authenticate you. Think of it as a way to log in, but different from the well known username/password couple. If you already generated keys that you already use with other services, you can skip this step.

Open a terminal and type this, replacing the email with yours (the same one you used to create your GitHub account). It will prompt for information. Just press enter until it asks for a passphrase.

```
mkdir -p ~/.ssh && ssh-keygen -t ed25519 -o -a 100 -f ~/.ssh/id_ed25519 -C "TYPE_YOUR_EMAIL@HERE.com"
```

NB: when asked for a passphrase, put something you want (and that you'll remember), it's a password to protect your private key stored on your hard drive. You'll type, nothing will show up on the screen, that's normal. Just type the passphrase, and when you're done, press Enter.

Then you need to give your public key to GitHub. Run:

```
cat ~/.ssh/id_ed25519.pub
```

It will prompt on the screen the content of the id_ed25519.pub file. Copy that text from ssh to the end of your email address.

Go to github.com/settings/ssh - Click on the green button New SSH key - Fill in the Title with your computer name (Macbook Pro for instance) - Paste the Key - Finish by clicking on the Add key green button.

To check that this step is completed, in the terminal run this.
```
ssh -T git@github.com
```

You will be prompted a warning, type yes then Enter.

This is the expected result:
```
# Hi --------! You've successfully authenticated, but GitHub does not provide shell access
```

If you got this message, the keys were added successfully!

If `ssh -T git@github.com` does not work

Try running this command before trying again:

```
ssh-add ~/.ssh/id_ed25519
```

## GitHub Cli
It should already be installed on your laptop from the previous commands. First you need to login:

```
gh auth login -s 'user:email' -w
```

You will get the following output:

```
- Logging into github.com

! First copy your one-time code: 0EF9-D015
- Press Enter to open github.com in your browser...
```

Select and copy the code (0EF9-D015 in the example), then type Enter. Your browser will open and ask you to authorize GitHub CLI to use your GitHub account. Accept and wait a bit. Come back to the terminal, type Enter again, and that should be it tada

To check that you are properly connected, type:

```
gh auth status
```

If you get Logged in to github.com as <YOUR USERNAME> , then all good.

```
gh config set git_protocol ssh
```

Finally, create a gist to make sure gh is working:

```
echo "Hello [Le Wagon](https://www.lewagon.com) :wave:" | gh gist create -d "Starting my coding journey..." -f "SETUP_DAY.md" -p -w
```

This line should open your browser on the newly created gist page. See, we've just created a Markdown file!

## Dotfiles

As your configuration is personal, you need your own repository storing it. Forking means that it will create a new repo in your GitHub account, identical to the original one. You'll have a new repository on your GitHub account, $GITHUB_USERNAME/dotfiles. We need to fork because each of you will need to put specific information (e.g. your name) in those files.

Open your terminal and run the following command:

```
export GITHUB_USERNAME=`gh api user | jq -r '.login'`
echo $GITHUB_USERNAME
```

You should see your GitHub username printed. If it's not the case, stop here and try to solve it. There seems to be a problem with the previous step (gh auth).

Time to fork the repo and clone it on your laptop:

```
mkdir -p ~/code/$GITHUB_USERNAME && cd $_
gh repo fork lewagon/dotfiles --clone
```

Run the dotfiles installer.

```
cd ~/code/$GITHUB_USERNAME/dotfiles && zsh install.sh
```

Check the emails registered with your GitHub Account. You'll need to pick one at the next step:

```
gh api user/emails | jq -r '.[].email'
```

Run the git installer:

```
cd ~/code/$GITHUB_USERNAME/dotfiles && zsh git_setup.sh
```

This will prompt you for your name (FirstName LastName) and your email. Be careful you need to put one of the email listed above thanks to the previous gh api ... command. If you don't do that, Kitt won't be able to track your progress.

Please now quit all your opened terminal windows.

## Sublime Text auto-configuration

Open a new terminal and type this:

```
cd ~/code
stt
```

It will open Sublime Text in the context of your current folder. That's how we'll use it.

Close Sublime text and open it again:

```
stt
```

Wait 1 minute for additional packages to be automatically installed (New tabs with text will automatically open, containing documentation for each new package installed). TO follow package installation, you can go to View > Show console.

To check if plugins are installed, open the Command Palette (⌘ + ⇧ + P on OSX, Ctrl + ⇧ + P on Linux), type in Packlist and then Enter, you should see a couple of packages installed (like Emmet).

If you don't, please install all of them manually. The list is referenced [here](https://github.com/lewagon/dotfiles/blob/master/Package%20Control.sublime-settings).

When it's done, you can close Sublime Text.

In Preferences:
"save_on_focus_lost": true,

In Keybindings:
{ "keys": ["ctrl+ç"], "command": "toggle_comment", "args": { "block": false } }

## Installing Ruby (with rbenv)

First, we need to clean up any previous Ruby installation you might have:

```
rvm implode && sudo rm -rf ~/.rvm
# If you got "zsh: command not found: rvm", carry on. It means `rvm` is not
# on your computer, that's what we want!

rm -rf ~/.rbenv
```

Then in the terminal, run:

```
sudo apt install -y build-essential tklib zlib1g-dev libssl-dev libffi-dev libxml2 libxml2-dev libxslt1-dev libreadline-dev
sudo apt clean
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```

Close your terminal and open it again (Alt+F4 and restart it). If you get a warning, just ignore it from now (Ruby is not installed yet).

Now, you are ready to install the latest ruby version and set it as the default version.

Run this command, it will take a while (5-10 minutes)

```
rbenv install 2.6.6
```

Once the ruby installation is done, run this command to tell the system to use the 2.6.6 version by default.

```
rbenv global 2.6.6
```

Then restart your Terminal again (close it and reopen it).

```
ruby -v
```

You should see something starting with ruby 2.6.6p.

## Installing Gems
```
gem install rake bundler rspec rubocop rubocop-performance pry pry-byebug colored http
```

If you encounter the following error:

```
ERROR: While executing gem ... (TypeError) incompatible marshal file format (can't be read) format version 4.8 required; 60.33 given
```

Run the following command:

```
rm -rf ~/.gemrc
```

Rerun the command to install the gems.

Never install a gem with sudo gem install! Even if you stumble upon a Stackoverflow answer (or the Terminal) telling you to do so.

### Additional GEMs
```
gem install nokogiri faker rest-client simple_form rails jekyll
```


## Installing Node (with nvm)
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.0/install.sh | zsh
```

Restart your terminal and run the following:

```
nvm -v
```
You should see a version.

Now let's install node:

```
nvm install 14.15.0
```
When the command returns, run

```
node -v
```
You should see v14.15.0

### Other method
```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## PostgreSQL

In a few weeks, we'll talk about SQL and Databases and you'll need something called PostgreSQL, an open-source robust and production-ready database. Let's install it now.


```
sudo apt install -y postgresql postgresql-contrib libpq-dev build-essential
sudo -u postgres psql --command "CREATE ROLE `whoami` LOGIN createdb;"
```

## Ubuntu inotify

Ubuntu is always tracking changes in your folders and to do this it uses inotify. By default the Ubuntu limit is set to 8192 files monitored.

As programming involves a lot of files, we need to raise this limit. In your terminal run:

```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

## Heroku
Sign up: (https://id.heroku.com/signup)

### Install
```
curl https://cli-assets.heroku.com/install.sh | sh
```

or

```
wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh
```

### Usage
#### Login
```
heroku login
```


## Yarn
```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt-get install yarn
```

### Some Yarn Packages
```
yarn add sortablejs
yarn add markdown
yarn add jquery select2
yarn add bootstrap
yarn add popper.js jquery
yarn add typed.js
yarn add sweetalert
yarn add popper.js jquery bootstrap select2 markdown sortablejs typed.js sweetalert
```
FancyBox

## Extra
### Install video codec H264

On our pedagogical platform (Kitt, you'll soon discover it!), we have some videos. By default Firefox on Linux cannot play them as they use an unsupported codec (H264). To get those videos working for you, you need to run this:

```
sudo apt install libavcodec-extra -y
```

### Install useful terminal tools

tree is a nice tool to visualize a directory tree inside the terminal:

ncdu is a text-based interface disk utility.

htop is an interactive process viewer.

tig is a text-mode interface for git.

```
sudo apt install tree ncdu htop tig
```

### Check-up
Let's check if you successfully installed everything.

Quit all opened Terminal, open a new one and run the following commands:

```
curl -Ls https://raw.githubusercontent.com/lewagon/setup/master/check.rb > _.rb && ruby _.rb || rm _.rb
```

It should tell you if your workstation is ready :)

### Git commands
#### Clone the previously forked Fullstack-Challenges to your computer and add the remote upstream to connect to LeWagon's repo
```
git clone git@github.com:GITHUB_USER/fullstack-challenges.git
git remote add upstream git@github.com:lewagon/fullstack-challenges.git
```
#### If your repo's URL is set to HTTPS (asks login and password every time) instead of SSH
```
git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
```
#### Update local files with remote repo
```
git pull upstream master -X ours
```

### Other Applications
#### SQLite3
```
sudo apt-get install sqlite libsqlite3-dev
```

#### Image Magick
```
sudo apt-get install imagemagick
```

#### Slack
[Slack Download](https://get.slack.help/hc/en-us/articles/212924728-Slack-for-Linux-beta-)
```
sudo snap install slack --classic
sudo apt-get update
sudo apt-get upgrade slack
```

#### Zoom
[Zoom Download](https://zoom.us/download)

#### Teamviewer
[Teamviewer](https://www.teamviewer.com/en/download)

or Remmina

#### GitKraken
[GitKraken](https://snapcraft.io/gitkraken)

#### Other
DBeaver
pgAdmin4
Authy
Cherry Tree
Simet
IMAGE
Darktable
Gimp
KColorChoser (KDE)

VIDEO
DaVinciResolve
KdenLive
Olive Video Editor
OBS Studio
Ffmpeg

AUDIO
Ardor
Audacity
RhythmBox
DeadBeef

BROWSERS
Brave
Libreworlf
Chromium
Vivaldi

TERMINAL
Yakuake (plasma)
Guake
Terminator

speedread
ncdu
esrever
hollywood
no more secrets
cmatrix
htop
pfetch / neofetch
trash-cli
youtube-dl
ranger
sneakers ?

Steam
PDFSam
Thunderbird
VirtualBox
KeyPass X
Dolphin (File Manager)
Conky
Timeshift
LibreOffice
OnlyOffice
Sigil
Rambox
Kodi
Stacer
Calibre
Gnome Tweak Tool
Mega
Simple Note
Synaptic
VLC media player
NitroShare
NotepadQQ
App Outlet
Vim
Etcher
Emax
Pidgin

