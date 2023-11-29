### The contents of an inode are

- Inode Number
- Uid
- Gid
- Size
- Atime
- Mtime
- Ctime
- Blocksize
- Mode
- Number of links
- ACLs

### Common Mount Options

- async -> Allows the asynchronous input/output operations on the file system.
- auto -> Allows the file system to be mounted automatically using the mount -a command.
- defaults -> Provides an alias for async,auto,dev,exec,nouser,rw,suid.
- exec -> Allows the execution of binary files on the particular file system.
- loop -> Mounts an image as a loop device.
- noauto -> Default behavior disallows the automatic mount of the file system using the mount -a command.
- noexec -> Disallows the execution of binary files on the particular file system.
- nouser -> Disallows an ordinary user (that is, other than root) to mount and unmount the file system.
- remount -> Remounts the file system in case it is already mounted.
- ro -> Mounts the file system for reading only.
- rw -> Mounts the file system for both reading and writing.
- user -> Allows an ordinary user (that is, other than root) to mount and unmount the file system.
