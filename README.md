# j4fs-block

### Samsung's j4fs source updated after [MoviTool's](http://movitool.ntd.homelinux.org/trac/movitool/wiki/j4fs) patches.

Started on top of a 4.4.1 Kernel, the original hack of using `struct file*->fops->llseek(),read()` directly on top of the `/dev/block` device doesn't work anymore.

**Very** basic support, the "image" needs to be prepared beforehand to be exactly the partition size of the STL flash device.
Headers also need changes to account for the flash underlying block sizes and erase block sizes (the fsd code still uses them for seeking inodes).
