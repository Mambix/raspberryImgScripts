# raspberryImgScripts
OS Image scripts for RaspberryPi

# Usefull tools
Add to .bashrc:
```
mntIMG() {
    sectorSize=`fdisk -l $1 | grep "Sector size" | awk -F' ' '{print $4}'`
    fat32Offset=`fdisk -l $1 | grep FAT32 | awk -F' ' '{print $2}'`
    fat32Size=`fdisk -l $1 | grep FAT32 | awk -F' ' '{print $4}'`
    linuxOffset=`fdisk -l $1 | grep Linux | awk -F' ' '{print $2}'`
    linuxSize=`fdisk -l $1 | grep Linux | awk -F' ' '{print $4}'`

    umount /mnt/FAT32 || /bin/true
    umount /mnt/LINUX || /bin/true
    mkdir -p /mnt/FAT32
    mkdir -p /mnt/LINUX
    mount -o loop,offset=$(($fat32Offset*$sectorSize)),sizelimit=$(($fat32Size*$sectorSize)) $1 /mnt/FAT32
    mount -o loop,offset=$(($linuxOffset*$sectorSize)),sizelimit=$(($linuxSize*$sectorSize)) $1 /mnt/LINUX
}

umntIMG() {
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