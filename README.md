# ManjaroKdeInstallRoutine

Step by step description on how to setup Manjaro KDE.

---

IMPORTANT: THERE IS NO GUARANTEE THAT NOTHING GOES WRONG SO BE CAREFUL AND IF YOU ARE UNSURE RESEARCH WHAT A CERTAIN STEP MEANS!

---

## 1. Create a from the motherboard bootable USB stick

1. Download the `.iso` file from the [Official Manjaro Website](https://manjaro.org/downloads/official/kde/)

   For a minimal, non bloated installation and a fast download the *Minimal* version is chosen

2. *Burn*/*Flash* the `.iso` file to a USB stick using for example [(Balena) Etcher](https://www.balena.io/etcher/) which is available for Windows, Linux and macOS

   **Be careful since this formats your USB stick and that you select the correct drive!**

## 2. Now reboot your PC and start the Manjaro on the USB stick

1. You need to check online or look at the initial boot image of your PC to see how to enter the motherboard BIOS so you can in there select a custom boot medium

   For my ASUS motherboard the initial boot image says press `F11` to enter the boot menu where I then get a list of all devices that the motherboard can boot

2. Boot the Manjaro USB stick by selecting its entry in the BIOS (for me it is called *UEFI USB STICK*)
3. After you press enter and boot a new screen comes up where you can configure how the Manjaro on the USB stick should boot

   - If you have an NVIDIA GPU and want to check out if certain features like G-Sync or CUDA work before installing Manjaro select *boot with proprietary drivers* since the OpenSource NVIDIA work good enough but don't have these special features implemented (You can switch later to either driver version though)

## 3. Prepare your installation drive

---

Since in the past I got errors when running the default installer on drives that were not empty I recommend to clear the drive since the error actually can result in you needing to restart the whole process:

- The installer ends with this error:

  ![Manjaro installer error](./screenshots/screenshot_20220505_191205_installer_error.png)

- This error even prevents `gnome-disk-utility` from formatting the drive:

  ![`gnome-disk-utility` format error](./screenshots/screenshot_20220505_191355_gnome_disk_utility_format_error.png)

---

1. Open the terminal and update the package manager database so you can install other programs:

   *If you have problems with your keyboard layout you might wanna go to step 4. and follow the default installer until the step with the keyboard layout (after you set the right layout just close the installer and come back to this point)*

   ```sh
   sudo pacman -Sy
   ```

2. Install `gnome-disk-utility` which is a program to format and partition drives:

   ```sh
   sudo pacman -S gnome-disk-utility
   ```

3. Run `gnome-disks` or search for *Disks*:

   1. Find the drive on which you want to install Manjaro

      ![`gnome-disk-utility`](./screenshots/screenshot_20220505_191236_gnome_disk_utility.png)

   2. Select it and clear all partitions

      ![`gnome-disk-utility` format](./screenshots/screenshot_20220505_191237_gnome_disk_utility_format.png)

      ![`gnome-disk-utility` format popup](./screenshots/screenshot_20220505_191251_gnome_disk_utility_format_popup.png)

      It should now look like this:

      ![`gnome-disk-utility` formatted drive](./screenshots/screenshot_20220505_171959_gnome_disk_utility_formatted_drive.png)

## 4. Follow the steps of the official installer

1. Launch the installer by clicking the button *Launch installer*:

   ![Launch installer](./screenshots/screenshot_20220505_170218_launch_installer.png)

2. Set the language of your installation:

   ![Installer language](./screenshots/screenshot_20220505_170230_installer_language.png)

3. Set the time zone of your installation:

   ![Installer language](./screenshots/screenshot_20220505_190237_installer_timezone.png)

4. Set the keyboard layout of your installation:

   ![Installer keyboard layout](./screenshots/screenshot_20220505_190347_installer_keyboard_layout.png)

5. Specify the partitioning of your installation:

   **Be careful since this formats your USB stick and that you select the correct drive!**

   ![Installer keyboard layout](./screenshots/screenshot_20220505_192134_installer_disk.png)

   - `ext4` is the standard/default
   - Swap to file was chosen since at least on my hardware hibernation is not working so there is no need to do a partition for that one since it can just be a file anyways
   - Encryption was chosen for security, it means that your drive needs to be decrypted before every boot and every access from another PC but it also means that your data can not just be read by anyone without the password

   **Make sure that it says EFI in the left corner of the installer if you want to create an UEFI installation medium (some older motherboards don't like UEFI so to change that boot the USB stick via the non UEFI entry in the BIOS)!**

6. Set the user name and passwords of your installation:

   *Make sure by typing your password into a field that shows text that all special characters in your password are correctly recognized and that you didn't make an error when choosing the keyboard layout!*

   ![Installer users](./screenshots/screenshot_20220505_192205_installer_users.png)

7. In the summary make sure that everything is exactly as you want and that the correct drives will be used:

   ![Installer summary](./screenshots/screenshot_20220505_192211_summary.png)

8. Reboot your PC:

   ![Installer finish](./screenshots/screenshot_20220505_192647_finish.png)

    In case your PC is not booting the new Manjaro drive automatically since the USB stick is still plugged in or you have another drive with a different OS that is in front of it check step 2 on how to enter the BIOS.
    You should be able to edit the boot order of all found drives with bootable operating systems in any BIOS.

## 5. Finalize installation

1. Swap file

   Since the default swap file is somehow not even 1GB here are the commands you need to run to replace it with one of a custom size (this depends on how much you want and how much RAM you have obviously):

   ```sh
   # List the swapfile
   swapon
   # Turn it off
   sudo swapoff /swapfile
   # Remove it
   sudo rm /swapfile
   # Create a new swapfile
   sudo dd if=/dev/zero of=/swapfile bs=1M count=38GB status=progress
   sudo chmod 600 /swapfile
   sudo mkswap /swapfile
   # Enable the new swapfile
   sudo swapon /swapfile
   ```

   ![Setup swap file](./screenshots/screenshot_20220505_214930_replace_swap_file.png)

2. Switch branch

   If you want to get faster (but also more unstable) updates you can switch to a different Manjaro branch:

   ```sh
   # Replace testing with the branch you want to switch to
   sudo pacman-mirrors --api --set-branch testing
   ```

   ![Switch branch](./screenshots/screenshot_20220505_193753_switch_branch.png)

   Then update all packages and find the fastest mirrors:

   ```sh
   sudo pacman-mirrors --fasttrack 5 && sudo pacman -Syyu
   ```

   ![Update packages](./screenshots/screenshot_20220505_193934_update_packages.png)

3. Update package manager database and update all packages (unnecessary if 2. was executed):

   ```sh
   sudo pacman -Syu
   ```

4. Switch kernel

   If you want to be on the latest stable kernel or simply another kernel you can switch it and then reboot afterwards into it:

   *Open the 'manjaro settings manager'*

   ![manjaro settings manager kernel](./screenshots/screenshot_20220505_195034_manjaro_settings_manager_kernel.png)

   *Click 'install' to select which kernel to install (the latest installed kernel will be used as a default on a normal start, older kernels are always available as backups in case something breaks with the other kernel)*

   ![manjaro settings manager kernel select](./screenshots/screenshot_20220505_195052_manjaro_settings_manager_kernel_select.png)

## 6. Enable AUR/3rd-party package support

Since the default repositories don't include all OpenSource programs in the world and especially not proprietary programs AUR support makes access to these kinds of programs/packages much easier:

```sh
sudo pacman -S yay
```

`yay` is able to read the AUR database and thus can be used to install for example `spotify`, `google-chrome` or games like `osu!lazer`:

```sh
# Example command to install a package from the AUR
yay -S spotify
```

Since I had problems installing certain AUR packages and other programs there is a recommended list of programs that you should probably just install so you have less problems down the line when they are missing on certain dependency lists:

```sh
# General build programs
sudo pacman -S make pkgconf patch cmake gcc clang
# General helpful CLI programs
sudo pacman -S vim grep
```

To enable flatpaks in e.g. `pamac` [follow the official Manjaro instructions](https://wiki.manjaro.org/index.php/Flatpak) (and logout, login again for changes to be applied sometimes):

```sh
pamac install flatpak libpamac-flatpak-plugin
```

## 7. Configure KDE

---

*Open your system settings for configuring KDE (from 2. onwards)*

---

1. Disable autostart:

   ![KDE disable autostart](./screenshots/screenshot_20220505_193403_kde_disable_autostart.png)

2. Setup your display(s) since the frequency is probably wrong:

   ![KDE display setup](./screenshots/screenshot_20220505_195555_kde_display_setup.png)

3. Setup your mouse to disable acceleration or other things:

   ![KDE mouse setup](./screenshots/screenshot_20220505_195614_kde_mouse_setup.png)

4. Setup your desktop wallpaper:

   *You get to this by doing a right click on an empty space on the Desktop and selecting `configure desktop and wallpaper`*

   ![KDE mouse setup](./screenshots/screenshot_20220505_200113_kde_wallpaper.png)

5. Setup your login screen:

   ![KDE mouse setup](./screenshots/screenshot_20220505_215759_kde_login_screen.png)

6. Setup your lock screen:

   ![KDE mouse setup](./screenshots/screenshot_20220505_200436_kde_lock_screen.png)

7. Connect (Google) calendar

   It is possible to sync other calendar services like Google calendar with KDE by downloading `korganizer` and additionally installing `kdepim-addons`.

   In `korganizer` you can connect to the calendar service of your choice (and configure how often it should synchronize) and in the calendar widget panel settings you can now activate **PMI Events Plugin** which gives you a new subsection in this window where you can select connected calendars to make them show up in the widget.

8. Connect a smartphone

   It is possible to connect a smartphone and some of its functionality (like storage and notification access) out of the box.

   To do this you need to download the app [KDE Connect](https://kdeconnect.kde.org/) for your smartphone and then pair it with your KDE installation by starting the preinstalled *KDE Connect* program.
   Now you can on your smartphone add notification, storage access and more which in turn makes it able to for example access your phone storage in the default file manager and the notifications next to the other notifications.

   You can even configure it to share the clipboard contents (you copy text on your desktop and can paste it on your phone or do it in the opposite direction).

9. Connect a printer

   It is possible to easily connect printers to KDE and manage them in the system settings after installing the `manjaro-printer` package group.

   ```sh
   sudo pacman -S manjaro-printer
   ```

   To enable the print service you can either make it run only temporarily or even permanently enable the service after the next reboot.

   ```sh
   # Optionally enable permanent the print service
   sudo systemctl enable cups.service
   # Start the print service
   # (works only temporarily without the previous command)
   sudo systemctl start cups.service
   ```

   After this step you can open the system settings and go to the *Printers* section.
   For example your network printer should automatically be listed after clicking *Add Printer* and drivers are also either selected automatically or you need to select one manually out of a list of available drivers (the printer name should match the series and driverless often has less options than other options).

   After installing the driver other programs should be able to find the connected printers.

10. Configure Virtual Desktops

   In the system settings under the section *Virtual Desktops* you can configure the layout (rows/columns), number and switch animation of desktops.

   You can also set custom keyboard shortcuts for easy switching between the desktops.
   For that you search in the system settings for *Shortcuts* and then go to the section *KWin*.

   When you for example set up 1 row with 3 columns and you want some keybindings to go easily to the next/previous desktop you can set:

   - *Switch to next Desktop*: `Ctrl` + `Alt` + `Left Arrow`
   - *Switch to previous Desktop*: `Ctrl` + `Alt` + `Right Arrow`

## 8. Install programs

### Printing

```sh
# Scan pages to images/PDF files
sudo pacman -S gscan2pdf
```

### General runtimes

```sh
# JAVA
sudo pacman -S jre-openjdk-headless jre-openjdk jdk-openjdk
# Python
sudo pacman -S python
# NodeJs
sudo pacman -S nodejs
```

### Tune CPU

```sh
# You can configure the CPU to not run in a powersave
# mode but in a performance mode
yay -S corectrl
```

This will result in a much higher power usage when doing non intensive tasks so only do it if you actually see a difference in performance or only enable it while you need high performance.

### Microphone noise cancelling

```sh
# You can create a new virtual microphone based on an
# existing microphone but with noise cancelling
yay -S noisetorch
```

### Camera controls

```sh
# You can configure camera zoom, focus etc. with a desktop UI that also has a preview window
# This for example is able to change the camera zoom while being in a Zoom video call
sudo pacman -S cameractrls
```

### Gaming

```sh
# Steam (Don't forget to go into the settings and
#        enable 'Steam Play' for all titles)
# > You can copy downloaded Steam games from another
#   drive onto your new drive without any download
sudo pacman -S steam
# Lutris
# > Install wine for the latest wine(-system) version
sudo pacman -S lutris
# Gaming stats
sudo pacman -S mangohud gamemode
yay -S goverlay
```

### Cloud Storage

```sh
# OneDrive
# > run 'onedrive --synchronize --download-only' for
#   an initial download of all data to '~/OneDrive'
# > run 'onedrive --monitor' to sync changes in both
#   directions
yay -S onedrive-abraunegg
```

## Wayland

> [!Tip]
> Nowadays it should just work out of the box.
> Just don't forget to change the session on the bottom left.
> Wayland does not only offer more performance but can offer additional functionality like touch display gestures and pen/tablet support depending on your hardware.

To enable Wayland check [this article](https://community.kde.org/Plasma/Wayland/Nvidia):

- On NVIDIA GPUs the kernel parameter `nvidia_drm.modeset=1` needs to be added when the Wayland session does not work out of the box (and `sudo cat /sys/module/nvidia_drm/parameters/modeset` returns `N`)
  - To do this edit the grub file (`/etc/default/grub`) and update the line `GRUB_CMDLINE_LINUX="nvidia_drm.modeset=1"`, then run `sudo update-grub`
- On the login screen select `Wayland session`
- To capture video with `obs` install the necessary pipewire packages (`pacman -S pipewire-alsa pipewire-audio pipewire-docs pipewire-jack pipewire-media-session pipewire-pulse`) and from the Qt wayland plugins for wayland (`pacman -S qt5-wayland`, `yay -S qt6-wayland`)

> If there are still problems with the Wayland session try adding `nvidia-drm.fbdev=1` (`GRUB_CMDLINE_LINUX="nvidia_drm.modeset=1 nvidia-drm.fbdev=1"`) [Manjaro Forum Reply](https://forum.manjaro.org/t/testing-update-2024-05-14-linux-firmware-mkinitcpio-php-plymouth/161487/7)
> Additionally you may need to edit (`sudo vim `) `/etc/mkinitcpio.conf` and update the line `HOOKS(...)` or `MODULES(...)` with additional entries (e.g. `MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)`) and then run `sudo mkinitcpio -P`.

> Instead of adding these lines directly to the `/etc/defaults/grub` file rather create a file with the NVIDIA specific changes like `/etc/modprobe.d/nvidia.conf`:
>
> ```sh
> options nvidia_drm modeset=1 fbdev=1
> ```
>
> This also means you do not need to update `grub`.

## VPNs

### (KDE) OpenVPN (`.ovpn`)

If the connection always fails (without error message) enter `journalctl -f` and then check the output after trying to connect once more.
If this shows errors that try to invoke a `tun` module enter `modinfo tun` in the terminal and check if `modinfo: ERROR: Module tun not found` is the output.
To make sure it's available you need to enable it:

```sh
# [OPTIONAL] Verify that the module is in your kernel:
$ ls /lib/modules/$(uname -r)/kernel/drivers/net
$ ls /dev/net/tun
# Enable available tun kernel module
$ sudo modprobe tun
# [OPTIONAL] Check if it's loaded
$ modinfo tun
$ lsmod | grep tun
```

If this doesn't work try to install the fitting Linux headers and reboot once:

```sh
# Get current kernel version and install the headers
$ uname -r
6.14.3-1-MANJARO
$ sudo pacman -S linux614-headers
# REBOOT
$ sudo reboot
```

It's also possible to permanently load the module on every start by adding it to the module list:

```sh
echo tun | sudo tee -a /etc/modules-load.d/tun.conf
```

## Services

### `systemd`

Sometimes programs (e.g. Docker) are managed by a `systemd` integration meaning they for example auto start on login or when they crash.

```sh
# Enable autostart at boot
sudo systemctl enable docker
# Disable autostart at boot
sudo systemctl disable docker
# Start the service
sudo systemctl start docker
# Stop the service
sudo systemctl stop docker
# Restart the service
sudo systemctl restart docker
# Check current status
sudo systemctl status docker
# Check if enabled on boot
sudo systemctl is-enabled docker
```

### `dbus`

Sometimes programs (e.g. Nextcloud) are managed by a `dbus` integration instead of `systemd` meaning they for example auto restart after you close them.
To stop this you need to do the following:

```sh
sudo mv /usr/share/dbus-1/services/com.nextcloudgmbh.Nextcloud.service /usr/share/dbus-1/services/com.nextcloudgmbh.Nextcloud.service.disabled
# Then close/kill the program and it won't automatically restart
```

## More

- To get a "boot" screen where you can switch kernels or do more advanced stuff before actually launching Manjaro/*the Linux kernel* you need to hold the `SHIFT` key after the BIOS prompt
  - This can be helpful if for example an external hard drive is not detected any more but was detected on a previous Linux kernel

## Encryption

A LUKS encrypted drive Linux drive stores the actual filesystem inside an **encrypted LUKS container**:

- Boot:
  - GRUB bootloader stage (if `/boot` is encrypted):
    - The GRUB bootloader is the software that loads the Linux kernel
    - GRUB needs to unlock the **encrypted LUKS container** holding the kernel and initramfs before it can load them
  - Initramfs stage
    - The initramfs (early boot environment) is used as the first root filesystem and then used to mount the real filesystem with all the data
    - The unlocks the root filesystem using your passphrase or a keyfile
    - On Manjaro, `/crypto_keyfile.bin` is embedded in the initramfs and stored inside the encrypted root, allowing it to unlock other drives automatically
    - This keyfile is added to the LUKS keyslots of any additional drives you want to unlock without retyping your password
  - Systemd stage
    - `/etc/crypttab`: what to unlock
    - `/etc/fstab`: what to mount

### Add an additional drive to automatically decrypt and mount on start

1. Identify the new encrypted drive
   - Run:

     ```sh
     sudo blkid
     # The following is the output for a 3 different nvme drives:
     # /dev/nvme0n1 → partitions like nvme0n1p1 (New drive [encrypted])
     # /dev/nvme1n1 → partitions like nvme1n1p1, nvme1n1p2, nvme1n1p3, nvme1n1p4 (Manjaro drive [encrypted])
     # /dev/nvme2n1 → partitions like nvme2n1p1, nvme2n1p2 (Windows 11 drive)
     ```

     This returns `/dev/<device>: KEY="value" KEY="value" ...`:

     - `/dev/<device>`: device name
     - Common `KEY`s:
       - `UUID`: filesystem or partition unique identifier
       - `UUID_SUB`: for multi-device filesystems like Btrfs
       - `BLOCK_SIZE`: filesystem block size (smallest unit of data storage)
       - `TYPE`: filesystem type (e.g. `ext4`, `vfat`, `ntfs`, `crypto_LUKS`).
       - `PARTUUID`: partition UUID from the partition table
       - `PARTLABEL`: human-readable partition label

     ```text
     /dev/mapper/luks-fdafce73-7820-4b7c-951b-a5118221fd82: UUID="4f354c10-a98c-4ab3-8c9b-24fc47f4f32a" UUID_SUB="997b0476-6b57-4e2d-ab11-782f0b43f158" BLOCK_SIZE="4096" TYPE="btrfs"
     /dev/nvme0n1p1: UUID="fdafce73-7820-4b7c-951b-a5118221fd82" TYPE="crypto_LUKS" PARTUUID="6fd3a22d-d038-4a34-97a3-b48a022bf759"
     /dev/nvme2n1p2: UUID="2b42ed68-695e-40de-89a9-5529c6443177" TYPE="crypto_LUKS" PARTLABEL="root" PARTUUID="4f28b7b0-dffd-cf4d-aba2-5ace144cd80a"
     /dev/nvme2n1p1: UUID="794A-9731" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="3c72ba79-1956-cf44-9f6f-6a3db6c34a57"
     /dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177: UUID="28ded8b9-5fef-4e19-a03d-d0a72520a007" BLOCK_SIZE="4096" TYPE="ext4"
     /dev/nvme1n1p4: BLOCK_SIZE="512" UUID="F82258162257D7E8" TYPE="ntfs" PARTUUID="1842f274-e3aa-4ed9-b087-c8be347247fa"
     /dev/nvme1n1p2: PARTLABEL="Microsoft reserved partition" PARTUUID="86fdde4d-d705-4e25-99a0-387e273cb8d3"
     /dev/nvme1n1p3: BLOCK_SIZE="512" UUID="6E46888C4688572D" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="98d7eafa-9f89-4aae-834a-3395b0522581"
     /dev/nvme1n1p1: UUID="BE87-AD66" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI system partition" PARTUUID="a5e02ca4-2859-4145-9766-478f9e8d07e8"
     ```

     In this case we see the line `/dev/nvme0n1p1: UUID="fdafce73-7820-4b7c-951b-a5118221fd82" TYPE="crypto_LUKS"` which tells us that the drive `/dev/nvme0n1p1` is encrypted using `LUKS`.

     If we now search for the `UUID` we see that this **encrypted LUKS container** contains the mounted filesystem `/dev/mapper/luks-fdafce73-7820-4b7c-951b-a5118221fd82: UUID="4f354c10-a98c-4ab3-8c9b-24fc47f4f32a" TYPE="btrfs"` which is of the type `btrfs`.

2. Tell Manjaro/`systemd` how to unlock the drive

   - If you look at the file `/etc/crypttab`:

     ```text
     # ...
     # <name>               <device>                         <password> <options>
     luks-2b42ed68-695e-40de-89a9-5529c6443177 UUID=2b42ed68-695e-40de-89a9-5529c6443177     /crypto_keyfile.bin luks
     ```

     You can see that the file `/crypto_keyfile.bin` is used to store the decryption passwords.

   - With the following command you can add the encrpytion password to the new drive (`/dev/nvme0n1p1`) to it:

     ```sh
     # Asks you first for the current passphrase, then the device passphrase
     sudo cryptsetup luksAddKey /dev/nvme0n1p1 /crypto_keyfile.bin
     ```

3. Tell Manjaro/`systemd` how to mount the drive

   - Now that the drive is unlocked you still need to mount it somewhere
   - Create new mountpoint:

     ```sh
     # Create the mountpoint 'data_drive_2025'
     sudo mkdir -p /mnt/data_drive_2025
     ```

   - Add the mountpoint to `/etc/fstab`:

     ```sh
     sudo vim /etc/fstab
     ```

     To the existing file:


     ```text
     # /etc/fstab: static file system information.
     #
     # Use 'blkid' to print the universally unique identifier for a device; this may
     # be used with UUID= as a more robust way to name devices that works even if
     # disks are added and removed. See fstab(5).
     #
     # <file system>             <mount point>  <type>  <options>  <dump>  <pass>
     UUID=794A-9731                            /boot/efi      vfat    umask=0077 0 2
     /dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 /              ext4    defaults,noatime 0 1
     /swapfile                                 swap           swap    defaults,noatime 0 0
     tmpfs                                     /tmp           tmpfs   defaults,noatime,mode=1777 0 0
     ```
    
     Add the line:
    
     ```text
     # <file system>             <mount point>  <type>  <options>  <dump>  <pass>
     # currently decrypted mounted filesystem UUID
     #                           the created mounting point
     #                                          mounted filesystem type
     #                                                  default options
     #                                                             default (0), don't backup filesystem
     #                                                                      default (0), don't run filesystem check
     UUID=4f354c10-a98c-4ab3-8c9b-24fc47f4f32a  /mnt/data_drive_2025  btrfs  defaults  0  0
     ```

Now when restarting your PC it will automatically decrypt the encrypted drive and mount the read filesystem at `/mnt/data_drive_2025`.
If your drive is not encrypted you can just follow step 3.

## Display Scaling

### Login Screen (SDDM) Scaling

Assuming you have a 4K display connected it can happen that your login screen looks tiny since it isn't scaled (like 200% on your actual plasma session).

1. Solution: Ugly but screen specific

   - Assuming you have the following displays connected identify your screen name (e.g. `DP-2`):

     ```sh
     xrandr | grep " connected"
     HDMI-A-1 connected 1080x1920+2560+0 right (normal left inverted right x axis y axis) 531mm x 299mm
     DP-2 connected primary 2560x1440+0+328 (normal left inverted right x axis y axis) 544mm x 303mm
     ```

   - and then specify the scale:

    ```sh
    sudo vim /usr/share/sddm/scripts/Xsetup
    ```

    ```sh
    #!/bin/sh
    # Xsetup - run as root before the login dialog appears
    # Scale everything twice as big
    xrandr --output DP-2 --scale 0.5x0.5
    ```

    This will look rather blurry and not sharp since the image is scaled after the rendering and not during the rendering!

2. Solution: Sharp but not screen specific

   - By creating a new SDDM configuration with the following content ([source: kalzEOS](https://www.reddit.com/r/kde/comments/1bahvog/fix_sddm_on_hidpi_screens_on_plasma_6/)):

     ```sh
     sudo vim /etc/sddm.conf.d/hidpi.conf
     ```

     ```text
     [Wayland]
     EnableHiDPI=true
     
     [X11]
     EnableHiDPI=true
     
     [General]
     GreeterEnvironment=QT_SCREEN_SCALE_FACTORS=2,QT_FONT_DPI=192
     ```

     The login screen will now be scaled times 2 during the rendering, but this will happen for all screens so if you have different resolutions this may look bad on lower resolution screens.

## Swap File

Using a swap file the system memory can be stored on your disk so you can completely shut down your PC (*hibernate*) and can then on the next normal start load it to be right back where you left.

> [!Warning]
>
> Depending on your hardware this can either work, work in parts or work not at all.
> On 2 test systems it worked perfectly fine on one (i5 6500 and GTX 1650) while only working in parts on the other (7800X3D and RTX 3060TI).

1. Create swap file (https://wiki.manjaro.org/index.php/Swap)

   Your swap file should have a certain size depending on your RAM size accoring to the Manjaro Wiki:

   ```text
          RAM   No hibernation    With Hibernation  Maximum
       8GB              3GB                11GB     16GB
      16GB              4GB                20GB     32GB
      32GB              6GB                38GB     64GB
      64GB              8GB                72GB    128GB
   ```

   > You should also calculate the video card memory into the size of your swap file ([i.e. at least 5 percent more than the sum of the memory capacities of all NVIDIA GPUs](https://wiki.archlinux.org/title/NVIDIA/Tips_and_tricks)):
   >
   > ```sh
   > nvidia-smi --query-gpu=memory.total --format=csv,noheader,nounits
   > ```

   If you have a swapfile that is too small you can remove it using:

   ```sh
   sudo swapoff /swapfile
   sudo rm /swapfile
   ```

   Create a swapfile and make it only readable and writable by root:

   ```sh
   # For 32GB of RAM 38GB was chosen in this instance
   sudo dd if=/dev/zero of=/swapfile bs=1M count=38912 status=progress
   sudo chmod 600 /swapfile
   ```

   Format and enable the swapfile:

   ```sh
   sudo mkswap /swapfile
   sudo swapon /swapfile
   # Setting up swapspace version 1, size = 38 GiB (40802185216 bytes)
   # no label, UUID=906f88f0-1edc-4b44-ab7f-089d6e522696
   ```

   Verify that the swap file was enabled correctly:

   ```sh
   swapon
   # NAME      TYPE SIZE   USED PRIO
   # /swapfile file  38G 601,7M   -2
   ```

2. Ensure that the swapfile is available at boot

   Add entry to `/etc/fstab` (`/swapfile none swap defaults 0 0`):

   ```sh
   sudo nano /etc/fstab
   ```

   Example of the updated file:

   ```text
   # /etc/fstab: static file system information.
   #
   # Use 'blkid' to print the universally unique identifier for a device; this may
   # be used with UUID= as a more robust way to name devices that works even if
   # disks are added and removed. See fstab(5).
   #
   # <file system>             <mount point>  <type>  <options>  <dump>  <pass>
   UUID=794A-9731                            /boot/efi      vfat    umask=0077 0 2
   /dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 /              ext4    defaults,noatime 0 1
   tmpfs                                     /tmp           tmpfs   defaults,noatime,mode=1777 0 0
   UUID=4f354c10-a98c-4ab3-8c9b-24fc47f4f32a  /mnt/data_drive_2025  btrfs  defaults  0  0
   /swapfile                                 none           swap    defaults 0 0
   ```

   > [!Warning]
   > Apparently this is not always this straight forward if you are on other filesystems like *btrfs* so make sure to check the Manjaro wiki in that case!

3. Add `resume` parameter to `grub` bootloader (**Not neccessarily needed, the system can sometimes auto detect a previous hibernation!**)

   Get the **UUID** of the swapfile:

   ```sh
   sudo blkid
   # /dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177: UUID="28ded8b9-5fef-4e19-a03d-d0a72520a007" BLOCK_SIZE="4096" TYPE="ext4"
   # /dev/nvme1n1p2: UUID="2b42ed68-695e-40de-89a9-5529c6443177" TYPE="crypto_LUKS" PARTLABEL="root" PARTUUID="4f28b7b0-dffd-cf4d-aba2-5ace144cd80a"
   # /dev/nvme1n1p1: UUID="794A-9731" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="3c72ba79-1956-cf44-9f6f-6a3db6c34a5
   ```

   In this case we see that the unlocked LUKS encrypted drive has the **UUID** `28ded8b9-5fef-4e19-a03d-d0a72520a007`.

   Now we need to find out the offset:

   ```sh
   sudo filefrag -v /swapfile | awk '{if($1=="0:"){print $4}}' | sed 's/\.\.//'
   # 153231360
   ```

   Using this information we can now edit the bootloader to point to the unlocked drive and the swapfile (using the offset):

   ```sh
   sudo nano /etc/default/grub
   ```

   Add to the line `GRUB_CMDLINE_LINUX_DEFAULT` at the end `resume=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 resume_offset=153231360`.

   Update grub:

   ```sh
   sudo update-grub
   ```

4. Make sure that initramfs includes resume support

   ```sh
   sudo nano /etc/mkinitcpio.conf
   ```

   Find the `HOOKS=` line and make sure it includes `resume` after `encrypt`:

   ```text
   HOOKS=(base udev autodetect modconf kms block keyboard keymap consolefont plymouth encrypt resume filesystems fsck)
   ```

   If `resume` was missing regenerate initramfs:

   ```sh
   sudo mkinitcpio -P
   ```

5. Restart your whole PC so the changes come into effect

   On errors check for the `dmesg` logs:

   ```sh
   sudo dmesg | grep -iE 'hibernat|resume|swap|error'
   ```

   ```text
   # sudo dmesg | grep -i resume (previous output of a failed hibernation)
   [    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.16-x86_64 root=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 rw nvidia_drm.modeset=1 nvidia-drm.fbdev=1 quiet cryptdevice=UUID=2b42ed68-695e-40de-89a9-5529c6443177:luks-2b42ed68-695e-40de-89a9-5529c6443177 root=/dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 splash udev.log_priority=3 resume=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 resume_offset=153231360
   [    0.028512] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.16-x86_64 root=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 rw nvidia_drm.modeset=1 nvidia-drm.fbdev=1 quiet cryptdevice=UUID=2b42ed68-695e-40de-89a9-5529c6443177:luks-2b42ed68-695e-40de-89a9-5529c6443177 root=/dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 splash udev.log_priority=3 resume=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 resume_offset=153231360
   [    8.311035] PM: hibernation: resume from hibernation
   [   12.840397] PM: hibernation: resume failed (-5)
   ```

   ```text
   [    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.16-x86_64 root=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 rw nvidia_drm.modeset=1 nvidia-drm.fbdev=1 quiet cryptdevice=UUID=2b42ed68-695e-40de-89a9-5529c6443177:luks-2b42ed68-695e-40de-89a9-5529c6443177 root=/dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 splash udev.log_priority=3 resume=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 resume_offset=153231360
   [    0.028143] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.16-x86_64 root=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 rw nvidia_drm.modeset=1 nvidia-drm.fbdev=1 quiet cryptdevice=UUID=2b42ed68-695e-40de-89a9-5529c6443177:luks-2b42ed68-695e-40de-89a9-5529c6443177 root=/dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 splash udev.log_priority=3 resume=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 resume_offset=153231360
   [    0.082009] Spectre V1 : Mitigation: usercopy/swapgs barriers and __user pointer sanitization
   [    0.839598] zswap: loaded using pool zstd/zsmalloc
   [    0.842001] RAS: Correctable Errors collector initialized.
   [    8.324123] PM: hibernation: resume from hibernation
   [   12.026776] nvidia 0000:01:00.0: PM: failed to quiesce async: error -5
   [   12.482600] PM: hibernation: resume failed (-5)
   [   14.007605] systemd[1]: Clear Stale Hibernate Storage Info was skipped because of an unmet condition check (ConditionPathExists=/sys/firmware/efi/efivars/HibernateLocation-8cf2644b-4b0b-428f-9387-6d876050dc67).
   [   14.028707] systemd[1]: Activating swap /swapfile...
   [   14.046534] Adding 39845884k swap on /swapfile.  Priority:-2 extents:2320 across:974151680k SS
   [   14.046559] systemd[1]: Activated swap /swapfile.
   [   14.046583] systemd[1]: Reached target Swaps.
   [   32.673457] ibus-x11[1593]: segfault at 1 ip 00007f5b9d323ac9 sp 00007ffd43dc89a8 error 4 in libgobject-2.0.so.0.8400.3[39ac9,7f5b9d2f6000+37000] likely on CPU 14 (core 6, socket 0)
   ```

   [**NVIDIA workarounds**](https://wiki.archlinux.org/title/NVIDIA/Tips_and_tricks): (all of them didn't work on one system, this is just here for reference)

   ```sh
   # Should already be enabled per default
   sudo systemctl enable nvidia-hibernate.service
   sudo systemctl enable nvidia-suspend.service
   sudo systemctl enable nvidia-resume.service
   ```

   `/etc/modprobe.d/nvidia.conf`:

   ```text
   # Should already be enabled per default, still experimental
   options nvidia NVreg_PreserveVideoMemoryAllocations=1
   options nvidia NVreg_TemporaryFilePath=/var/tmp
   ```

6. Fix issues with in part working hibernations:

   ```sh
   sudo dmesg | grep -iE 'hibernat|resume|swap|error|nvidia'
   ```

   ```text
   $ sudo dmesg | grep -iE 'hibernat|resume|swap|error|nvidia'
   [    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.16-x86_64 root=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 rw quiet cryptdevice=UUID=2b42ed68-695e-40de-89a9-5529c6443177:luks-2b42ed68-695e-40de-89a9-5529c6443177 root=/dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 splash udev.log_priority=3 resume=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 resume_offset=20809728
   [    0.024704] PM: hibernation: Registered nosave memory: [mem 0x00000000-0x00000fff]
   [    0.024705] PM: hibernation: Registered nosave memory: [mem 0x000a0000-0x000fffff]
   [    0.024706] PM: hibernation: Registered nosave memory: [mem 0x09e02000-0x09ffffff]
   [    0.024706] PM: hibernation: Registered nosave memory: [mem 0x0a200000-0x0a20ffff]
   [    0.024707] PM: hibernation: Registered nosave memory: [mem 0x0b000000-0x0b020fff]
   [    0.024708] PM: hibernation: Registered nosave memory: [mem 0x81a46000-0x81aa2fff]
   [    0.024708] PM: hibernation: Registered nosave memory: [mem 0x82bcc000-0x82bccfff]
   [    0.024709] PM: hibernation: Registered nosave memory: [mem 0x8738f000-0x985fefff]
   [    0.024710] PM: hibernation: Registered nosave memory: [mem 0x99ff9000-0x99ffbfff]
   [    0.024710] PM: hibernation: Registered nosave memory: [mem 0x9a000000-0xffffffff]
   [    0.028219] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.16-x86_64 root=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 rw quiet cryptdevice=UUID=2b42ed68-695e-40de-89a9-5529c6443177:luks-2b42ed68-695e-40de-89a9-5529c6443177 root=/dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 splash udev.log_priority=3 resume=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 resume_offset=20809728
   [    0.081970] Spectre V1 : Mitigation: usercopy/swapgs barriers and __user pointer sanitization
   [    0.792811] zswap: loaded using pool zstd/zsmalloc
   [    0.795241] RAS: Correctable Errors collector initialized.
   [    3.115538] nvidia: loading out-of-tree module taints kernel.
   [    3.115544] nvidia: module license 'NVIDIA' taints kernel.
   [    3.115548] nvidia: module verification failed: signature and/or required key missing - tainting kernel
   [    3.115549] nvidia: module license taints kernel.
   [    3.512704] nvidia-nvlink: Nvlink Core is being initialized, major device number 240
   [    3.515236] nvidia 0000:01:00.0: vgaarb: VGA decodes changed: olddecodes=io+mem,decodes=none:owns=none
   [    3.559404] NVRM: loading NVIDIA UNIX x86_64 Kernel Module  575.64.05  Fri Jul 18 16:01:21 UTC 2025
   [    3.583414] nvidia-modeset: Loading NVIDIA Kernel Mode Setting Driver for UNIX platforms  575.64.05  Fri Jul 18 15:45:08 UTC 2025
   [    3.589361] nvidia_uvm: module uses symbols nvUvmInterfaceDisableAccessCntr from proprietary module nvidia, inheriting taint.
   [    3.685576] [drm] [nvidia-drm] [GPU ID 0x00000100] Loading driver
   [    5.432757] [drm] Initialized nvidia-drm 0.0.0 for 0000:01:00.0 on minor 1
   [    5.457539] nvidia 0000:01:00.0: vgaarb: deactivate vga console
   [    5.565541] fbcon: nvidia-drmdrmfb (fb0) is primary device
   [    5.565544] nvidia 0000:01:00.0: [drm] fb0: nvidia-drmdrmfb frame buffer device
   [    7.391701] systemd[1]: Clear Stale Hibernate Storage Info was skipped because of an unmet condition check (ConditionPathExists=/sys/firmware/efi/efivars/HibernateLocation-8cf2644b-4b0b-428f-9387-6d876050dc67).
   [    7.413905] systemd[1]: Activating swap /swapfile...
   [    7.434153] Adding 67108860k swap on /swapfile.  Priority:-2 extents:2862 across:971309056k SS
   [    7.434176] systemd[1]: Activated swap /swapfile.
   [    7.434210] systemd[1]: Reached target Swaps.
   [    7.844299] input: HDA NVidia HDMI/DP,pcm=3 as /devices/pci0000:00/0000:00:01.1/0000:01:00.1/sound/card1/input23
   [    7.844361] input: HDA NVidia HDMI/DP,pcm=7 as /devices/pci0000:00/0000:00:01.1/0000:01:00.1/sound/card1/input24
   [    7.844416] input: HDA NVidia HDMI/DP,pcm=8 as /devices/pci0000:00/0000:00:01.1/0000:01:00.1/sound/card1/input25
   [    7.844455] input: HDA NVidia HDMI/DP,pcm=9 as /devices/pci0000:00/0000:00:01.1/0000:01:00.1/sound/card1/input26
   [   39.222202] PM: hibernation: hibernation entry
   [   39.335214] PM: hibernation: Marking nosave pages: [mem 0x00000000-0x00000fff]
   [   39.335217] PM: hibernation: Marking nosave pages: [mem 0x000a0000-0x000fffff]
   [   39.335219] PM: hibernation: Marking nosave pages: [mem 0x09e02000-0x09ffffff]
   [   39.335224] PM: hibernation: Marking nosave pages: [mem 0x0a200000-0x0a20ffff]
   [   39.335225] PM: hibernation: Marking nosave pages: [mem 0x0b000000-0x0b020fff]
   [   39.335227] PM: hibernation: Marking nosave pages: [mem 0x81a46000-0x81aa2fff]
   [   39.335229] PM: hibernation: Marking nosave pages: [mem 0x82bcc000-0x82bccfff]
   [   39.335230] PM: hibernation: Marking nosave pages: [mem 0x8738f000-0x985fefff]
   [   39.335266] PM: hibernation: Marking nosave pages: [mem 0x99ff9000-0x99ffbfff]
   [   39.335267] PM: hibernation: Marking nosave pages: [mem 0x9a000000-0xffffffff]
   [   39.335456] PM: hibernation: Basic memory bitmaps created
   [   39.338552] PM: hibernation: Preallocating image memory
   [   40.607541] PM: hibernation: Allocated 1985001 pages for snapshot
   [   40.607547] PM: hibernation: Allocated 7940004 kbytes in 1.26 seconds (6301.59 MB/s)
   [   41.371667] PM: hibernation: Creating image:
   [   42.078790] PM: hibernation: Need to copy 1795628 pages
   [   42.078794] PM: hibernation: Normal pages needed: 1795628 + 1024, available pages: 6487644
   [   41.373801] ACPI: PM: Hardware changed while hibernated, success doubtful!
   [   53.716339] PM: hibernation: Basic memory bitmaps freed
   [   53.718847] PM: hibernation: hibernation exit
   [   54.661257] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 11 Error
   [   54.661260] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 18 Error
   [   60.055681] NVRM: Xid (PCI:0000:01:00): 13, pid=1615, name=plasmashell, Graphics Exception: Shader Program Header 11 Error
   [   60.055710] NVRM: Xid (PCI:0000:01:00): 13, pid=1615, name=plasmashell, Graphics Exception: Shader Program Header 18 Error
   [   65.108850] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 11 Error
   [   65.108929] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 18 Error
   [   65.491952] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 11 Error
   [   65.492030] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 18 Error
   ```

   - Sometimes `plasmashell`/`kwin` can die or break in parts after the hibernation which does not mean that the session is lost.

     ```text
     [   73.334184] PM: hibernation: hibernation exit
     [   74.269496] NVRM: Xid (PCI:0000:01:00): 13, pid=1602, name=plasmashell, Graphics Exception: Shader Program Header 11 Error
     [   74.269522] NVRM: Xid (PCI:0000:01:00): 13, pid=1602, name=plasmashell, Graphics Exception: Shader Program Header 18 Error
     [   74.284077] NVRM: Xid (PCI:0000:01:00): 13, pid=1421, name=kwin_wayland, Graphics Exception: Shader Program Header 11 Error
     [   74.284105] NVRM: Xid (PCI:0000:01:00): 13, pid=1421, name=kwin_wayland, Graphics Exception: Shader Program Header 18 Error
     [   74.308891] NVRM: Xid (PCI:0000:01:00): 13, pid=2373, name=github-desktop, Graphics Exception: Shader Program Header 18 Error
     [   74.758631] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 11 Error
     [   74.758637] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 18 Error
     [   82.776894] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 11 Error
     [   82.776901] NVRM: Xid (PCI:0000:01:00): 13, Graphics Exception: Shader Program Header 18 Error
     ```

     You can try restarting `plasmashell` by opening a terminal and entering the following command:

     ```sh
     plasmashell --replace &
     # kf.dbusaddons: Failed to register name 'org.kde.plasmashell' with DBUS - does this process have permission to use the name, and do no other processes own it already?

     # [1]  + exit 1     plasmashell --replace
     ```

     If you get the displayed output wait a maximum of around 30s and try again entering the same command.

   - Electron applications becoming black windows: No solution yet

### Verify that the NVIDIA GPU is preventing a successful hibernation

In case you are wondering if the hibernation is only failing because of an NVIDIA graphics card error you can try switching to the integrated graphics card temporarily if you have one (following tutorial was done on a 7800X3D):

1. Delete the file `/etc/X11/xorg.conf.d/90-mhwd.conf` (is not needed even when pluigging it in again later, just move it `/etc/X11/xorg.conf.d/90-mhwd.conf.backup` to have a backup)
2. (Turn your PC off) Disconnect the power cable from NVIDIA GPU, plug in your display cables into the motherboard instead of the GPU
3. In the BIOS:
   1. Enable the IGPU (if it wasn't enabled already)
   2. Set the IG adapter to IGD (was PEG) before that [EDIT: I also].

```sh
sudo dmesg | grep -iE 'hibernat|resume|swap|error'
```

```text
[    0.027825] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.16-x86_64 root=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 rw nvidia_drm.modeset=1 nvidia-drm.fbdev=1 quiet cryptdevice=UUID=2b42ed68-695e-40de-89a9-5529c6443177:luks-2b42ed68-695e-40de-89a9-5529c6443177 root=/dev/mapper/luks-2b42ed68-695e-40de-89a9-5529c6443177 splash udev.log_priority=3 resume=UUID=28ded8b9-5fef-4e19-a03d-d0a72520a007 resume_offset=153231360
[    0.080894] Spectre V1 : Mitigation: usercopy/swapgs barriers and __user pointer sanitization
[    0.779449] zswap: loaded using pool zstd/zsmalloc
[    0.781935] RAS: Correctable Errors collector initialized.
[    7.027084] systemd[1]: Clear Stale Hibernate Storage Info was skipped because of an unmet condition check (ConditionPathExists=/sys/firmware/efi/efivars/HibernateLocation-8cf2644b-4b0b-428f-9387-6d876050dc67).
[    7.041943] systemd[1]: Activating swap /swapfile...
[    7.061630] Adding 39845884k swap on /swapfile.  Priority:-2 extents:2320 across:974151680k SS
[    7.062265] systemd[1]: Activated swap /swapfile.
[    7.062454] systemd[1]: Reached target Swaps.
[    9.110549] amdgpu 0000:15:00.0: [drm] *ERROR* lttpr_caps phy_repeater_cnt is 0x0, forcing it to 0x80.
[    9.110552] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.110554] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.144618] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.179071] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.179077] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.180103] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.187435] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.188500] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.565295] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[    9.566363] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[   15.582298] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[   15.583368] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  522.583316] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  522.584385] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  573.497244] ACPI: PM: Hardware changed while hibernated, success doubtful!
[  583.270862] amdgpu 0000:15:00.0: amdgpu: SMU is resumed successfully!
[  583.275093] amdgpu 0000:15:00.0: [drm] *ERROR* lttpr_caps phy_repeater_cnt is 0x0, forcing it to 0x80.
[  583.275095] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.325884] amdgpu 0000:15:00.0: [drm] *ERROR* lttpr_caps phy_repeater_cnt is 0x0, forcing it to 0x80.
[  583.325885] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.325886] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.359947] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.393141] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.393766] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.394778] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.401538] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.402596] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.592826] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
[  583.593883] amdgpu 0000:15:00.0: [drm] *ERROR* LTTPR count is nonzero but invalid lane count reported. Assuming no LTTPR present.
```

A hibernation was successful in this case.
