# Installation guide

This is a simple Arch installation guide for new users.
This guide will be divided into 1 part for making the bootable usb
and 5 parts for the actual installation.

## Usb preparation:

1. First you need to go to the Arch linux website and download the latest Arch iso file([Arch linux site](https://archlinux.org/))

2. Than you need a usb flash drive with a minimum size of 4Gb

3. After this you need a software to falsh the iso into the usb flash drive i reccomand Balena etcher([Balena etcher site](https://etcher.balena.io/))

4. After just select your usb and the arch iso and click flash
   
   ![](img/etcher.png)

5. Wait till it finish falshing and than the usb is ready to be used

6. Now plug your usb flash drive in the pc and boot from it
   
   - to boot from in you can just reboot the pc with the usb in, and press the boot order key (usually is F11 or F12)

7. This is what you should see after booting in
   
   ![](img/root_archiso.png)

## Step 1 |Pre-installation

1. Firstly, if you need to set the your keyboard layout (The default one is US) do this command´s to set it right: 
   
   ```bash
   localctl list-keymaps  #to list all the keyboard layout
   ```
   
   ```bash
   loadkeys <name of the layout> #to set the layout
   ```

2. Now you have to verify the boot mode(UEFI or BIOS/CMS), to do it use this command:
   
   ```bash
   cat /sys/firmware/efi/fw_platform_size #check the boot mood
   ```
- if it gives you **64** or **32** your are respectly on a 64 or 32 bit mashine with UEFI

- and if it gives you **`No such file or directory`** the system may be on BIOS or CMS
3. To install Arch you need to be connected to the internet.  
   
   - If you can, use an **ethernet cable**.  
   
   - If not, use the following commands for Wi-Fi:
     
     ```bash
     iwctl  # tool we are going to use
     ```
     
     ```bash
     [iwd] device list  # list all the devices
     ```
     
     ///-----

4. Now we are going to partition the disk, we are gonna be using **`gdisk`** 
   
   - firstly we need to view the disk we are gonna be partitioning, use te following command to do it:
     
     ```bash
     lsblk
     ```
     
     the result should be something like this (you may have a diffrent label for your disk like 'sda'):
     
     ```bash
     NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
     nvme0n1     259:0    0 931.5G  0 disk 
     ├─nvme0n1p1 259:1    0     2G  0 part /boot
     ├─nvme0n1p2 259:2    0    20G  0 part [SWAP]
     └─nvme0n1p3 259:3    0 909.5G  0 part /
     ```
   
   - now that we know what our disk is we can star partitioning it,
     in order to do that you have to do the following commands:
     
     ```bash
     gdisk /dev/<name of your device> # gdisk /dev/nvme0n1     
     ```
     
     now you wanna write  **`p`** in order to print all of the current partition,
     here is an out put example:
     
     ```bash
     Number  Start (sector)    End (sector)  Size       Code  Name
     1            2048         4196351   2.0 GiB     EF00  EFI system partition
     2         4196352        46139391   20.0 GiB    8200  Linux swap
     3        46139392      1953523711   909.5 GiB   8300  Linux filesystem
     ```
     
     if you see them in order to delete the you have to press **`d`** and then
     write the number of the partition, do it for all of them.
     Now you should not have any partition left, and we are gonna create some new one's,
     in order to do so follow these comands:
     
     the first one is for the boot
     
     write **`n`** to make one partition you are gonna be seeing this promp:
     
     ```bash
     Command (? for help): n
     Partition number (1-128, default 1): # press enter it is just the partition number
     ```
     
     now we are gonna be selecting the size, this firts command 
     is gonna be diffrent if you use **`efi`** or **`bios`**
     
     1. ## Bios
        
        ```bash
        First sector (1953523712-1953525134, default = 1953523712) or {+-}size{KMGTP}: #press enter     
        Last sector (1953523712-1953525134, default = 1953525134) or {+-}size{KMGTP}: +2M #for the bios boot     
        
        Hex code or GUID (L to show codes, Enter = 8300): ef02 #this is the code to put for bios
        ```
     
     2. ## Efi
        
        ```bash
        First sector (1953523712-1953525134, default = 1953523712) or {+-}size{KMGTP}: #press enter               
        Last sector (1953523712-1953525134, default = 1953525134) or {+-}size{KMGTP}: +2G #for the bios boot      
        
        Hex code or GUID (L to show codes, Enter = 8300): EF0O #this is the code to put for bios                  
        ```
     
     now we are going to make the swap partition(you don't relly need to do it
     if you have more than 16gb of ram) to understand the quantity of disk space
     to allocate you should do this simple calculation:
     **`<yourRamAmount> / 3 = <DivRam>`** 
     **`<yourRamAmount> + <DivRam> = swap`**
     and now in order to make it do this following commands:
     
     ```bash
     Command (? for help): n
     Partition number (2-128, default 2): 
     First sector (1953523712-1953525134, default = 1953523712) or {+-}size{KMGTP}: #press enter
     Last sector (1953523712-1953525134, default = 1953525134) or {+-}size{KMGTP}: +<Swap>G #es. +24G
     Current type is 8300 (Linux filesystem)
     Hex code or GUID (L to show codes, Enter = 8300): 8200 #code for swap
     ```
     
     now for the last partition we are gonna be doing the root partition,
     that is gonna be the rest of the disk in order to do it do this following command: 
     
     ```bash
     Command (? for help): n                                                                          
     Partition number (3-128, default 3):                                                             
     First sector (1953523712-1953525134, default = 1953523712) or {+-}size{KMGTP}: #press enter 
     Last sector (1953523712-1953525134, default = 1953525134) or {+-}size{KMGTP}:  #press enter #we use the rest of the disk
     Current type is 8300 (Linux filesystem)                                                          
     Hex code or GUID (L to show codes, Enter = 8300): enter # or write 8300
     ```
     
     now that we have finished partitioning we have to save and quit,
     in order to do so press **`w`** to save and ad the **`[y/n]`**
     press **`y`** and we are done partitioning.
