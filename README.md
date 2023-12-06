# rebuild kernel
    step1: Install the necessary toolchains for kernel build.
    sudo apt install git bc bison flex libssl-dev make libc6-dev libncurses5-dev libmpc-dev libelf-dev

Step2: Install the 32-bit Toolchain for a 32-bit Kernel & 64-bit Toolchain for a 64-bit Kernel
    sudo apt install crossbuild-essential-armhf
    sudo apt install crossbuild-essential-arm64

Step3: Get the Kernel Sources
    git clone --depth=1 -b rpi-6.1.y https://github.com/raspberrypi/linux

Step4: Build sources
* 32-bit Configs : 
For Raspberry Pi 2, 3, 3+ and Zero 2 W, and Raspberry Pi Compute Modules 3 and 3+
    cd linux
    KERNEL=kernel7
    make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcm2709_defconfig
    make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig

If for Raspberry Pi 4 and 400, and Raspberry Pi Compute Module 4:
    cd linux
    KERNEL=kernel7l
    make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcm2711_defconfig
    make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig

* 64-bit Configs
    cd linux
    KERNEL=kernel8
    make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcm2711_defconfig
    make ARCH=arm CROSS_COMPILE=aarch64-linux-gnu- menuconfig
    
Step5: build kernel
For all 32-bit Builds
    make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage modules dtbs

For all 64-bit Builds
    make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image modules dtbs

Step6: Install Directly onto the SD Card
    lsblk 
    mkdir mnt
    mkdir mnt/fat32
    mkdir mnt/ext4
    sudo mount /dev/sdb1 mnt/fat32
    sudo mount /dev/sdb2 mnt/ext4

For 32-bit:
    sudo env PATH=$PATH make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=mnt/ext4 modules_install

For 64-bit
    sudo env PATH=$PATH make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=mnt/ext4 modules_install

step7: Copy the kernel and Device Tree blobs onto the SD card
For 32-bit:
    sudo cp arch/arm/boot/zImage mnt/fat32/kernel-2023.img

    sudo cp arch/arm/boot/dts/*.dtb mnt/fat32/

    sudo cp arch/arm/boot/dts/overlays/*.dtb* mnt/fat32/overlays/

    sudo cp arch/arm/boot/dts/overlays/README mnt/fat32/overlays/

    sudo umount mnt/fat32

    sudo umount mnt/ext4

For 64-bit:
    sudo cp arch/arm64/boot/Image mnt/fat32/kernel-2023.img

    sudo cp arch/arm64/boot/dts/broadcom/*.dtb mnt/fat32/

    sudo cp arch/arm64/boot/dts/overlays/*.dtb* mnt/fat32/overlays/

    sudo cp arch/arm64/boot/dts/overlays/README mnt/fat32/overlays/

    sudo umount mnt/fat32
    
    sudo umount mnt/ext4

Edit the "config.txt" file to select the kernel that the Raspberry Pi will boot:
    kernel=kernel-2023.img
Finally, plug the card into the Raspberry Pi and boot it!