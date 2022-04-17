# archsetup
## archinstall
```
python -m archinstall --config https://raw.githubusercontent.com/mattsiedenburg/archsetup/main/config.json
```
## pacman config
```
sudo vim /etc/pacman.conf
```
### enable multilib
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```
### enable color
```
Color
```
## reflector
```
sudo pacman -S reflector-mirrorlist-update
```
### one-time execution
```
sudo reflector --country 'United States' --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```
### edit config
```
sudo vim /etc/xdg/reflector/reflector.conf
```
### enable timer
```
sudo systemctl enable reflector.timer
```
### aur/yay
```sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
```
### octopi
```
yay -S octopi alpm_octopi_utils octopi-notifier-qt5
```
## hardware
### bluetooth
```
sudo systemctl enable bluetooth.service --now
```
### prime
```
yay -S nvidia-prime
```
### touchpad
```
yay -S touchegg touche
sudo systemctl enable touchegg.service --now
```
### ssd trim
```
yay -S util-linux --needed`
sudo systemctl enable fstrim.timer --now
```
### power
```
yay -S gnome-power-manager power-profiles-daemon thermald
sudo systemctl enable power-profiles-daemon.service --now
sudo systemctl enable thermald.service --now
```
### microcode update
```
yay -S --needed intel-ucode
```
## packages
### os
```
yay -S gnome-tweaks man-db man-pages chrome-gnome-shell extension-manager
```
### general
```
yay -S firefox transmission-gtk zoom
```
### media
```
yay -S spotify audacious vlc
```
### gaming
```
yay -S steam lutris minecraft-launcher
```
### text editors
```
yay -S sublime-text-4 visual-studio-code-bin
```
### docker
```
yay -S docker docker-compose
sudo systemctl enable docker.service --now
sudo usermod -a -G docker $USER
newgrp docker
```
### k8s
```
yay -S kubectl helm minikube kind-bin
echo 'source <(kubectl completion zsh)' >> $HOME/.zshrc
echo 'source <(helm completion zsh)' >> $HOME/.zshrc
echo 'source <(minikube completion zsh)' >> $HOME/.zshrc
echo 'source <(kind completion zsh)' >> $HOME/.zshrc
```
### libvirt/kvm/qemu
```
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
### zsh
```
yay -S zsh
```
### oh my zsh
```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
### nerd fonts
```
yay -S nerd-fonts-complete
```
### just symbols
```
yay -S ttf-nerd-fonts-symbols
```
### p10k
```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
### add to .zshrc
```
ZSH_THEME="powerlevel10k/powerlevel10k"
```
### zsh plugins
### zsh-syntax-highlighting
```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
### zsh-completions
```
git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
```
### zsh-autosuggestions
```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
### zsh-history-substring-search
```
git clone https://github.com/zsh-users/zsh-history-substring-search ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-history-substring-search
```
### add to .zshrc:
```
plugins=(
  git
  zsh-syntax-highlighting
  zsh-autosuggestions
  zsh-history-substring-search
  docker
  docker-compose
  )
fpath+=${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions/src
```