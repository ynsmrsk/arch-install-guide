# Arch Linux Installation Guide
Check uefi is enabled
<pre>$ ls /sys/firmware/efi/efivars</pre>

Connect to the internet
<pre>$ wifi-menu</pre>

Update the system clock
<pre>$ timedatectl set-ntp true</pre>

Partition the disk
<pre>$ lsblk</pre>

<pre>
$ fdisk
m help
1.boot   2.swap   3.root   4.home
</pre>
(notice to select last part as primary)

Format the partitions
<pre>
$ mkfs.ext4 /dev/sda1
$ mkswap /dev/sda2
$ swapon /dev/sda2
$ mkfs.ext4 /dev/sda3
$ mkfs.ext4 /dev/sda4
</pre>

Create dirs will be mounted
<pre>
$ mkdir /mnt/boot
$ mkdir /mnt/home
</pre>

Mount the file systems
<pre>
$ mount /dev/sda3 /mnt
$ mount /dev/sda1 /mnt/boot
$ mount /dev/sda4 /mnt/home
$ lsblk
</pre>

Configure mirrorlist
<pre>$ vim /etc/pacman.d/mirrorlist</pre>
Install essential packages
<pre>$ pacstrap /mnt base neovim linux linux-firmware</pre>

Generate fstab file (auto mount)
<pre>$ genfstab -U /mnt >> /mnt/etc/fstab</pre>
Check the resulting file
<pre>$ nvim /mnt/etc/fstab</pre>

Change root into the new system
<pre>$ arch-chroot /mnt</pre>

Set time zone
<pre>$ ln -sf /usr/share/zoneinfo/Europe/Istanbul /etc/localtime</pre>
Set hardware clock to generate /etc/adjtime
<pre>$ hwclock --systohc</pre>

Uncomment preferred locales
<pre>$ nvim /etc/locale.gen</pre>
Generate locale file
<pre>$ locale-gen</pre>

Create locale.conf, set the LANG variable
<pre>$ localectl set-locale LANG="en_US.UTF-8"</pre>

<pre>$ echo hostName > /etc/hostname</pre>
<pre>
$ nvim /etc/hosts
<hr>
127.0.0.1     localhost
::1		        localhost
127.0.1.1	    myhostname.localdomain	myhostname
</pre>

Set the root password
<pre>$ passwd</pre>
Add user
<pre>$ useradd -m -g users -G wheel -s /bin/bash userName</pre>
Create password for new user
<pre>$ passwd username</pre>

Enable sudo commands without password
<pre>$ sudo EDITOR=nvim visudo</pre>
Uncomment the following line
<pre>%wheel ALL=(ALL) NOPASSWD: ALL</pre>

Install necessary packages
<pre>
$ pacman -Syyu
dialog wpa_supplicant network-manager-applet
pulseaudio alsa-utils volumeicon
grub os-prober intel-ucode
sudo grep make gcc linux-headers
kitty vifm curl xclip unclutter libinput
chromium feh gcolor2 
xorg-server xorg-xinit xorg-setxkbmap 
</pre>

Install grub on new system
<pre>$ grub-install /dev/sda</pre>
Fuck pcieport error
<pre>
$ nvim /etc/default/grub
<hr>
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet splash pci=nomsi"
</pre>
Create grub config file
<pre>$ grub-mkconfig -o /boot/grub/grub.cfg</pre>

Clone dotfiles
<pre>$ git clone https://www.github.com/ynsmrsk/arch-dotfiles.git</pre>

Install vim-plug
<pre>
$ curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs
  https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
</pre>

Install yay and packages
<pre>
$ cd /tmp
$ git clone https://aur.archlinux.org/yay.git
$ cd yay
$ makepkg -si
$ cd ..
$ rm -rf yay
$ cd ~/
<hr>
$ yay -S i3-gaps-rounded-git
$ yay -S picom-ibhagwan-git
</pre>

Exit the chroot session
<pre>$ exit</pre>

Unmount the file system
<pre>$ umount -R /mnt</pre>

Reboot and remove usb
<pre>$ reboot</pre>

If wifi-menu fucks up
<pre>
$ wifi-menu -o wlp3s0
$ netctl
</pre>

Enable network manager
<pre>$ sudo systemctl enable NetworkManager</pre>
