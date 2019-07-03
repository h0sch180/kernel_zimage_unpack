### What is it?

** unpack.sh ** is a simple script that allows you to work with Linux Kernel zImage (ARM, Little Endian) from MTK (Mediatek). Suppose you pulled a Linux Kernel image from boot.img or recovery.img. In general, it consists of the kernel binary itself, compressed with gzip, dtb, loader, and possibly some overhead information.

There are several options, for example:

* At the command file recovery.img-kernel content is defined as the Linux kernel ARM boot executable zImage (little-endian).
* Content is defined as gzip compressed data, max compression, from Unix.

In the first case, the file structure is as follows, first comes the boot executable code, then GZip of the kernel itself, then the offset table and then DTB, starting with the DTB_MAGIC signature - D0 0D FE ED (0xEDFE0DD0). What is the offset table?

* DWORD (0x0)
* DWORD (0x0)
* DWORD (0x0)
* Pointer to DTB_MAGIC
* Pointer to offset table (i.e., to the first DWORD from this list)
* ... (etc.)

In the second case, the structure is simpler, there are no boot executables, the GZip kernel comes immediately, and then DTB, starting with dtb-magic.

The script correctly processes (at least on test examples) both options.

The launch is as follows:

unpack.sh recovery.img-kernel

As a result of the script, three files will be generated:

* 1_kernel_header.bin
* 2_kernel_gzip.gz
* 3_kernel_footer.bin

With the names, I think everything is clear - the first is the boot executable code (if it exists), the second is directly clean kernel.gz, and the third is the offset table (if available) + DTB. The total size of these three files should be equal to the size of the original recovery.img-kernel. If this condition is met, then the kernel is unpacked correctly.

Now you can unzip 2_kernel_gzip.gz and study (or modify) the kernel binary. When modifying, it is important that the size of the resulting gz be equal to the size of the original gzip. You can collect everything back like this:

cat 1_kernel_header.bin 2_kernel_gzip.gz 3_kernel_footer.bin> recovery.img-kernel-new

The script was written thanks to the questions that arose when communicating with ** jemmini ** and ** hyperion70 **. In any case, it does not pretend to perfection, just a convenient "utility" in order not to launch that HIEW from under Wine and not to cut the recovery.img-kernel manually.
