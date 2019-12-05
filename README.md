# Wosmini
## Installation guide Arch Linux ARM with HDD/SSD
```bash
wget http://os.archlinuxarm.org/os/ArchLinuxARM-rpi-4-latest.tar.gz
sudo fdisk -l
sudo umount /dev/sdX1 /dev/sdX2 ...
sudo fdisk /dev/sdX
```
```bash
o
n
p
+100M
t
c
w
```
```bash
sudo umount /dev/sdS1 /dev/sdS2 ...
sudo fdisk /dev/sdS
```
```bash
o
n
p
+40G

n
p
w
```
```bash
sudo mkfs.vfat /dev/sdX1
sudo mkfs.ext4 /dev/sdS1
sudo mkfs.ext4 /dev/sdS2
sudo mkdir wmini
sudo mount /dev/sdS1 wmini
sudo mkdir wmini/boot
sudo mkdir wmini/home
sudo mount /dev/sdX1 wmini/boot
sudo mount /dev/sdS2 wmini/home
sudo bsdtar -xpf ArchLinuxARM-rpi-4-latest.tar.gz -C wmini
sudo sync
sudo blkid | grep sdb
/dev/sdb1: UUID="e32e2726-803a-4bd0-a1de-9b935dbb2246" TYPE="ext4" PARTUUID="5572f842-01"
/dev/sdb2: UUID="8099802f-f372-4ede-b147-edb7ffa7b9ed" TYPE="ext4" PARTUUID="5572f842-02"
cd wmini
sudo vim boot/cmdline.txt
```
#
```python
root=PARTUUID=5572f842-01 rw rootwait console=ttyAMA0,115200 console=tty1 selinux=0 plymouth.enable=0 smsc95xx.turbo_mode=N dwc_otg.lpm_enable=0 kgdboc=ttyAMA0,115200 elevator=noop
```
---
```bash
sudo blkid | grep sdX
/dev/sdX1: SEC_TYPE="msdos" UUID="600E-E4D5" TYPE="vfat" PARTUUID="8224ec18-01"
sudo vim etc/fstab
```
#
```python
PARTUUID=8224ec18-01    /boot    vfat    defaults            0    0
PARTUUID=5572f842-01    /        ext4    defaults,discard    0    1
PARTUUID=5572f842-02    /home    ext4    defaults,discard    0    2
```
---
```bash
sudo cp etc/fstab boot/fstab_usb
sudo vim boot/config.txt
```
#
```python
...
device_tree_param=audio=on
disable_overscan=1
disable_audio_dither=1
```
---
```bash
sudo vim etc/hostname
```
#
```python
wmini
```
---
```bash
sudo vim etc/hosts
```
#
```python
127.0.0.1	localhost
::1		localhost
127.0.1.1	wmini.localdomain	wmini
```
---
```bash
sudo vim etc/locale.gen
```
#
```python
en_US.UTF-8 UTF-8
```
---
```bash
sudo vim etc/locale.conf
```
#
```python
LANG=en_US.UTF-8
```
---
```bash
cd
sudo umount wmini/boot wmini/home wmini
```
Insert cards into the Wmini ad login with root/root
```bash
locale-gen
pacman-key --init
pacman-key --populate archlinuxarm
pacman -Syu
useradd -m -G users -s /bin/bash user
passwd user
userdel -r alarm
pacman -S gnome gnome-flashback gnome-keyring gnome-tweaks gnome-applets xf-video-fbdev xorg-server xorg-xinit gvim network-manager-applet ttf-dejavu ttf-droid xmonad xmonad-contribi dmenu base-devel
systemctl enable NetworkManager.service
systemctl start gdm.service
```
Login with user/user
```bash
cp /etc/X11/xinit/xinitrc ~/.xinitrc
vim ~/.xinitrc
```
#
```python
...
exec xmonad
```
---
```bash
mkdir .xmonad
vim .xmonad/xmonad.hs
```
#
```python
import XMonad

main = xmonad def
    { terminal    = "urxvt"
    , modMask     = mod4Mask
    , borderWidth = 3
    }
```
---
```bash
xmonad --recompile
su
systemctl enable gdm.service
passwd
passwd user
pacman -Rs gnome-software gnome-music
pacman -S ntfs-3g android-file-transfer
pacman -S chromium vlc libreoffice-fresh gimp git clipgrap
cd Downloads
git clone https://aur.archlinux.org/bluez-utils-compat.git
cd bluez-utils-compat
gpg --recv-keys XXXXXXXXXXXXXXXX
makepkg -sci
git clone https://aur.archlinux.org/pi-bluetooth.git
cd pi-bluetooth
makepkg -sci
```
