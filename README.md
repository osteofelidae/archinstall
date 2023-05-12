# The Noob's guide to installing Arch Linux

## -1: Preamble

I was just like you once, a complete newbie to linux. I had just grown tired of my Mint install, and was browsing the internet for an alternative. I stumbled upon Arch, and was captivated; all the tech articles had buzzwords like 'Do it Yourself!' and 'rolling release!' After using it for a year, I can safely say that the DIY part, at least, is cool as fuck. Thus, I'm making this guide in the hopes that more of you get sucked down the glorious rabbit hole that is Arch Linux. Enjoy! :)

## 0: Assumptions

### 0.0: Assumptions I'm making

- You have a bootable Arch install drive. If you don't, get the .iso [https://archlinux.org/download/](here), then use something like [https://www.balena.io/etcher](BalenaEtcher) to flash it onto an external flash drive.
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
	- The `ls` command lists all files in a given directory. Learn more by reading the [https://man7.org/linux/man-pages/man1/ls.1.html](ls manpage \(manual page\)). 
	- 
