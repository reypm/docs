# Table of Contents
1. [Common Configurations](#common-configurations)
   - [Git Configuration](#git-configuration)
     - [Setup Password Less SSH](#setup-passwordless-ssh-login)
   - [Install OhMyZsh](#install-ohmyzsh)
     - [OhMyZSH Recommended Configuration](#ohmyszh-recommended-configuration)
   - [Setup Powerlevel10k Theme](#setup-powerlevel10k-theme)
2. Fedora Server Configuration Snippets
3. Ubuntu Server Configuration Snippets
4. [macOS Configuration Snippets](#macos)
   - [Install Homebrew](#install-homebrew)
   - [Install Bat](#install-bat)
   - [Install Cakebrew](#install-cakebrew)
   - [Cheatsheet](#cheatsheet)
   - Install Parallels Tools
5. [Others](#others)
   - [Proxmox VE Download Backup Files](#proxmox-downloadupload-backup-files)
   - Install Parallels Tools

## Common Configurations
### Git Configuration
```zsh
$ git config --global user.name "<your_name>"
$ git config --global user.email <your_email>
$ git config --global pull.rebase false
$ git config --global core.editor nano
```
### [Setup Passwordless SSH Login](https://linuxize.com/post/how-to-setup-passwordless-ssh-login/)
- Check for existing SSH key pairs. If there are existing keys, you can either use those and skip the next step or
- Backup up the old keys and generate a new one.
```zsh
ls -al ~/.ssh/id_*.pub
```
- Configure your SSH keys (this step can be omitted if you have some keys already and do not want to generate new ones)
```sh
ssh-keygen -t ed25519 -C "<your_email>"
```
- Copy the keys to the server:
```bash
ssh-copy-id -i <path_to_your_ssh_private_key> <remote_username>@<server_ip_address>
```
### Install [OhMyZsh](https://github.com/ohmyzsh/ohmyzsh)
> Note: Latest version macOS comes with ZSH installed
```zsh
$ sudo dnf install zsh # Fedora
$ sudo apt install zsh # Ubuntu

$ chsh -s $(which zsh) # You must log out from your user session and log back in to see this change
$ sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
#### OhMySZH Recommended Configuration
> Note: this is a custom configuration feel free to modify it as needed
```zsh
# ~/.zshrc
zstyle ':omz:update' mode auto
zstyle ':omz:update' frequency 15

plugins=(
  git
  gitfast
  common-aliases
  ssh-agent
  brew
  copypath
  copyfile
  cp
  macos
  node
  nvm
  docker
  docker-compose
)

 # Preferred editor for local and remote sessions
if [[ -n $SSH_CONNECTION ]]; then
  export EDITOR='nano'
else
  export EDITOR='nano'
fi

# The above lines should be added by PowerLevel10k theme when it gets install
source ~/powerlevel10k/powerlevel10k.zsh-theme
# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

# https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/ssh-agent
zstyle :omz:plugins:ssh-agent agent-forwarding on
zstyle :omz:plugins:ssh-agent identities <your_identity_private_file>
zstyle :omz:plugins:ssh-agent quiet yes

# User configuration
source $ZSH/oh-my-zsh.sh
source ~/.zshrc_alias

---
# ~/.zshrc_alias
# shellcheck shell=bash

alias myip="curl https://ipecho.net/plain; echo"
alias cat="bat"
```
### Setup [Powerlevel10k](https://github.com/romkatv/powerlevel10k#oh-my-zsh) Theme
```zsh
$ git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
Close and reopen your terminal or use `omz-reload` to reload the OhMyZSH configuration, and you should be walked through the theme setup process, upon finish you should be able to see the following lines in your `~/.zshrc` configuration (normally it gets added at the very end of the file)
```zsh
source ~/powerlevel10k/powerlevel10k.zsh-theme
# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
```
If the above does not work for you, you can try set `ZSH_THEME="powerlevel10k/powerlevel10k"` in `~/.zshrc` and restart your Terminal or use `omz-reload` to get changes loaded.

## macOS
### [Install Homebrew](https://brew.sh/)
Open your terminal ([iTerm](https://iterm2.com/) suggested) and run the following command:
```zsh
$  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
Run these two commands in your terminal to add Homebrew to your PATH:
```zsh
$ echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/reynierpm/.zprofile
$ eval "$(/opt/homebrew/bin/brew shellenv)"
```
### [Install Cakebrew](https://www.cakebrew.com)
```zsh
$ brew install cakebrew
```
### [Install Bat](https://formulae.brew.sh/formula/bat)
```zsh
$ brew install bat
```
> Note: For Linux check [this docs](https://www.linode.com/docs/guides/how-to-install-and-use-the-bat-command-on-linux)
### Cheatsheet
1. Copy file content to clipboard
```zsh
$ pbcopy < file.txt
```
You can check if the content is there by using the "pbpaste" command:
```bash
$ pbpaste
```

## Others
### [Proxmox Download/Upload Backup Files](https://forum.proxmox.com/threads/how-to-download-upload-backup-files.127656/)
- Log into your machine and read your file `/etc/pve/storage.cfg` to check where PVE is storing the backup files:
```zsh
$ cat /etc/pve/storage.cfg
dir: local
	path /var/lib/vz
	content iso,vztmpl,backup
lvmthin: local-lvm
	thinpool data
	vgname pve
	content rootdir,images

zfspool: zfs_disk
	pool zfs_disk
	content rootdir,images
	mountpoint /zfs_disk
	nodes pve
```
- Go to the directory `/var/lib/vz` and list the contents:
```zsh
$ cd /var/lib/vz/dump/
$ ls -l
total 18618116
-rw-r--r-- 1 root root        1078 May 18 21:15 vzdump-qemu-101-2023_05_18-21_15_44.log
-rw-r--r-- 1 root root        4087 May 25 10:24 vzdump-qemu-101-2023_05_25-10_22_26.log
-rw-r--r-- 1 root root  6590814742 May 25 10:24 vzdump-qemu-101-2023_05_25-10_22_26.vma.zst
-rw-r--r-- 1 root root           7 May 25 10:24 vzdump-qemu-101-2023_05_25-10_22_26.vma.zst.notes
-rw-r--r-- 1 root root        3596 May 18 21:15 vzdump-qemu-103-2023_05_18-21_07_33.log
-rw-r--r-- 1 root root        5962 May 25 17:50 vzdump-qemu-103-2023_05_25-17_47_05.log
-rw-r--r-- 1 root root 12474096384 May 25 17:50 vzdump-qemu-103-2023_05_25-17_47_05.vma.zst
-rw-r--r-- 1 root root          13 May 25 17:50 vzdump-qemu-103-2023_05_25-17_47_05.vma.zst.notes
```
- Copy the files from your machine
```bash
# if you are using macOS
# option 1
scp proxmox:/var/lib/vz/dump/. .
scp -r <user>@<ip_address>:/var/lib/vz/dump/. .

# if you are using Linux/macOS
scp proxmox:/var/lib/vz/dump/\* .
```