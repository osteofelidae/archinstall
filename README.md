# The Noob's guide to installing Arch Linux
**(Warning; contains strong language.)**

## -1: Preamble

I was just like you once, a complete newbie to linux. I had just grown tired of my Mint install, and was browsing the internet for an alternative. I stumbled upon Arch, and was captivated; all the tech articles had buzzwords like 'Do it Yourself!' and 'rolling release!' After using it for a year, I can safely say that the DIY part, at least, is cool as fuck. Thus, I'm making this guide in the hopes that more of you get sucked down the glorious rabbit hole that is Arch Linux. Enjoy! :)

## 0: Assumptions

### 0.0: Assumptions I'm making

- You have a bootable Arch install drive. If you don't, get the .iso [here](https://archlinux.org/download/), then use something like [BalenaEtcher](https://www.balena.io/etcher) to flash it onto an external flash drive.
- Your computer is using UEFI with Safe Boot off. (Safe boot can usually be disabled from the UEFI menu at startup. The UEFI menu is accessed in different ways on different systems. If you don't know how, google it for your specific system.)
	- Note that on some systems with Windows already installed, you may have to disable 'fast startup'. To do this, go to `[Start menu] > 'Choose a power plan' > 'Choose what the power buttons do' > 'Turn on fast startup'` and uncheck the check box.

## 1: Installing the base system

### 1.0: Live booting the installation media

1. Plug in the Arch install drive.
2. Get to the boot menu. (Again, this is accessed in different ways on different systems. If you don't know how, google it for your specific system.)
3. Boot from the USB drive you just installed.
4. On the menu that appears, select the first option containing something along the lines of 'Arch Linux install medium'.
5. Wait for it to load (it may take a while) and don't touch the computer until it gives you a command prompt along the lines of `root@archiso ~ #`. (The `#` at the end signifies that you are logged in as the root user, as opposed to a `$` which signifies a normal user.) 

### 1.1: Keyboard format

You *probably* won't have to do this step. The only reason why you *should* is if you use a mac (macs suck /j) or if you have a foreign/non-qwerty keyboard.

Many things in Linux are stored as files (or folders), including system stats (temperature, cpu usage, memory usage, etc.), connected devices and peripherals, and keymaps. The keymaps are stored in `/usr/share/kbd/keymaps`. 
- Run the command `ls /usr/share/kbd/keymaps/**/*.map.gz` to display them.
	- The `ls` command lists all files in a given directory. Learn more by reading the [ls manpage (manual page)](https://man7.org/linux/man-pages/man1/ls.1.html).
	- `*` is called a 'wildcard', and means 'anything'. In this case, `*.map.gz` means 'anything that ends in .map.gz'.
	- `**` means 'any directories and subdirectories the current directory contains. In this case, it references `.../keymaps/mac`, `.../keymaps/sum`, etc..
- Run `loadkeys <keymap name>` to load a keymap. For example, to load `.../mac-us.map.gz`, run `loadkeys mac-us`. (Notice how the file path and extension are not included.

### 1.2: Connecting to the internet (this is hella important!!!)

- If you're using an ethernet connection or are on a virtual machine, skip this section.
- If you need to connect to wifi to access the internet, read on.

To install Arch Linux, you *must* connect to the internet. We will use `iwd` (iNet Wireless Daemon) to do so.
1. Run `iwctl`. This starts the iNet Wireless ConTroL utility. You should see a prompt similar to `[iwd]#`.
2. Run `device list`. This, quite obviously, lists the available wireless network devices detected. Take note of the device name of your network card (this is often `wlan0`.
3. Run `station <device name> scan`. For example, if your network card is named `wlan0`, you would run `station wlan0 scan`. This scans your surroundings for networks `<device name>` can connect to. *This will not list the networks found. Don't panic - we're getting to that part.*
4. Run `station <device name> get-networks`. This will list the networks found in the previous step.
5. Run `station <device name> connect <network SSID>`. Obviously, this connects to the network you specify in the command. It may ask for the network password.
6. Run `exit` to exit `iwctl`.

### 1.3: Partitioning the disks

This is the most fuck-up-able step. Don't back up your computer because you are perfect and don't make mistakes.

1. Run `fdisk -l` to list all disks and partitions. *This does not list free space.* Take note of the device you want to install arch linux on.
2. You can also run `fdisk -l <device name>` to list the partitions inside that device. For example, if your device is named `/dev/sda1`, run `fdisk -l /dev/sda1`.
3. Run `cfdisk <device name>`. This is a utility which will allow you to manage partitions in `<device name>`.
4. If you are installing Arch on a clean disk (or a virtual machine), you will likely get a prompt that says 'Select label type'. Choose `gpt`.
5. Navigate to the free space you wish to use (up and down arrow keys) and select `[New]` (right and left arrow keys + enter).
6. Create a partition of at least 500MB size.
7. Repeat steps 4 and 5 to create another partition of whatever size you want. This is going to be where the actual OS and your files are, so make it big as fuck.
8. Optional: repeat steps 4 and 5 to create another partition. This is going to be the swap partition, space which is used to offload stuff in RAM when the RAM fills up. For most modern systems, you probably won't need this, but I always make one so I can show it off to my friends.
9. Now select the partition you made in step 6. Select `[Type]`. In the menu that appears, select `EFI System`.
10. You don't have to change the file type of the second partition that you made. It's already `Linux filesystem`. If you made a swap partition, change the type of that to `Linux swap`.
