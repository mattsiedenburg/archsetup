
# archsetup

Documenting my setup. I don't want to automate this with ansible/scripting but wanted to be able to pick and choose depending on my usecase and hardware.

## Contents

- [setup](#setup)
  - [wipefs](#wipefs)
  - [connect to wi-fi](#connect-to-wi-fi)
  - [archinstall](#archinstall)
  - [swap](#swap)
- [pacman and aur](#pacman-and-aur)
  - [pacman](#pacman)
  - [reflector](#reflector)
  - [yay](#yay)
- [hardware](#hardware)
  - [power](#power)
  - [bluetooth](#bluetooth)
  - [prime](#prime)
  - [ssd trim](#ssd-trim)
  - [microcode](#microcode)
  - [prime](#prime)
- [kernel](#kernel)
  - [zen](#zen)
  - [lts](#lts)
  - [dkms](#dkms)
  - [grub](#grub)
- [packages](#packages)
  - [documentation](#documentation)
  - [internet](#internet)
  - [gaming](#gaming)
  - [text editors and dev tools](#text-editors-and-dev-tools)
- [virtualization and containers](#virtualization-and-containers)
  - [docker](#docker)
  - [kubernetes](#kubernetes)
  - [libvirt](#libvirt)
  - [vagrant](#vagrant)
- [zsh](#zsh)
  - [history](#history)
  - [plugins](#plugins)
  - [starship](#starship)
  - [antigen](#antigen)
  - [antigen setup](#antigen-setup)
- [misc](#misc)
  - [tools](#tools)
  - [fonts](#fonts)
  - [alacritty](#alacritty)
- [fish](#fish)
  - [configure fish](#configure-fish)
- [github](#github)
  - [ssh key](#ssh-key)
  - [test](#test)
  - [config](#config)

## setup

### wipefs

Necessary in some circumstances where archinstall is unable to format the disk.

All data will be lost.

NVMe: `/dev/nvme0n1`

SATA: `/dev/sda`

```bash
wipefs -a /dev/nvme0n1
```

### connect to wi-fi

Needed if using Wi-Fi for installation.

```bash
iwctl
```

Device name is typically `wlan0`.

Substitute `MyWiFiSSID` with actual SSID.

```bash
station wlan0 connect MyWiFiSSID
```

### archinstall

```bash
pacman -Sy archinstall --noconfirm
archinstall
```

### swap

Only necessary if swap/zram is not selected with archinstall.

```bash
sudo dd if=/dev/zero of=/swapfile bs=1M count=4096 status=progress
sudo chmod 0600 /swapfile
sudo mkswap -U clear /swapfile
sudo swapon /swapfile
```

Update `/etc/fstab`.

```bash
sudo vim /etc/fstab
```

Contents of `/etc/fstab`:

```bash
/swapfile none swap defaults 0 0
```

## pacman and aur

### pacman

```bash
sudo vim /etc/pacman.conf
```

Uncomment the following lines in `/etc/pacman.conf`:

```bash
Color

[multilib]
Include = /etc/pacman.d/mirrorlist
```

```bash
sudo pacman -Syu
```

### reflector

```bash
sudo pacman -S reflector --needed
```

Set country code: `US`

```bash
sudo vim /etc/xdg/reflector/reflector.conf
```

Contents of `/etc/xdg/reflector/reflector.conf`:

```bash
--country US
```

```bash
sudo systemctl enable reflector.timer --now
```

### yay

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
cd ..
rm -rf yay-bin
```

## hardware

### power

```bash
yay -S power-profiles-daemon --needed
sudo systemctl enable power-profiles-daemon.service --now
```

AMD Ryzen only, for reading power statistics using mangohud.

```bash
yay -S zenpower3-dkms --needed
```

Intel only

```bash
yay -S thermald --needed
sudo systemctl enable thermald.service --now
```

### bluetooth

If a Bluetooth adapter installed.

```bash
sudo systemctl enable bluetooth.service --now
```

### ssd trim

Installed and enabled by default when using archinstall.

```bash
yay -S util-linux --needed
sudo systemctl enable fstrim.timer --now
```

### microcode

Microcode installed automatically when using archinstall.

Intel

```bash
yay -S intel-ucode --needed
```

AMD

```bash
yay -S amd-ucode --needed
```

### prime

Nvidia hybrid graphics laptop only.

```bash
yay -S nvidia-prime --needed
```

## kernel

### zen

```bash
yay -S linux-zen linux-zen-headers --needed
```

### lts

```bash
yay -S linux-lts linux-lts-headers --needed
```

### dkms

Nvidia hybrid graphics only, needed only when running non-mainline kernel.

```bash
yay -S nvidia-dkms --needed
```

### grub

If dual booting, install `os-prober`

```bash
yay -S os-prober --needed
```

If using more than one kernel, edit grub.

```bash
sudo vim /etc/default/grub
```

Contents of `/etc/default/grub`:

```bash
GRUB_DEFAULT=saved
...
GRUB_SAVEDEFAULT=true
...
GRUB_DISABLE_SUBMENU=y
...
GRUB_DISABLE_OS_PROBER=false # if dual booting
```

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## packages

### documentation

```bash
yay -S man-db \
    man-pages \
    tldr --needed
```

### internet

```bash
yay -S firefox --needed
```

### gaming

```bash
yay -S steam \
    gamemode \
    gamescope \
    mangohud \
    goverlay \
    minecraft-launcher --needed
```

### text editors and dev tools

```bash
yay -S vim \
    sublime-text-4 \
    visual-studio-code-bin \
    cursor-bin \
    git --needed
```

## virtualization and containers

### docker

```bash
yay -S docker docker-compose --needed
sudo systemctl enable docker.service --now
sudo usermod -a -G docker $USER
```

### kubernetes

```bash
yay -S kubectl \
    helm \
    minikube \
    kind-bin \
    k9s --needed
```

Contents of `~/.zshrc`:

```bash
source <(kubectl completion zsh)
source <(helm completion zsh)
source <(minikube completion zsh)
source <(kind completion zsh)
```

### libvirt

```bash
yay -S libvirt \
    qemu \
    virt-manager \
    iptables-nft \
    dnsmasq \
    dmidecode \
    bridge-utils \
    openbsd-netcat \
    virt-manager --needed

sudo systemctl enable libvirtd.service --now
sudo usermod -a -G libvirt $USER
```

### vagrant

```bash
yay -S vagrant --needed
vagrant plugin install vagrant-libvirt
```

## zsh

```bash
yay -S zsh --needed
touch ~/.zshrc
chsh -s $(which zsh)
```

### history

Configure zsh history.

Contents of `~/.zshrc`:

```bash
HISTFILE=~/.zsh_history
HISTSIZE=1001
SAVEHIST=1000
setopt EXTENDED_HISTORY
setopt HIST_EXPIRE_DUPS_FIRST
setopt HIST_FIND_NO_DUPS
setopt HIST_IGNORE_ALL_DUPS
setopt HIST_IGNORE_DUPS
setopt HIST_IGNORE_SPACE
setopt HIST_REDUCE_BLANKS
setopt HIST_SAVE_NO_DUPS
setopt INC_APPEND_HISTORY
```

### plugins

```bash
yay -S zsh-syntax-highlighting \
    zsh-history-substring-search \
    zsh-autosuggestions \
    zsh-autocomplete --needed
```

Contents of `~/.zshrc`:

```bash
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source /usr/share/zsh/plugins/zsh-history-substring-search/zsh-history-substring-search.zsh
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh/plugins/zsh-autocomplete/zsh-autocomplete.plugin.zsh
```

### starship

```bash
yay starship --needed
starship preset gruvbox-rainbow -o ~/.config/starship.toml
```

Contents of `~/.zshrc`:

```bash
eval "$(starship init zsh)"
```

### antigen

In lieu of starship and manual zsh configurations above:

```bash
yay -S antigen --needed
```

### antigen setup

Contents of `~/.zshrc`:

```bash
source /usr/share/zsh/share/antigen.zsh

antigen use oh-my-zsh

antigen bundle git
antigen bundle command-not-found
antigen bundle docker
antigen bundle docker-compose

antigen bundle zsh-users/zsh-syntax-highlighting
antigen bundle zsh-users/zsh-autosuggestions
antigen bundle zsh-users/zsh-history-substring-search
antigen bundle zsh-users/zsh-completions

antigen theme romkatv/powerlevel10k

antigen apply
```

## misc

### tools

```bash
yay -S bat \
    eza \
    ripgrep \
    fzf --needed
```

Contents of `~/.zshrc`:

```bash
alias cat='bat'
alias less='bat'
alias ls='eza'
alias ll='eza -ahl --group-directories-first --icons --color always'
alias tree='eza --tree'
```

### fonts

```bash
yay -S ttf-firacode-nerd ttf-nerd-fonts-symbols --needed
```

### alacritty

```bash
yay -S alacritty alacritty-themes --needed
```

Contents of `~/.alacritty.toml`:

```ini
[general]
import = [
  "/usr/lib/node_modules/alacritty-themes/themes/Gruvbox-Dark.toml"
]

[font]
size = 18

  [font.normal]
  family = "FiraCode Nerd Font"

  [font.bold]
  family = "FiraCode Nerd Font"

  [font.italic]
  family = "FiraCode Nerd Font"

  [font.bold_italic]
  family = "FiraCode Nerd Font"
```

## fish

```bash
yay -S fish --needed
touch ~/.config/fish/config.fish
chsh -s $(which fish)
```

### configure fish

Contents of `~/.config/fish/config.fish`

```bash
if status is-interactive
    set -U fish_greeting "🐟"
    set -U EDITOR vim
    set -U BROWSER firefox

    function ll
        eza -Ahl --group-directories-first --icons --color always $argv
    end

    function tree
        eza -A --tree --icons --color always $argv
    end

    function cat
        bat
    end

    function less
        bat
    end

    function yyy
            yay --noconfirm && yay -Yc --noconfirm && yay -Sc --noconfirm
    end
end
```

## github

### ssh key

```bash
ssh-keygen
```

### test

Add public key to github before testing.

```bash
ssh -T git@github.com
```

### config

```bash
git config --global user.name 'Matt Siedenburg'
git config --global user.email 'email@domain.com'
```
