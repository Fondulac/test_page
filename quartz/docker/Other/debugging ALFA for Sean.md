
```
sudo apt-get install realtek-rtl88xxau-dkms;
sudo apt-get install dkms;
git clone [https://github.com/aircrack-ng/rtl8812au](https://github.com/aircrack-ng/rtl8812au.git);
cd rtl8812au;
make;
sudo make install; 
```


# Attempt 1

Attempts to follow this [guide](https://hackernoon.com/configuring-the-alpha-awus036ach-wi-fi-adapter-on-kali-linux) resulted in failures at the make command (count not find directories). The attempt took place on a Kali Linux virtual machine using 6.6.9-amd64.

running sudo apt upgrade (potentially also installing new system headers as well) seemed to result in the card being detected after running sudo apt install realtek-rtl88xxau-dkms - however, the card did not detect networks with airodump-ng. 

# Attempt 2

Attempt 2 consists of repeating the installation process on an Ubuntu machine (bare metal)
resulted in successful installation but no detected networks.

# Attempt 3

1. Installed new kali linux virtualbox iso image
2. sudo apt update
3. cloned driver repo - failed to locate header dir
4. sudo apt install linux-header-`uname -r` (failed)
5. sudo apt dist-upgrade

# Further Attempts
- Attempted debugging with additional ALFA adapter  - no change 
- Attempted debugging on new VM and a different laptop - no change
- dmesg does not provide additional information - probably not a kernel issue


# Research information for potential new avenues 

- Power issues - USB 2.0 may not be able to provide enough power, try finding a USB 3.0 connection (likely not the issue: Thinkpad T470 only has USB 3.0. Some sources note issues with USB 3.1)



