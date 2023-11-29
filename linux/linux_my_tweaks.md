# Linux customizations

>OpenGL works faster with pro divers (4150 vs 3850 Superposition).  
>Seems pro driver is the best stability. But what about performance in vulcan?

### Mount disks

- <https://askubuntu.com/questions/113733/how-to-mount-a-ntfs-partition-in-etc-fstab>  
<https://pimylifeup.com/linux-ntfs/>

    ```bash
    mkdir -p /mnt/sys
    mkdir -p /mnt/docs
    ```

    update /etc/fstab:

    ```bash
    UUID=1696C3A596C383A9   /mnt/docs   ntfs defaults,nls=utf8,umask=000,dmask=027,fmask=137,uid=1000,gid=1000,windows_names,rw 0 0
    UUID=A43616D03616A37A   /mnt/sys    ntfs defaults,nls=utf8,umask=000,dmask=027,fmask=137,uid=1000,gid=1000,windows_names,rw 0 0
    ```

### Set local time in bios instead UTC

- <https://www.howtogeek.com/323390/how-to-fix-windows-and-linux-showing-different-times-when-dual-booting/>

    ```bash
    timedatectl set-local-rtc 1 --adjust-system-clock
    # To restore
    timedatectl set-local-rtc 0 --adjust-system-clock
    ```

### Reduce grub timeout menu

```bash
sudo nano /etc/default/grub
sudo update-grub
```

update GRUB_TIMEOUT=3

### Timeshift. Fix issue in Ubuntu (update to latest version)

```bash
sudo add-apt-repository ppa:teejee2008/timeshift
sudo apt install --only-upgrade timeshift
```

### Viber

- minimize on startup <https://askubuntu.com/questions/720359/how-to-start-viber-minimized-on-system-startup>

    ```bash
    #Configure your linux startup commands (Properties - Startup - Viber) for Viber:
    /opt/viber/Viber StartMinimized
    ```

### Vivaldi

- check GPU acceleration

    ```bash
    vivaldi://gpu
    --enable-features=VaapiVideoEncoder,VaapiVideoDecoder,CanvasOopRasterization
    --enable-unsafe-webgpu
    ```

    <https://forum.manjaro.org/t/chromium-cant-enable-video-encoding-hardware-acceleration/101760/2>
- **Seems not actual** fix opera issue with codecs <https://github.com/Ld-Hagen/fix-opera-linux-ffmpeg-widevine>

### Video Players (interestiong)

- SMPlayer
- Clip <https://invent.kde.org/maui/clip>
- Celluloid <https://celluloid-player.github.io/index.html>
- QMPlay2 <https://github.com/zaps166/QMPlay2>

### Firefox (not actual ??)

- enable hardware acceleration <https://www.ghacks.net/2022/02/11/how-to-fix-hardware-video-acceleration-in-firefox-on-linux/>

### Noto and Roboto fonts install (for browser) - Noto good, Roboto so-so

- copy fonts to ~/.local/share/fonts/  
<https://fonts.google.com/specimen/Roboto?query=roboto>  
<https://fonts.google.com/noto/specimen/Noto+Sans>

### (Gnome like) Create list of images links for background slideshow

- <https://forums.linuxmint.com/viewtopic.php?t=303142&start=20>

    ```bash
    sudo mkdir -p ~/Шаблоны/background/ ; sudo find ~/Изображения/ -type f -iname "*.*p*g" -exec ln -s "{}" ~/Шаблоны/background/ \; ;
    ```

### VmWare workstation fix

- <https://communities.vmware.com/t5/VMware-Workstation-Pro/Windows-10-VM-Slow-in-Ubuntu-22-04-Host-Workstation-Pro-16-2-4/td-p/2924843>

### CoreCtrl to control amd GPU params

- <https://gitlab.com/corectrl/corectrl>  
- <https://gitlab.com/corectrl/corectrl/-/wikis/Setup#full-amd-gpu-controls>  
- minimize to tray <https://imgur.com/2cEKqY4>

### Lutris

> Not sure about lutris - after some reinstaliations it is not working properly

- install Lutres to play games  
    <https://github.com/lutris/docs/blob/master/InstallingDrivers.md>  
    <https://lutris.net/downloads>  
    <https://github.com/lutris/docs/blob/master/WineDependencies.md>
- install gamemode  
    <https://github.com/FeralInteractive/gamemode>
- MangoHud - для мониторинга fps+  
    <https://github.com/flightlessmango/MangoHud>
- Heroic Games Launcher - for epic and gog games  
    <https://heroicgameslauncher.com/downloads>
