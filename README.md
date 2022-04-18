
# archsetup

Documenting my setup.

TODO: Use ansible to automate the post-archinstall setup.

## archinstall

```bash
# Passing multiple URLs doesn't seem to work, so download configs locally

curl https://raw.githubusercontent.com/mattsiedenburg/archsetup/main/\{config.json,disks.json,creds.json\} -O

archinstall --config config.json --disk_layouts disks.json --creds creds.json --silent
```

## pacman config

```bash
sudo vim /etc/pacman.conf
```

### enable multilib

```text
[multilib]
Include = /etc/pacman.d/mirrorlist
```

### enable color

```text
Color
```

### enable parallel downloads

```text
ParallelDownloads = 5
```

### update pacman

```bash
sudo pacman -Syu
```

## reflector

```bash
sudo pacman -S reflector
```

### one-time execution

```bash
sudo reflector --country 'United States' --age 5 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

### edit config

```bash
sudo vim /etc/xdg/reflector/reflector.conf
```

### enable timer

```bash
sudo systemctl enable reflector.timer
```

### aur/yay

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
```

### octopi

```bash
yay -S octopi alpm_octopi_utils octopi-notifier-qt5
```

## hardware

### bluetooth

```bash
sudo systemctl enable bluetooth.service --now
```

### prime

```bash
yay -S nvidia-prime
```

### touchpad

```bash
yay -S touchegg touche
sudo systemctl enable touchegg.service --now
```

### ssd trim

```bash
yay -S util-linux --needed
sudo systemctl enable fstrim.timer --now
```

### power

```bash
yay -S gnome-power-manager power-profiles-daemon thermald
sudo systemctl enable power-profiles-daemon.service --now
sudo systemctl enable thermald.service --now
```

### microcode update

```bash
yay -S --needed intel-ucode
```

## packages

### os

```bash
yay -S gnome-tweaks man-db man-pages chrome-gnome-shell extension-manager
```

### general

```bash
yay -S firefox transmission-gtk zoom
```

### media

```bash
yay -S spotify audacious vlc
```

### gaming

```bash
yay -S steam lutris minecraft-launcher
```

### text editors

```bash
yay -S sublime-text-4 visual-studio-code-bin
```

### docker

```bash
yay -S docker docker-compose
sudo systemctl enable docker.service --now
sudo usermod -a -G docker $USER
newgrp docker
```

### k8s

```bash
yay -S kubectl helm minikube kind-bin

# $HOME/.zshrc
source <(kubectl completion zsh)
source <(helm completion zsh)
source <(minikube completion zsh)
source <(kind completion zsh)
```

### libvirt/kvm/qemu

```bash
yay -S libvirt qemu virt-manager --needed
yay -S iptables-nft dnsmasq dmidecode --needed
yay -S bridge-utils --needed
yay -S openbsd-netcat --needed
yay -S virt-manager --needed

sudo systemctl enable libvirtd.service --now
sudo usermod -a -G libvirt $USER
newgrp libvirt
```

## shell config

### glyphs

```bash
yay -S ttf-nerd-fonts-symbols
```

### zsh

```bash
yay -S zsh
chsh --shell=/bin/zsh
```

### antigen

```bash
yay -S antigen
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
