# raspberryImgScripts
OS Image scripts for RaspberryPi

# Usefull tools
Add to .bashrc:
```
mntIMG() {
    sectorSize=`sudo fdisk -l $1 | grep "Sector size" | awk -F' ' '{print $4}'`
    fat32Offset=`sudo fdisk -l $1 | grep FAT32 | awk -F' ' '{print $2}'`
    linuxOffset=`sudo fdisk -l $1 | grep Linux | awk -F' ' '{print $2}'`

    umount /mnt/FAT32 || /bin/true
    umount /mnt/LINUX || /bin/true
    mkdir -p /mnt/FAT32
    mkdir -p /mnt/LINUX
    mount -o offset=$($fat32Offset*$sectorSize) $1 /mnt/FAT32
    mount -o offset=$($linuxOffset*$sectorSize) $1 /mnt/LINUX
}

umntLINUX() {
    umount /mnt/FAT32 || /bin/true
    umount /mnt/LINUX || /bin/true
    rmdir /mnt/FAT32
    rmdir /mnt/LINUX
}

growIMG() {
    echo "Inflating Linux Partition..."
    dd if=/dev/zero bs=1M count=$2 >> $1
    sudo growpart $1 2
}
```