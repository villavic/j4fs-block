# j4fs-block

### Samsung's j4fs source updated after [MoviTool's](http://movitool.ntd.homelinux.org/trac/movitool/wiki/j4fs) patches.

Started on top of a 4.4.1 Kernel, the original hack of using `struct file*->fops->llseek(),read()` directly on top of the `/dev/block` device doesn't work anymore.

**Very** basic support, the "image" needs to be prepared beforehand to be exactly the partition size of the STL flash device.
Headers also need changes to account for the flash underlying block sizes and erase block sizes (the fsd code still uses them for seeking inodes).

The files that would be mounted (via loopback) need to be sized to match the *porting values* at the beggining of `j4fs.h`.
For a `PHYSICAL_PAGE_SIZE` of 4096 and a `PHYSICAL_BLOCK_SIZE` of 256kb (4k x 64), and a `J4FS_BLOCK_COUNT` of 20, the file **must** be of size 5242880 = 4096 x 64 x 20.

Fastest way to acoomplish that: `truncate -s 5M param.lfs`

Then the file can be mounted and operated upon normally: `mount -o loop -t j4fs param.lfs /mnt/lfs`

That file will be too big to flash back into the STL partition, to check where the new size ends use hexdump:


    $ hexdump -C param.lfs.big | tail -4
    *
    00140000  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    *
    00500000


The new size would be 0x140000 or 1310720 bytes in this example, then just dd it out to a flashable file: `dd if=param.lfs.big of=param.lfs.new bs=4096 count=$((1310720 / 4096))`

And that should be it.


### Not tested:

 * Deleting files.
 * Renaming files.
 * Creating directories.
 * Altering file order (which would involve deletion and then creation + remounting for `fsd_reclaimi()` to do it's thing)
