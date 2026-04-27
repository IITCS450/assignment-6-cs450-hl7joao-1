I implemented symbolic links in xv6-public x86 by adding a new inode type, T_SYMLINK, and a new syscall, symlink(const char *target, const char *linkpath). The symlink target path is stored as a NUL-terminated string in the inode’s data blocks.

I updated the system call interface by modifying syscall.h, user.h, usys.s, and syscall.c. I implemented sys_symlink() in sysfile.c using create() and writei() so that the target path is written into the new symbolic link inode.

I modified sys_open() so that when a pathname resolves to a symbolic link inode, the kernel reads the stored target path and continues pathname resolution. This supports chained symbolic links. To prevent infinite loops, I enforced a maximum resolution depth of 10. If the limit is exceeded, open fails.

I added testsymlink to the build and verified that symbolic link creation works, reading through a symlink returns the target file’s contents, and cyclic symbolic links fail as expected due to the depth limit.
