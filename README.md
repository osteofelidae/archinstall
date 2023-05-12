# The Dum Dum User's guide to installing Arch Linux
by osteofelidae

**(Warning: contains strong language, mostly 'fuck' and variants thereof.)**

## -1: Preamble

I was just like you once, a complete newbie to linux. I had just grown tired of my Mint install, and was browsing the internet for an alternative. I stumbled upon Arch, and was captivated; all the tech articles had buzzwords like 'Do it Yourself!' and 'rolling release!' After using it for a year, I can safely say that the DIY part, at least, is cool as fuck. Thus, I'm making this guide in the hopes that more of you get sucked down the glorious rabbit hole that is Arch Linux. Enjoy! :)

P.S. I learned from [here](https://www.freecodecamp.org/news/how-to-install-arch-linux/), so this guide will go along much of the same lines. However, I will endeavor to be more explanatory, succint, and passive-aggressive.

## 0: Assumptions

### 0.0: Assumptions I'm making

- You have a bootable Arch install drive. If you don't, get the .iso [here](https://archlinux.org/download/), then use something like [BalenaEtcher](https://www.balena.io/etcher) to flash it onto an external flash drive.
- Your computer is using UEFI with Safe Boot off. (Safe boot can usually be disabled from the UEFI menu at startup. The UEFI menu is accessed in different ways on different systems. If you don't know how, google it for your specific system.)
	- Note that on some systems with Windows already installed, you may have to disable 'fast startup'. To do this, go to `[Start menu] > 'Choose a power plan' > 'Choose what the power buttons do' > 'Turn on fast startup'` and uncheck the check box.

## 1: Preparation

### 1.0: Live booting the installation media

1. Plug in the Arch install drive.
2. Get to the boot menu. (Again, this is accessed in different ways on different systems. If you don't know how, google it for your specific system.)
3. Boot from the USB drive you just installed.
4. On the menu that appears, select the first option containing something along the lines of 'Arch Linux install medium'.
5. Wait for it to load (it may take a while) and don't touch the computer until it gives you a command prompt along the lines of `root@archiso ~ #`. 
	- The `#` at the end signifies that you are logged in as the root user, as opposed to a `$` which signifies a normal user.

### 1.1: Keyboard format

You *probably* won't have to do this step. The only reason why you *should* is if you use a mac (macs suck /j) or if you have a foreign/non-qwerty keyboard.

Many things in Linux are stored as files (or folders), including system stats (temperature, cpu usage, memory usage, etc.), connected devices and peripherals, and keymaps. The keymaps are stored in `/usr/share/kbd/keymaps`. 
1. Run the command `ls /usr/share/kbd/keymaps/**/*.map.gz` to display them.
	- The `ls` command lists all files in a given directory.
	- `*` is called a 'wildcard', and means 'anything'. In this case, `*.map.gz` means 'anything that ends in .map.gz'.
	- `**` means 'any directories and subdirectories the current directory contains. In this case, it references `.../keymaps/mac`, `.../keymaps/sum`, etc..
2. Run `loadkeys <keymap name>` to load a keymap. For example, to load `.../mac-us.map.gz`, run `loadkeys mac-us`. (Notice how the file path and extension are not included.)

### 1.2: Connecting to the internet (this is hella important!!!)

- If you're using an ethernet connection or are on a virtual machine, skip this section.
- If you need to connect to wifi to access the internet, read on.

To install Arch Linux, you *must* connect to the internet. We will use `iwd` (iNet Wireless Daemon) to do so.
1. Run `iwctl`. This starts the iNet Wireless ConTroL utility. You should see a prompt similar to `[iwd]#`.
2. Run `device list`. This, quite obviously, lists the available wireless network devices detected. Take note of the device name of your network card (this is often `wlan0`.)
3. Run `station <device name> scan`. For example, if your network card is named `wlan0`, you would run `station wlan0 scan`. This scans your surroundings for networks `<device name>` can connect to. *This will not list the networks found. Don't panic - we're getting to that part.*
4. Run `station <device name> get-networks`. This will list the networks found in the previous step.
5. Run `station <device name> connect <network SSID>`. Obviously, this connects to the network you specify in the command. It may ask for the network password.
6. Run `exit` to exit `iwctl`.
7. You can test if you are connected by running `ping 8.8.8.8`.
	- `ping` is a command which sends a packet of data to a given address and awaits a response.
	- `8.8.8.8` is a Google name server. If it is down, we are probably in a nuclear apocalypse, and installing Arch Linux is probably not your biggest concern.

### 1.3: Partitioning the disks

This is the most fuck-up-able step. Don't back up your computer because you are perfect and don't make mistakes.

1. Run `fdisk -l` to list all disks and partitions. *This does not list free space.* Take note of the device you want to install arch linux on.
	- `fdisk` is a disk utility commonly found on DOS, Linux, and Windows.
	- `-l` is a flag which tells `fdisk` to list the partitions it sees.
2. You can also run `fdisk -l <device name>` to list the partitions inside that device. For example, if your device is named `/dev/sda1`, run `fdisk -l /dev/sda1`.
3. Run `cfdisk <device name>`.
	- `cfdisk` is fdisk but with a better ui.
	- the `c` stands for Curses, which is a library that allows interactive gui-like elements in a terminal window.
4. If you are installing Arch on a clean disk (or a virtual machine), you will likely get a prompt that says 'Select label type'. Choose `gpt`.
5. Navigate to the free space you wish to use (up and down arrow keys) and select `[New]` (right and left arrow keys + enter).
6. Create a partition of at least 500MB size.
7. Repeat steps 4 and 5 to create another partition of whatever size you want. This is going to be where the actual OS and your files are, so make it big as fuck. (We call this the 'root partition'.)
8. Optional: repeat steps 4 and 5 to create another partition. This is going to be the swap partition, space which is used to offload stuff in RAM when the RAM fills up. For most modern systems, you probably won't need this, but I always make one so I can show it off to my friends.
9. Now select the partition you made in step 6. Select `[Type]`. In the menu that appears, select `EFI System`.
10. You don't have to change the file type of the second partition that you made. It's already `Linux filesystem`. If you made a swap partition, change the type of that to `Linux swap`.
11. Select `[Write]`. (If you fucked up, don't.)
12. Select `[Quit]`.

### 1.4: Formatting the partitions

This is a direct continuation of part 1.3.

1. Run `fdisk -l` again. You should see the new partitions you just made. Take a note of their names.
2. Run `mkfs.fat -F32 <EFI partition name>`.
	- `mkfs` is a command which generates a filesystem in a given partition.
	- `.fat` is a variant of that command which generates a FAT filesystem.
	- `-F32` is a flag which specifies the FAT32 variant of the FAT filesystem.
3. Run `mkfs.ext4 <Root partition name>`.
4. If you made a swap partition in section 1.3, run `mkswap <Swap partition name>`.

### 1.5: Mounting the partitions

We now need to mount some of the partitions in order to access the files in them. (There are currently no files in them, we need to add some. If there are already files, you may have a ghost in your house. Run away.)

1. Run `mount <Root partition name> /mnt`. If you forgot what the root partition name was, remember that you can run `fdisk -l` for a list of partitions.
	- `mount` obviously mounts a given partition. In linux, partitions are represented as directories in the root filesystem, so mounting is synonymous to assigning a partition a directory name. Partitions must be mounted to be accessed.
	- `/mnt` is a folder *in your live boot of the arch installer*. `/mnt` is a folder in linux systems which is commonly used as a temporary mount point for partitions. Thus, it is perfect for our needs right now.
2. If you made a swap partition, run `swapon <Swap partition name>`.
	- `swapon` tells the system to use a given partition as a swap partition.

## 2: Installing the base system (finally)

### 2.0: Installing the base system

1. Run `pacman -Sy` (case sensitive).
	- `pacman` is the Arch Linux package manager. It can download and install software for you.
	- `-S` is a flag that tells `pacman` to sync its cache with the online mirror list.
	- `y` is a flag (sub-flag?) that tells `pacman` to refresh its local copy of the master package database with those it finds online.
2. Run `pacstrap /mnt base base-devel linux linux-firmware sudo nano ntfs-3g networkmanager iwd`. Holy shit what a mouthful.
	- `pacstrap` is the equivalent of `pacman` but for a specified root directory - here, it is `/mnt`. Since we mounted our new root partition on `/mnt`, we are now installing packages on the root partition.
	- `base` is the package group for a base Arch install.
	- `base-devel` is a package group for common build dependencies.
	- `linux` is the linux kernel.
	- `linux-firmware` are common drivers.
	- `sudo` is a tool which allows us to execute commands as the superuser. More on that later on.
	- `nano` is a command-line text editor.
	- `ntfs-3g` is a package which allows us to access and modify ntfs partitions, such as windows installations.
	- `networkmanager` is a package which allows us to access the internet.
	- `iwd` is a package which allows you to easily control `networkmanager`, like in section 1.2.
3. Wait until you see the command prompt again. This is going to take a while.

### 2.1: Configuration

1. Run `genfstab -U /mnt >> /mnt/etc/fstab`. This will generate the fstab file.
	- The fstab file is a file which defines how external devices should be mounted.
	- `genfstab` generates a list of mount points below a given one, here `/mnt`.
	- `>>` redirects the output of the command into a certain file, here `/mnt/etc/fstab`.
2. Run `arch-chroot /mnt`.
	- `arch-chroot` logs you in as the root user of another Arch system, here specified by `/mnt` (the one you just installed.)
3. Run `timedatectl set-ntp true`.
	- `timedatectl` is a package which allows you to do various things with the system's clock.
	- `set-ntp true` tells `timedatectl` to sync the system clock using NTP. NTP (Network Time Protocol) is used to sync clocks online.
4. Run `ls /usr/share/zoneinfo/**/*`. Remember how earlier I said that timezones are stored in files? Here they are.
5. Run `ln -sf /usr/share/zoneinfo/<Timezone path> /etc/localtime`. For example, if you live in Phoenix, Arizona, use `ln -sf /usr/share/zoneinfo/America/Phoenix /etc/localtime`. This sets your timezone.
	- `ln` creates a link between files.
	- `-s` creates a symbolic link (shortcut).
	- `-f` removes the destination file if it exists, here `/etc/localtime`.
6. Run `nano /etc/locale.gen`. Uncomment (remove the `#`) from any languages you want to use. Most commonly, this will only be `en_US.UTF-8`. Save the file (ctrl+o then enter) and exit nano (ctrl+x).
7. Run `locale-gen`.
	- `locale-gen` reads the file you have just edited and generates the locales accordingly. You should see all the lines you uncommented in step 6 be printed on the screen. If you don't, you fucked up.
8. If you changed your keymaps while installing, make them persist. Run `nano /etc/vconsole.conf` and add the line `KEYMAP=<Keymap name>`. For example, if you used the keymap `mac-us`, add `KEYMAP=mac-us`,
9. Run `nano /etc/hostname`. Add the line `<Hostname>` where 'Hostname' is whatever you want your computer to be called. Save and exit.
10. Run `nano /etc/hosts`.
	- `/etc/hosts` is a file that contains explicit hostname definitions.
11. Add the following lines: `127.0.0.1	localhost`, `::1	localhost`, `127.0.1.1	<Hostname>`. Save and exit.
12. Run `systemctl enable NetworkManager`.
	- `systemctl` is a tool for controlling startup programs.
	- `enable NetworkManager` tells `systemctl` that you want to start `NetworkManager` when the system starts.
13. Run `passwd`. Enter a new password in the prompt.
	- `passwd` sets the password of the given user. If no user is given, it sets the password of the current user (which is root).
14. Run `useradd -m -G wheel <Username>`.
	- `useradd` obviously creates a new user.
	- `-m` also creates the home directory for that user.
	- `-G wheel` adds the user to the `wheel` group, which is the administration group in Arch Linux.
15. Run `passwd <Username>`. Enter a new password in the prompt.
16. Run `nano /etc/sudoers`. Uncomment the line `# %wheel ALL=(ALL:ALL) ALL`.
	- This allows the `wheel` user group to execute the `sudo` command.

## 2.2: Installing Grub

We will first install microcode, which is the instruction set for your processor and serves as a translator between Arch and your processor.
- If you have an intel processor, run `pacman -S intel-ucode`.
- If you have an AMD processor, run `pacman -S amd-ucode`.

To load your new arch install, we need a bootloader. We will be using `grub` because it is a funny word (and because it is one of the more popular ones and can detect ext4 partitions like the one our Arch install is in.)

1. Run `pacman -S grub efibootmgr`.
	- `grub` is obviously `grub`, our bootloader.
	- `efibootmgr` is a tool which allows us to modify the EFI boot manager.
2. If you have other OSs installed on the same disk, run `pacman -S os-prober`.
3. Run `mkdir /boot/efi`.
	- `mkdir` makes a directory at a given location.
4. Run `mount <EFI partition name> /boot/efi`.
5. Run `grub-install --target=x86_64-efi --bootloader-id=grub`.
	- `grub-install` installs `grub`.
	- `--target=x86_64-efi` is a flag that tells `grub` what type of system you are using.
	- `--bootloader-id` is the name of the bootloader.
6. If you have installed `os-prober`, run `nano /etc/default/grub`, find the line `#GRUB_DISABLE_OS_PROBER=false` and uncomment it.
7. Run `grub-mkconfig -o /boot/grub/grub.cfg`
	- `grub-mkconfig` generates the `grub` configuration.
	- `-o`saves the generated configuration to the given file.

Congrats, you're done (If you didn't fuck up).

## 3: Quality of life

(I haven't done this part yet)
