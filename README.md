# ubuntu-dev-machine-setup | Ubuntu 24.04 | Linux Mint 22

## Description

This repo contains Ansible playbooks to configure your system as a development machine upon a clean install.

The playbooks should run in Debian based system with minor modifications but was only tested with:

- **Ubuntu 24.04 LTS (Noble Numbat)**

![bullet-train-zsh-theme](.images/screenshot-bullet-train.png)

Screenshot above is using *bullet-train zsh theme*

![pure-zsh-theme](.images/screenshot-pure.png)

Screenshot above is using *pure zsh theme*

![p10k-zsh-theme-tmux](.images/screenshot-p10k-tmux.png)

Screenshot above is using *p10k zsh theme with tmux*

---

## What gets installed and cofigured?

I am a DevSecOps Engineer and my daily job include working with AWS, docker, ansible, terraform, etc. So if you are in a similar profession the installed system will suit your needs. It is also easy to extend using Ansible roles.

Summary of packages that get installed and configured based on roles:

- **role: base**
  - set default system editor to vim instead of nano
  - enable ufw firewall and install ufw graphical frontend gufw
  - disable system crash reports
  - tune system swappiness so that swapping is greatly reduced
  - upgrade all packages
  - install archiving tools like zip, rar, etc
  - install libreoffice
  - install foliate, an e-book reader
  - install glow, apostrophe and Obsidian markdown editors
  - install dive, a tool for exploring each layer in a docker image
  - install development related packages like httpie, clusterssh, docker, filezilla, golang, pipenv, etc
  - install nala, an alternative package management tool to apt/apt-get
  - install code fomatters and linters like black, ruff, ansible-lint, etc
  - setup golang directories
  - install download tools like axel, transmission, wget, aria2
  - install image, audio and video packages like vlc, totem, gimp, imagemagick, etc
  - install virtualization tools
  - enable `fzf` fuzzy finder in zsh terminal; check out this [YouTube video](https://www.youtube.com/watch?v=1a5NiMhqAR0) to see how to use it
  - install terminal emulators Tilix
  - install [lazygit](https://github.com/jesseduffield/lazygit)
- **role: zsh**
  - install zsh package and set user shell to zsh
  - install antigen zsh plugin manager
  - copy and enable sample `~/.zshrc` file if one does not exist
    - contains function to stop ssh-agent from asking for encrypted ssh key password repeatedly when launching new terminal
    - adds additional shell aliases and functions in `~/.shell_aliases` and `~/.shell_functions`
  - install ohmyzsh/ohmyzsh and enable some bundled plugins
  - enable bullet train zsh theme (others like p10k can be configured as well)
- **role: terminal_customizations**
  - download and install some nerd fonts from ryanoasis/nerd-fonts; these are mono fonts ideal for use in terminal or programming editors
  - copy and enable sample tilix config file with configured nerd font
  - copy and enable sample tmux config file if one does not exist
  - copy and enable sample `~/.tmux.conf` file with [tmux plugin manager](https://github.com/tmux-plugins/tpm) and several tmux plugins
    - open Tilix terminal and run `tmux` command, or enable custom command option in Tilix
    - edit `~/.tmux.conf` if necessary
- **role: vim**
  - install vim packages
  - install [amix/vimrc](https://github.com/amix/vimrc) vim distribution
  - create sample vim customization file in `~/.vim_runtime/my_configs.vim`
    - additional vim settings are enabled in `~/.vim_runtime/my_configs.vim` which are not part of the Vim Distribution. Edit this file if necessary.
- **role: neovim**
  - install neovim packages
  - install [lazyvim](https://www.lazyvim.org) neovim distribution
    - open `nvim` from terminal and let the plugins get installed automatically on the initial launch
- **role: vscode**
  - add Visual Studio Code apt repo
  - install Visual Studio Code
  - install some popular Visual Studio Code extensions
- **role: security**
  - install ClamAV (antivirus) and ClamAV GNOME interface. Manual scan from nautilus or from CLI using `clamscan`; clamd not installed for its huge memory footprint
  - install firejail for sanboxing applications
  - enable additional apparmor profiles
- **role: hashicorp**
  - install vagrant, terraform, packer
- **role: googlechrome**
  - add Google Chrome apt repo
  - install Google Chrome

---

## Step 0 | Pre-requisites for running the ansible playbooks

On the system which you are going to setup using Ansible, perform these steps.

You need to install `ansible` and `git` before running the playbooks. You can either install it using `pip` or `apt`.

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt install ansible git -y
```

And clone this repo (do not clone in `/tmp` as this dir is cleaned and mounted in tmpfs)

```bash
git clone https://github.com/istvano/ubuntu-dev-machine-setup.git
cd ubuntu-dev-machine-setup
```

## Step 1 | Running the playbooks to configure your system

**Invoke the following as yourself, the primary user of the system. Do not run as `root`.**

```bash
ansible-playbook main.yml -vv -e "{ laptop_mode: True }" -e "local_username=$(id -un)" -K
```

Enter the sudo password when asked for `BECOME password:`.

The `main.yml` playbook will take anything from 15 minutes to an hour to complete.

After all is done, give your laptop a new life by rebooting.


---

## Known Issues

- If the ansible playbook halts after completing a few tasks, simply run the playbook again. Since most of the tasks are idempotent, running the playbook multiple times won't break anything.
- If your terminal shows any weird characters because of installing one of the zsh themes, simply change the font to a suitable Nerd Font from the terminal's settings.
- If you do not like the fuzzy finder completions in your terminal, remove or comment out the `#fzf` lines in your `~/.zshrc` (this is not a known issue but a feature)
- When launching the terminal, having some [ohmyzsh](https://github.com/ohmyzsh/ohmyzsh) plugins like **docker** enabled results in the error: `tee: <snip> No such file or directory`. You can easily fix this by creating the missing directory manually: `mkdir -p ~/.antigen/bundles/robbyrussell/oh-my-zsh/cache/completions`.

---