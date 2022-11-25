# u-boot general case 

ref : https://github.com/ARM-software/u-boot/blob/master/doc/README.qemu-arm

ref : https://pandysong.github.io/blog/post/run_u-boot_in_qemu/

## for arm 32bit arch

export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabi-

make qemu_arm_defconfig

make -j $(nproc) 

### qemu verify 

qemu-system-arm -machine virt -nographic -bios u-boot.bin


## for arm 64bit arch
export ARCH=arm64

export CROSS_COMPILE=aarch64-linux-gnu-

make qemu_arm64_defconfig

make -j $(nproc)
### qemu verify 

qemu-system-aarch64 -machine virt -cpu cortex-a57 -nographic -bios u-boot.bin

# u-boot build and debug flow 

ref : https://shenki.github.io/debugging-u-boot-after-relocation/

sudo apt install gdb-multiarch qemu-system-arm gcc-arm-linux-gnueabi flex

git clone git://git.denx.de/u-boot.git

cd u-boot

export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabi-

make clean       (if have used make command)

make mrproper    (if have used make command)

make evb-ast2500_defconfig

make -j $(nproc)

##  make u-boot image for gemu

 dd if=/dev/zero of=test.img count=32 bs=1M

 dd if=u-boot.bin of=test.img conv=notrunc

##  qemu usage 

qemu-system-arm -M ast2500-evb -nographic -drive file=test.img,format=raw,if=mtd

qemu-system-arm -M tacoma-bmc -nographic -drive file=test.img,format=raw,if=mtd

### if need to wait gdb 
qemu-system-arm -M ast2500-evb -nographic -drive file=test.img,format=raw,if=mtd,readonly -s -S 

qemu-system-arm -M tacoma-bmc -nographic -drive file=test.img,format=raw,if=mtd,readonly -s -S

##  AST2600 

make clean       (if have used make command)

make mrproper    (if have used make command)

make evb-ast2600_defconfig

make -j $(nproc)

##  make qemu boot image for ast2600 u-boot/u-boot-spl (not complete , just reference)
ref : https://lists.ozlabs.org/pipermail/openbmc/2021-March/025172.html


dd of=test.img if=/dev/zero bs=1M count=128
dd of=test.img if=./spl/u-boot-spl.bin conv=notrunc
dd of=test.img if=./u-boot.bin conv=notrunc bs=1K seek=64

qemu-system-arm -M rainier-bmc -nographic -drive file=test.img,if=sd,format=raw,id=sd0,index=2 -nodefaults -serial mon:stdio

qemu-system-arm -m 512 -M tacoma-bmc -nographic -drive file=./test.img,format=raw,if=mtd -net nic -net user

## make u-boot image 
ref : https://stdrc.cc/post/2021/02/23/u-boot-qemu-virt/

mkimage -A arm64 -C none -T kernel -a 0x40000000 -e 0x40000000 -n qemu-virt-hello -d build/kernel.bin uImage