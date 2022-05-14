
# archsetup

Documenting my setup.

TODO: Use ansible to automate the post-archinstall setup.

# Contents

- [iwctl](#iwctl)
- [archinstall](#archinstall)
- [swap](#swap)
- [pacman](#pacman)
- [aur](#aur)
- [hardware](#hardware)
- [kernel](#kernel)
- [packages](#packages)
- [docker](#docker)
- [kubernetes](#kubernetes)
- [libvirt](#libvirt)
- [zsh](#zsh)
- [github](#github)

## iwctl

```bash
# <DEVICE is typically wlan0>
iwctl --passphrase=<PSK> station <DEVICE> connect <SSID>
```

## archinstall

```bash
# Passing multiple URLs doesn't seem to work, so download configs locally
curl https://raw.githubusercontent.com/mattsiedenburg/archsetup/main/\{config.json,disks.json,creds.json\} -O

archinstall --config config.json --disk_layouts disks.json --creds creds.json --silent && reboot
```

## swap

```bash
# Only necessary if swap/zram is not selected with archinstall
sudo dd if=/dev/zero of=/swapfile bs=1M count=4096 status=progress
sudo chmod 0600 /swapfile
sudo mkswap -U clear /swapfile
sudo swapon /swapfile
sudo vim /etc/fstab

# /etc/fstab
/swapfile none swap defaults 0 0
```

## pacman

```bash
sudo vim /etc/pacman.conf

# /etc/pacman.conf, uncomment the following lines
[multilib]
Include = /etc/pacman.d/mirrorlist
...
Color
...
ParallelDownloads = 5

sudo pacman -Syu
```

### reflector

```bash
sudo pacman -S reflector --needed
sudo vim /etc/xdg/reflector/reflector.conf # optional
sudo systemctl enable reflector.timer --now
```

## aur

### yay

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
cd ..
rm -rf yay-bin
```

### octopi

```bash
yay -S octopi alpm_octopi_utils octopi-notifier-qt5 --needed
```

## hardware

### unmute and set volume level

```bash
pactl set-sink-mute @DEFAULT_SINK@ false
pactl set-sink-volume @DEFAULT_SINK@ 25%
```

### enable bluetooth

```bash
sudo systemctl enable bluetooth.service --now

# /etc/bluetooth/main.conf
[Policy]
...
AutoEnable=true

rfkill unblock bluetooth
```

### prime

```bash
yay -S nvidia-prime --needed
```

### touchpad

```bash
yay -S touchegg touche --needed
sudo systemctl enable touchegg.service --now
```

### ssd trim

```bash
yay -S util-linux --needed
sudo systemctl enable fstrim.timer
```

### power

```bash
yay -S gnome-power-manager power-profiles-daemon thermald --needed
sudo systemctl enable power-profiles-daemon.service --now
sudo systemctl enable thermald.service --now
```

### microcode update

```bash
yay -S intel-ucode --needed
```

## kernel

### zen kernel and nvidia drivers

```bash
yay -S linux-zen linux-zen-headers nvidia-dkms --needed
```

### update grub

```bash
sudo vim /etc/default/grub

# /etc/default/grub
GRUB_DEFAULT=saved
...
GRUB_SAVEDEFAULT=true
...
GRUB_DISABLE_SUBMENU=y
```

### regenerate grub config

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## packages

### general

```bash
yay -S man-db man-pages ntfs-3g --needed
```

### gnome

```bash
# GNOME only
yay -S gnome-tweaks chrome-gnome-shell extension-manager --needed
```

### internet

```bash
yay -S firefox transmission-gtk zoom --needed
```

### media

```bash
yay -S spotify audacious vlc --needed
```

### gaming

```bash
yay -S steam lutris minecraft-launcher gamemode --needed
```

### text editors and dev tools

```bash
yay -S sublime-text-4 visual-studio-code-bin git git-lfs --needed
```


## docker

```bash
yay -S docker docker-compose --needed
sudo systemctl enable docker.service --now
sudo usermod -a -G docker $USER
newgrp docker
```

## kubernetes

```bash
yay -S kubectl helm minikube kind-bin k9s lens-bin --needed

# $HOME/.zshrc
source <(kubectl completion zsh)
source <(helm completion zsh)
source <(minikube completion zsh)
source <(kind completion zsh)
```

## libvirt

```bash
yay -S libvirt qemu virt-manager --needed
yay -S iptables-nft dnsmasq dmidecode --needed
yay -S bridge-utils --needed
yay -S openbsd-netcat --needed
yay -S virt-manager --needed

sudo systemctl enable libvirtd.service --now
sudo usermod -a -G libvirt $USER
newgrp libvirt

yay -S vagrant --needed
vagrant plugin install vagrant-libvirt
```

## zsh

```bash
yay -S zsh --needed
chsh --shell=/bin/zsh
```

### glyphs

```bash
yay -S ttf-nerd-fonts-symbols --needed
```

### bat and exa

```bash
yay -S bat exa --needed

# $HOME/.zshrc
alias ll='exa -ahl --group-directories-first --icons --color always'
alias less='bat'
alias cat='bat -pp'
```

### antigen

```bash
yay -S antigen --needed
```

### antigen setup

```bash
# $HOME/.zshrc
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

## github

### ssh key

```bash
ssh-keygen -t rsa -C "matthewsiedenburg@domain.com"
ssh-add
# Add public key to github before testing
```

### test

```bash
ssh -T git@github.com
```

### git config

```bash
git config --global user.name 'Matt Siedenburg'
git config --global user.email 'matthewsiedenburg@domain.com'
```
