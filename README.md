# -Linux-RGB-Keyboard-Driver-on-Gigabyte-G5-Laptop
**Linux on Gigabyte Laptops has few drivers that support LED activation for keyboard. And if there is support, their GUI driver has been removed from the repositories. So I created a "program" based on "Tuxedo-keyboard" to communicate with the keyboard and you can control the LED using Terminal and shortcut keys.**

>[!CAUTION]
>**This entire guide was performed on Fedora Linux 42 (kernel 6.14.0-63. fc42.x86_64) (Workstation Edition) other operating systems may not work. Tested on Kernel 6.15.10-200. fc42.x86_64 is not compatible.**

>[!IMPORTANT]
>**Make sure "Secure Boot Status = Disabled" by restarting the computer and continuously pressing F2 to enter BIOS. In BIOS select "Admin Secure Boot", set all values ​​to "Disabled" and save.**

>1. Install the kernel-devel package for your current kernel</br>
Depending on the linux system, there will be different installation commands: `sudo apt`, `sudo dnf`,...<br>
```
sudo dnf install kernel-devel-$(uname -r)
```

>2. Download source code from clevo-keyboard<br>
```
sudo dkms remove tuxedo-keyboard/3.2.10 --all
sudo rm -rf /usr/src/tuxedo-keyboard-3.2.10
rm -rf ~/clevo-keyboard
cd ~
git clone https://github.com/wessel-novacustom/clevo-keyboard.git
```

>3. Require DKMS build and module installation

>[!NOTE]
>You will see few lines after running the command, don't worry and do the next steps.<br>
><sub>Executing post-transaction command.............(bad exit status: 1)</sub><br>
><sub>Failed command:</sub><br>
><sub>dracut --regenerate-all --force</sub>
```
sudo cp -R ~/clevo-keyboard /usr/src/tuxedo-keyboard-3.2.10
sudo dkms install -m tuxedo-keyboard -v 3.2.10
```
>4. After installation, load the module, if nothing appears when running the command, it means the load was successful.<br>
```
sudo modprobe tuxedo_keyboard
```

>5. Then check it immediately with the command.<br>
If you see `rgb:kbd_backlight` appear, it means the installation was successful.
```
ls /sys/class/leds/
echo 255 | sudo tee /sys/class/leds/rgb:kbd_backlight/brightness
```

>6. Download this archive
```
cd ~
git clone https://github.com/NightOwlEyes/-Linux-RGB-Keyboard-Driver-on-Gigabyte-G5-Laptop.git
cd ~/"-Linux-RGB-Keyboard-Driver-on-Gigabyte-G5-Laptop"
sudo cp led /usr/local/bin/
sudo cp kbd-backlight-* /etc/systemd/system/
echo tuxedo_keyboard | sudo tee /etc/modules-load.d/tuxedo.conf
```

>7. Give the script executable permissions<br>
```
sudo chmod +x /usr/local/bin/led
```

>8. Label it safe so SELinux doesn't block the script<br>
```
sudo chcon -t bin_t /usr/local/bin/led
```

>9. Tell the system to "Remember to run this service at startup".<br>
```
sudo systemctl enable kbd-backlight-color.service
sudo systemctl enable kbd-backlight-autosave.timer
```

>10. Reload systemd configuration: After modifying a service file, you should always run this command so that the system is aware of the change.<br>
```
sudo systemctl daemon-reload
```

## Now you can use the Key Combination<br>
  Fn + / (num lock) to change the basic keyboard RGB color<br>
  Fn + * (num lock) to turn on/off the keyboard light<br>
  Fn + - (num lock) to turn on/off the keyboard brightness<br>
  Fn + + (num lock) to turn on/off the keyboard brightness<br>
  
## Or you can also<br>
Usage: `sudo led` <command> [parameters]<br>
Command:<br>
  `on` | `off`               Turn on/off the light.<br>
  `bright` <0-255>          Set custom brightness.<br>
  `rgb` <r> <g> <b>         Custom RGB color mixing.<br>
  `save`                    (System) Save current state.<br>
  `restore`                 (System) Restore saved state
