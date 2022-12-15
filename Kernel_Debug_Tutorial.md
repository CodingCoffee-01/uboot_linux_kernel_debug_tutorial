#  Kernel Debug Tutorial 
ref : https://programming.vip/docs/teach-you-how-to-debug-linux-kernel-with-vs-code-qemu-gdb.html

#  check gcc and gdb in gitpod 
gcc -v 
gdb -v 

#   install qemu-system-arm qemu-system-x86
sudo apt install qemu cpio qemu-system qemu-system-i386

nano hello.c 

### hello.c 

`#include <stdio.h>
int main()
{
    printf("hello world!");
    printf("hello world!");
    printf("hello world!");
    printf("hello world!");
    fflush(stdout);
    while(1);
    return 0;
}`

### ---------------------------------

touch hello.c

gcc --static -o helloworld hello.c

echo helloworld | cpio -o --format=newc > rootfs

qemu-system-x86_64 -kernel ./arch/x86/boot/bzImage -initrd ./rootfs -append "root=/dev/ram rdinit=/helloworld" -smp 2  -s -S -nographic


#  gdb to connect with kernel 

gdb ./vmLinux

## Debug the following in gdb

target remote:1234

b start_kernel

c

## Gitpod VScode have been installed 

ref : https://www.gitpod.io/docs/languages/cpp 

