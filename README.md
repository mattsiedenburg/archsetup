# archsetup





# pacman config
sudo vim /etc/pacman.conf 

# enable multilib
[multilib]
Include = /etc/pacman.d/mirrorlist

# enable color
Color

# reflector

sudo pacman -S reflector-mirrorlist-update

# one-time execution
sudo reflector --country 'United States' --age 5 --protocol https --sort rate --save /etc/pacman.d/mirrorlist

# edit config
sudo vim /etc/xdg/reflector/reflector.conf
sudo systemctl enable reflector.timer --now





# yay/aur

sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si

# octopi
yay -S octopi alpm_octopi_utils octopi-notifier-qt5


# os
yay -S gnome-tweaks man-db man-pages chrome-gnome-shell extension-manager



# other packages

yay -S --needed \
firefox \
transmission-gtk \
zoom

# media
yay -S spotify audacious vlc

# gaming packages

yay -S steam \
lutris \
minecraft-launcher

# dev packages

sublime-text-4
visual-studio-code-bin
atlacritty


# docker
yay -S docker docker-compose
sudo systemctl enable docker.service --now
sudo usermod -a -G docker $USER
newgrp docker




# k8s
yay -S kubectl helm minikube kind-bin


echo 'autoload -Uz compinit && compinit -i' >> $HOME/.zshrc
echo 'source <(kubectl completion zsh)' >> $HOME/.zshrc
echo 'source <(helm completion zsh)' >> $HOME/.zshrc
echo 'source <(minikube completion zsh)' >> $HOME/.zshrc
echo 'source <(kind completion zsh)' >> $HOME/.zshrc


# libvirt/kvm/qemu

yay -S libvirt qemu virt-manager --needed
yay -S iptables-nft dnsmasq dmidecode --needed
yay -S bridge-utils --needed
yay -S openbsd-netcat --needed
yay -S virt-manager --needed

sudo systemctl enable libvirtd.service --now
sudo usermod -a -G libvirt $USER
newgrp libvirt


# shell config

# zsh
yay -S zsh

# oh my zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# all nerd fonts
git clone https://github.com/ryanoasis/nerd-fonts.git

# or just symbols
yay -S ttf-nerd-fonts-symbols

# p10k
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
ZSH_THEME="powerlevel10k/powerlevel10k"

# zsh plugins

# zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# zsh-completions
git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions

# zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# zsh-history-substring-search
git clone https://github.com/zsh-users/zsh-history-substring-search ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-history-substring-search

# add to .zshrc:
plugins=(
  git
  zsh-syntax-highlighting
  zsh-autosuggestions
  zsh-history-substring-search
  docker docker-compose
  )
fpath+=${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions/src




# hardware

# bluetooth
sudo systemctl enable bluetooth.service --now

# prime
yay -S nvidia-prime

# touchpad
yay -S touchegg touche
sudo systemctl enable touchegg.service --now

# trim
yay -S util-linux --needed
sudo systemctl enable fstrim.timer --now

# power
yay -S gnome-power-manager power-profiles-daemon thermald
sudo systemctl enable power-profiles-daemon.service --now
sudo systemctl enable thermald.service --now

# ucode
yay -S --needed intel-ucode

