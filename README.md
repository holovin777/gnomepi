# GnomePi
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
sudo blkid | grep sdS
/dev/sdS1: UUID="e32e2726-803a-4bd0-a1de-9b935dbb2246" TYPE="ext4" PARTUUID="5572f842-01"
/dev/sdS2: UUID="8099802f-f372-4ede-b147-edb7ffa7b9ed" TYPE="ext4" PARTUUID="5572f842-02"
sudo vim wmini/boot/cmdline.txt
```
#
```python
root=PARTUUID=5572f842-01 rw rootwait console=ttyAMA0,115200 console=tty1 selinux=0 plymouth.enable=0 smsc95xx.turbo_mode=N dwc_otg.lpm_enable=0 kgdboc=ttyAMA0,115200 elevator=noop
```
---
```bash
sudo blkid | grep sdX
/dev/sdX1: SEC_TYPE="msdos" UUID="600E-E4D5" TYPE="vfat" PARTUUID="8224ec18-01"
sudo vim wmini/etc/fstab
```
#
```python
PARTUUID=8224ec18-01    /boot    vfat    defaults            0    0
PARTUUID=5572f842-01    /        ext4    defaults,discard    0    1
PARTUUID=5572f842-02    /home    ext4    defaults,discard    0    2
```
---
```bash
sudo cp wmini/etc/fstab boot/fstab_usb
sudo vim wmini/boot/config.txt
```
#
```python
...
# For more options and information see
# http://www.raspberrypi.org/documentation/configuration/config-txt.md
# Some settings may impact device functionality. See link above for details

# uncomment if you get no picture on HDMI for a default "safe" mode
#hdmi_safe=1

# uncomment this if your display has a black border of unused pixels visible
# and your display can output without overscan
#disable_overscan=1

# uncomment the following to adjust overscan. Use positive numbers if console
# goes off screen, and negative if there is too much border
#overscan_left=16
#overscan_right=16
#overscan_top=16
#overscan_bottom=16

# uncomment to force a console size. By default it will be display's size minus
# overscan.
#framebuffer_width=1280
#framebuffer_height=720

# uncomment if hdmi display is not detected and composite is being output
#hdmi_force_hotplug=1

# uncomment to force a specific HDMI mode (this will force VGA)
#hdmi_group=1
#hdmi_mode=1

# uncomment to force a HDMI mode rather than DVI. This can make audio work in
# DMT (computer monitor) modes
#hdmi_drive=2

# uncomment to increase signal to HDMI, if you have interference, blanking, or
# no display
#config_hdmi_boost=4

# uncomment for composite PAL
#sdtv_mode=2

#uncomment to overclock the arm. 700 MHz is the default.
#arm_freq=800

# Uncomment some or all of these to enable the optional hardware interfaces
dtparam=i2c_arm=on
#dtparam=i2s=on
dtparam=spi=on

# Uncomment this to enable the lirc-rpi module
#dtoverlay=lirc-rpi

# Additional overlays and parameters are documented /boot/overlays/README

# Enable audio (loads snd_bcm2835)
dtparam=audio=on
```
---
```bash
sudo vim wmini/etc/hostname
```
#
```python
wmini
```
---
```bash
sudo vim wmini/etc/hosts
```
#
```python
127.0.0.1	localhost
::1		localhost
127.0.1.1	wmini.localdomain	wmini
```
---
```bash
sudo vim wmini/etc/locale.gen
```
#
```python
en_US.UTF-8 UTF-8
```
---
```bash
sudo vim wmini/etc/locale.conf
```
#
```python
LANG=en_US.UTF-8
```
---
```bash
sudo umount wmini/boot wmini/home wmini
```
Insert cards into the Wmini ad login with root/root
```bash
locale-gen
pacman-key --init
pacman-key --populate archlinuxarm
pacman -Syu
passwd
useradd -m -G users -s /bin/bash user
passwd user
userdel -r alarm
pacman -S gnome gnome-flashback gnome-keyring gnome-tweaks gnome-applets xf86-video-fbdev xorg-server xorg-xinit gvim network-manager-applet dnsmasq ttf-dejavu ttf-droid xmonad xmonad-contribi dmenu base-devel
systemctl enable NetworkManager.service
reboot
systemctl start gdm.service
```
Login with user/user

```bash
su
systemctl enable gdm.service
pacman -Rs gnome-software gnome-music totem
pacman -S ntfs-3g android-file-transfer
pacman -S chromium vlc libreoffice-fresh gimp git clipgrab
cd Downloads
git clone https://aur.archlinux.org/bluez-utils-compat.git
cd bluez-utils-compat
gpg --recv-keys XXXXXXXXXXXXXXXX
makepkg -sci
git clone https://aur.archlinux.org/pi-bluetooth.git
cd pi-bluetooth
makepkg -sci
exit
```
## Xmonad
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
```
