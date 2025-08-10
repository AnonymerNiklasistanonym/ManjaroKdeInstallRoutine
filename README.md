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
