# How Linux Works What Every Superuser Should Know

## Table of contents:
1. [Chapter 1: The big picture](#Chapter1)
2. [Chapter 2: Basic Commands and Directory Hierarchy](#Chapter2)
3. [Chapter 3: Devices](#Chapter3)
4. [Chapter 4: Disks and Filesystems](#Chapter4)
5. [Chapter 5: How the Linux Kernel Boots](#Chapter5)
6. [Chapter 6: How User Space Starts](#Chapter6)
7. [Chapter 7: System Configuration: Logging, System Time, Batch Jobs, and Users](#Chapter7)
8. [Chapter 8: A Closer Look at Processes and Resource Utilization](#Chapter8)
9. [Chapter 9: Understanding Your Network and Its Configuration](#Chapter9)
10. [Chapter 10: Network Applications and Services](#Chapter10)
11. [Chapter 11: Introduction to Shell Scripts](#Chapter11)
12. [Chapter 12: Moving Files Across the Network](#Chapter12)
13. [Chapter 13: User Environments](#Chapter13)
14. [Chapter 14: A Brief Survey of the Linux Desktop Chapter](#Chapter14)
15. [Chapter 15: Development Tools](#Chapter15)
16. [Chapter 16: Introduction to Compiling Software from C Source](#Chapter16)
17. [Chapter 17: Building on the Basics](#Chapter17)


## Chapter 1: The big picture<a name="Chapter1"></a>
### Levels and layers of abstraction in a linux system
A layer or level is a classification (or grouping) of a component according to where that component sits between 
the user and the hardware. A Linux system has three main levels: Hardware, kernel and processes which makes 
collectively the _user space_.
The kernel runs in kernel mode, which has unrestricted access to the processor and main memory. User processes run in
 user mode, which restricts access to a (usually quite small) subset of memory and safe CPU operations.
 
### Hardware: understanding main memory
Running kernel and processes reside in memory (most important part of hardware), a CPU is just an operator on memory.
 _State_ in reference to memory, is a particular arrangement of bits.
 
### The Kernel
One of the kernel’s tasks is to split memory into many subdivisions, and it must maintain certain state information 
about those subdivisions at all times. The kernel is in charge of managing tasks in four general system areas:

    * Processes: determines which processes are allowed to use the CPU
    * Memory: keeps track of all memory, currently allocated to a particular process, shared between processes or free
    * Device drivers: acts as an interface between hardware (such as a disk) and processes
    * System calls and support: processes normally use system calls to communicate with the kernel
    
#### Process Management
Process management describes the starting, pausing, resuming, and terminating of processes. The act of one process 
giving up control of the CPU to another process is called a context switch (kernel is responsible for this).

#### Memory Management
The kernel must manage memory during a context switch, for this CPUs include a memory management unit (MMU) which 
acts like a proxy between process memory and physical location of the memory itself (The implementation of a memory 
address map is called a page table).

#### Device Drivers and Management
A device is typically accessible only in kernel mode because improper access could crash the machine and the 
impedance between devices programming interfaces.

#### System Calls and Support
system calls (or syscalls) perform specific tasks that a user process alone cannot do it or do well. Two important ones: 

    * fork(): call that creates a nearly identical copy of the process.
    * exec(): When a process calls exec(program), the kernel starts program, replacing the current process.
    
Other than init, all user processes on a Linux system start as a result of _fork()_, and most of the 
time, you also run exec() to start a new program instead of running a copy of an existing process.

### User space
The main memory that the kernel allocates for user processes is called user space. Because a process is simply a 
state (or image) in memory, user space also refers to the memory for the entire collection of running processes.

### Users
A user is an entity that can run processes and own files. A user is associated with a _username_, the kernel does 
not manage the usernames, instead, it identifies users by simple numeric identifiers called _userids_.
Users exist primarily to support permissions and boundaries. Every user-space process has a user owner, and 
processes are said to run as the owner. 
Groups are sets of users. The primary purpose of them is to allow a user to share file access to other users in a group.


## Chapter 2: Basic Commands and Directory Hierarchy<a name="Chapter2"></a>
### The Bourne shell: /bin/sh
Every Unix system needs the Bourne shell in order to function correctly. The bash shell is the default shell on most
Linux distributions, and _/bin/sh_ is normally a link to bash on a Linux system. When you install Linux, you should 
create at least one regular user in addition to the root user.
 
### Using the Shell
#### The Shell Window
##### Standard Input and Standard Output
_cat_ outputs the content of one or more files. Unix processes use I/O streams to read and write data, the source of 
an input stream can be a file, a device, a terminal, or even the output stream from another process. Pressing 
ctrl-D on an empty line stops the current standard input entry from the terminal, ctrl-C terminates the process. 
Standard output is similar. The kernel gives each process a standard output stream where it can write its output.

### Basic commands
Some basic commands:

    * ls: lists the contents of a directory. Use -l for a detailed listing and -F to display file type information
    * cp: copies files
    * mv: moves (renames) files
    * touch: creates a file if the file doesn't exists, and if it does, it modifies the file’s modification time stamp
    * rm: deletes a file
    * echo: prints its arguments to the standard output
    
### Navigating Directories
Some essential directory commands:

    * cd: current working directory is the directory that a process is currently in. The cd command changes the 
    shell’s current working directory
    * mkdir: creates a directory
    * rmdir: removes directory
    
#### Shell Globbing (Wildcards)
The shell can match simple patterns to file and directory names, a process known as globbing (i.e. * matches any 
number of arbitrary characters). The shell matches arguments containing globs to filenames, substitutes the 
filenames for those arguments, and then runs the revised command line, this is called expansion.
The shell glob character '?' instructs the shell to match exactly one arbitrary character. If you don’t want the 
shell to expand a glob in a command, enclose the glob in single quotes.

### intermediate commands
Other essential intermediate Unix commands:

    * grep: prints the lines from a file or input stream that match an expression (prints the filename in addition 
    to the matching line). Use -i for case insensitive matches and -v for invert matches (not containing the expression)
    * egrep: similar to grep but the matches a regular expression
    * less: prints the contents of the file one screenful at a time (spacebar to go forward in the file and the b key
     to skip back one screenful and q to quit). To search forward for a word, type '/word', and to search backward, use
      '?word'
    * pwd: prints the current working directory
    * diff: prints the difference between two files
    * file: prints the file format
    * find: Run find to find file in a directory. The find command accepts special pattern-matching characters such 
    as *, but you must enclose them in single quotes ('*')to protect the special characters from the shell’s own 
    globbing feature as shell expands globs before running commands
    * locate: similar to find, but this command searches an index that the system builds periodically instead of 
    running it real time (thus is faster)
    * head: prints the start of a file, use -n to indicate the number of lines
    * tail: prints the end of a file, use -n to indicate the number of lines
    * sort: puts the lines of a text file in alphanumeric order
    
### Changing your Password and shell
Use the `passwd` command to change your password, which will ask you for the old password before allowing you to enter 
the new one. 

### dot files
Files that start with a dot are not shown by default by some programs like `ls` (unless you run it with the -a option).
Shell globs don’t match dot files unless you explicitly use a pattern such as `.*`.

### Environment and shell variables
To assign a value to a shell variable, use the equal sign (=) like in `TEST='This is a test'`, access this variable 
with a dollar sign like in `echo $TEST`. To create an environmental variable (not specific to any shell) use the 
export command like in `export TEST='This is a test'`

### The Command Path
PATH is an environmental variable that contains a list of system directories that the shell searches when trying to 
locate a command.

### Special Characters

    * * -> Regular expression, glob character
    * . -> Current directory, file/hostname delimiter
    * ! -> Negation, command history
    * | -> Command pipes
    * / -> Directory delimiter, search command
    * \ -> Literals, macros (never directories)
    * $ -> Variable denotation, end of line
    * ' -> Literal strings
    * ` -> Command substitution
    * " -> Semi-literal strings
    * ^ -> Negation, beginning of line
    * ~ -> Negation, directory shortcut
    * # -> Comments, preprocessor, substitutions
    * [ ] -> Ranges
    * { } -> Statement blocks, ranges 
    * _ -> Cheap substitute for a space

### Command-line Editing

    * ctrl-B Move the cursor left
    * ctrl-F Move the cursor right
    * ctrl-P View the previous command (or move the cursor up)
    * ctrl-N View the next command (or move the cursor down)
    * ctrl-A Move the cursor to the beginning of the line
    * ctrl-E Move the cursor to the end of the line
    * ctrl-W Erase the preceding word
    * ctrl-U Erase from cursor to beginning of line
    * ctrl-K Erase from cursor to end of line
    * ctrl-Y Paste erased text (for example, from ctrl-U)

### Text Editors
There are two standard de facto for text editors in Unix: vi (faster) and emacs (can do almost anything requires some 
extra typing to get these features).

### Getting online Help
For basic commands, the manual pages (or man pages) will tell you everything: `man ls`. To search for a manual page 
by keyword, use the -k option. Manual pages are referenced by numbered sections and you can select a manual page by 
section like in `man 5 passwd`. There is another format called info `info command`. Some packages dump their 
available documentation into _/usr/share/doc_.

### Shell Input and Output
To send the output of command to a file instead of the terminal, use the `>` redirection character. The shell  creates 
file if it does not already exist. If file exists, the shell erases (clobbers) the original file first. In  bash, set -C
to avoid clobbering. Use `>>` to avoid overwriting the file.
To send the standard output of a command to the standard input of another command, use the pipe character `|`.

#### Standard Error
The standard error (stderr); it’s an additional output stream for diagnostics and debugging. You can redirect the 
standard error using the 2> syntax (2 is the stream ID that the shell modifies), like this: `ls /fffffffff > f 2> e`.
You can also send the standard error to the same place as stdout with the >& notation like in `ls /fffffffff > f 2>&1`.

#### Standard Input Redirection
To channel a file to a program’s standard input, use the `<` operator: `head < /proc/cpuinfo`

### Understanding Error Messages
Unlike messages from other operating sys- tems, Unix errors usually tell you exactly what went wrong.

#### Anatomy of a UNIX Error Message
A UNIX error message usually consist of several parts like the command that was erroneous, the file it was acting 
upon, and the error message. When troubleshooting errors, always address the first error first.

#### Common Errors
A list of the most common error messages:

    * No such file or directory: You tried to access a file that doesn’t exist
    * File exists: This is common when you try to create a directory with the same name as a file
    * Not a directory, Is a directory: Appears when you try to use a file as a directory or a directory as a file
    * No space left on device: You’re out of disk space
    * Permission denied: you attempt to read or write to a file or directory that you’re not allowed to access
    * Operation not permitted: This usually happens when you try to kill a process that you don’t own
    * Segmentation fault, Bus error: The program tried to access a part of memory that it was not allowed to touch, 
    and the operating system killed it
    
### Listing and Manipulating Processes
For a quick listing of running processes, just run `ps` on the command line. This displays the following fields:

    * PID: The process ID
    * TTY: The terminal device where the process is running. More about this later
    * STAT: The process status(S means sleeping and R means running)
    * TIME: The total amount of time that the process has spent running instructions on the processor
    * COMMAND: This one might seem obvious, but be aware that a pro- cess can change this field from its original value

#### Command Options
The ps command has some useful options:

    * ps x: Show all of your running processes
    * ps ax: Show all processes on the system, not just the ones you own
    * ps u: Include more detailed information on processes
    * ps w: Show full command names, not just what fits on one line

To check on a specific process, add its PID to the argument list of the ps command. To inspect the current shell 
process, you could use ps u `$$`, because `$$` is a shell variable that evaluates to the current shell’s PID.

#### Killing Processes
To terminate a process, send it a signal with the kill command. When you run kill, you’re asking the kernel to send a
 signal to another process A signal is a message to a process from the kernel. There are many types of signals. The 
 default is TERM, or terminate. You can pass options, for example to freeze a process: `kill -STOP pid`. To continue 
 with the previously stopped process use CONT: `kill -CONT pid`
 
#### Job Control
Shells also support job control, using various keystrokes and commands. You can send a TSTP signal (similar to STOP) 
with ctrl-Z, then start the process again by entering fg (bring to foreground) or bg (move to background).

#### Background Processes
When you run a Unix command from the shell, you don’t get the shell prompt back until the program finishes executing.
To detach a process from the shell and put it in the background use `&`: `gunzip file.gz &`. The process will 
continue to run after you log out. If a program tries to read something from the standard input when it’s in the 
background, it can freeze. The bash shell and most full-screen interactive programs support ctRl-l to redraw the 
entire screen. 

### file modes and Permissions
Every Unix file has a set of permissions that determine whether you can read, write, or run the file. The first 
character of the mode is the file type. A dash (-) in this position, denotes a regular file and a (d), denotes a 
directory. The rest of a file’s mode contains the permissions, which break down into three sets: user, group, and other.
Some executable files have an s in the user permissions listing instead of an x. This indicates that the executable 
is setuid, meaning that when you execute the program, it runs as though the file owner is the user instead of you.

#### Modifying Permissions
To change permissions, use the chmod command. The command is like this: `chmod (target)[+-](permissions) <file>` 
where target is g for group, o for others u for user. You can do this with numbers too, for example with the number 
644 you set the following permissions user: read/write; group, other: read
You can specify a set of default permissions with the `umask` shell command, which applies a predefined set of 
permissions to any new file you create (most common is 022).

#### Symbolic Links
A symbolic link is a file that points to another file or a directory, effectively creating an alias (names that point
 to other names). Denoted by the '->' if the `ls` command is executed.
 
#### Creating Symbolic Links
To create a symbolic link from target to linkname, use `ln -s target linkname`.

### Archiving and Compressing Files
    
#### gzip
One of the current standard Unix compression programs. A file that ends with .gz is a GNU Zip archive. Gzip doesn't 
pack multiple files and directories into one file.

#### Tar
To create an archive file execute `tar cvf archive.tar file1 file2 ...`. To unpack a .tar file with tar use the x 
flag `tar xvf archive.tar`. You can list the contents of a file before untar-ing it with the 't' option. Use 'p' to 
preserve permissions.

#### Compressed Archives (.tar.gz)
To unpack a compressed archive, work from the right side to the left; get rid of the .gz first and then worry about 
the .tar (and inverse to compress).

#### zcat
The previous method isn’t the fastest or most efficient way to invoke tar on a compressed archive, and it wastes 
disk space and kernel I/O time. A better way is to combine archival and compression functions with a pipeline: 
`zcat file.tar.gz | tar xvf -`. The zcat command is the same as gunzip -dc. The -d option decom- presses and the -c 
option sends the result to standard output. The option z in tar is a shortcut for the zcat command: `tar ztvf f.tar.gz`.

#### Other Compression Utilities
There are other compression utilities like the bzip2. The bzip2 compression/decompression option for tar is j.

### Linux Directory Hierarchy Essentials
Here are the most important subdirectories in root:

    * /bin: Contains ready-to-run programs (also known as an executables), including most of the basic Unix commands
    * /dev: Contains device files
    * /etc: This core system configuration directory: the user password, boot, device, networking, and other setup files
    * /home: Holds personal directories for regular users
    * /lib: An abbreviation for library, this directory holds library files containing code that executables can use.
    There are two types of libraries: static and shared. The /lib directory should contain only shared libraries, 
    but other lib directories, such as /usr/lib, contain both varieties as well as other auxiliary files
    * /proc Provides system statistics through a browsable directory-and-file interface. The /proc directory 
    contains information about currently running processes as well as some kernel parameters
    * /sys: This directory is similar to /proc in that it provides a device and system interface
    * /sbin: The place for system executables. 
    * /tmp: A storage area for smaller, temporary files that you don’t care much about. Most distributions clear /tmp
     when the machine boots and some even remove its old files periodically
    * /usr: Contains a large directory hierarchy, including the bulk of the Linux system
    * /var: The variable subdirectory, where programs record runtime information
    
#### Other Root Subdirectories
There are a few other interesting subdirectories in the root directory:

    * /boot: Contains kernel boot loader files
    * /media: A base attachment point for removable media such as flash drives that is found in many distributions
    * /opt: This may contain additional third-party software
    
#### The /usr Directory
In addition to _/usr/bin_, _/usr/sbin_, and _/usr/lib_, _/usr_ contains the following:
    
    * /include: Holds header files used by the C compiler
    * /info: Contains GNU info manuals
    * /local: Is where administrators can install their own software
    * /man: Contains manual pages
    * /share: Contains files that should work on other kinds of Unix machines with no loss of functionality
    
#### Kernel Location
On Linux systems, the kernel is normally in _/vmlinuz_ or _/boot/vmlinuz_. A boot loader loads this file into memory and
 sets it in motion when the system boots. You’ll find many modules that the kernel can load and unload on demand 
 during the course of normal system operation (Called loadable kernel modules).
 
### Running Commands as the Superuser 
You can run the su command and enter the root password to start a root shell. This practice works, although:
    * You have no record of system-altering commands
    * You have no record of the users who performed system-altering commands
    * You don’t have access to your normal shell environment
    * You have to enter the root password

#### sudo
`sudo` allows administrators to run commands as root when they are logged in as themselves. When you run this 
command, sudo logs this action with the syslog service under the local2 facility. 

#### /etc/sudoers
To run sudo, you must configure the privileged users in your _/etc/sudoers_ file. An example of this file is below:

```text
User_Alias ADMINS = user1, user2
ADMINS ALL = NOPASSWD: ALL
root ALL=(ALL) ALL
```
The first line defines an ADMINS user alias with the two users, and the second line grants the privileges. The `ALL =
 NOPASSWD: ALL` part means that the users in the `ADMINS` alias can use sudo to execute commands as root. The second 
 `ALL` means any command. The first `ALL` means any host.
The `root ALL=(ALL) ALL` means that the superuser may also use sudo to run any command on any host. The extra `(ALL)`
 means that the superuser may also run commands as any other user. You can extend this privilege to the `ADMINS` 
 users by adding `(ALL)` to the _/etc/sudoers_ line: `ADMINS ALL = (ALL) NOPASSWD: ALL`


## Chapter 3: Devices<a name="Chapter3"></a>
### Device Files
Most devices on a Unix system because the kernel presents many of the device I/O interfaces to user processes as files. 
Some devices are also accessible to standard programs like `cat`, although not all devices or device capabilities 
are accessible with standard file I/O. If you list the contents of _/dev_, the first letter displayed would tell you 
the type of device:

    * Block device (b): Programs access data from a block device in fixed chunks (like disc devices)
    * Character device (c): Works with data streams. You can only read/write characters from/to character devices. 
    Character devices don’t have a size; when you read from or write to one, the kernel usually performs a read or 
    write operation on the device (like printers). During character device interaction, the kernel cannot back up and
    reexamine the data stream after it has passed data to a device or process
    * Pipe devices (p): like character devices, with another process at the other end of the I/O stream instead of a
    kernel driver
    * Socket device (s): special-purpose interfaces that are frequently used for interprocess communication
    
### The sysfs Device Path
The kernel assigns devices in the order in which they are found, so a device may have a different name between 
reboots. The Linux kernel offers the sysfs interface through a system of files and directories to provide a uniform 
view for attached devices based on their actual hardware attributes. The base path for devices is _/sys/devices_.
There are a few shortcuts in the _/sys_ directory. _/sys/block_ should contain all of the block devices 
available on a system. To find the sysfs location of a device in _/dev_. Use the `udevadm` command: 
`udevadm info --query=all --name=/dev/sda`

### dd and Devices
The program dd is extremely useful when working with block and character devices, its function is to read from an 
input file or stream and write to an output file or stream (dd copies data in blocks of a fixed size). Usage: 
`dd if=/dev/zero of=new_file bs=1024 count=1`

### device name summary
It can sometimes be difficult to find the name of a device. Some common Linux devices and their naming conventions are:

#### Hard Disks: /dev/sd*
These devices represent entire disks; the kernel makes separate device files, such as _/dev/sda1_ and _/dev/sda2_, for 
the partitions on a disk. The sd portion of the name stands for SCSI disk (Small Computer System Interface). To list
 the SCSI devices on your system, use a utility that walks the device paths provided by sysfs.
Most modern Linux systems use the Universally Unique Identifier (UUID) for persistent disk device access.

#### CD and DVD Drives: /dev/sr*
The /dev/sr* devices are read only, and they are used only for reading from discs.

#### PATA Hard Disks: /dev/hd*
The Linux block devices /dev/hd* are common on older versions of the Linux kernel and with older hardware.

#### Terminals: /dev/tty*, /dev/pts/*, and /dev/tty
Terminals are devices for moving characters between a user process and an I/O device, usually for text output to a 
terminal screen. Pseudoterminal devices are emulated terminals that understand the I/O features of real terminals. 
Two common terminal devices are _/dev/tty1_ (the first virtual console) and _/dev/pts/0_ (the first pseudoterminal 
device). The /dev/tty device is the controlling terminal of the current process. If a program is currently reading and 
writing to a terminal, this device is a synonym for that terminal. A process does not need to be attached to a terminal.
Linux has two primary display modes: text mode and an X Window System server (graphics mode). You can switch between 
the different virtual environments with the ctrl-alt-Function keys.

#### Parallel Ports: /dev/lp0 and /dev/lp1
Representing an interface type that has largely been replaced by USB. You can send files (such as a file to be 
printed) directly to a parallel port with the cat command.

#### Audio Devices: /dev/snd/*, /dev/dsp, /dev/audio, and More
Linux has two sets of audio devices. There are separate devices for the Advanced Linux Sound Architecture (ALSA in 
_/dev/snd_) system interface and the older Open Sound System (OSS).

#### Creating Device Files
The mknod command creates one device (deprecated). You must know the device name as well as its major and minor 
numbers. 

### udev
The Linux kernel can send notifications to a user-space process (named udevd) upon detecting a new device on the system.
The user-space process on the other end examines the new device’s characteristics, creates a device file, and then 
performs any device initialization.

#### devtmpfs
The devtmpfs filesystem was developed in response to the problem of device availability during boot. This filesystem
 is similar to the older devfs support, but it’s simplified. The kernel creates device files as necessary, but it 
 also notifies udevd that a new device is available.
 
#### udevd Operation and Configuration
The udevd daemon operates as follows:

    1. The kernel sends udevd a notification event, called a uevent, through an internal network link
    2. udevd loads all of the attributes in the uevent
    3. udevd parses its rules, and it takes actions or sets more attributes based on those rules

#### udevadm
The udevadm program is an administration tool for udevd. You can reload udevd rules and trigger events, but perhaps 
the most powerful features of udevadm are the ability to search for and explore system devices and the ability to 
monitor uevents as udevd receives them from the kernel.

#### Monitoring Devices
To monitor uevents with udevadm, use the monitor command: `udevadm monitor`

### in-depth: scsi and the linux kernel
The traditional SCSI hardware setup is a host adapter linked with a chain of devices over an SCSI bus. This adapter 
is attached to a computer, the host adapter and devices each have an SCSI ID and there can be 8 or 16 IDs per bus, 
depending on the SCSI version. The host adapter communicates with the devices through the SCSI command set in a 
peer-to-peer relationship; the devices send responses back to the host adapter. 
The SCSI subsystem and its three layers of drivers can be described as:
    
    * The top layer handles operations for a class of device
    * The middle layer moderates and routes the SCSI messages between the top and bottom layers, and keeps track of all 
    of the SCSI buses and devices attached to the system
    * The bottom layer handles hardware-specific actions. The drivers here send outgoing SCSI protocol messages to 
    specific host adapters or hardware, and they extract incoming messages from the hardware

#### USB Storage and SCSI
USB is quite similar to SCSI—it has device classes, buses, and host controllers. The Linux kernel includes a 
three-layer USB subsystem that closely resembles the SCSI subsystem, with device-class drivers at the top, a bus 
management core in the middle, and host controller drivers at the bottom.

#### SCSI and ATA
To connect the SATA-specific drivers of the kernel to the SCSI subsystem, the kernel employs a bridge driver, as 
 with the USB drives. The optical drive speaks ATAPI, a version of SCSI commands encoded in the ATA protocol. 
 
 
## Chapter 4: Disks and Filesystems<a name="Chapter4"></a>
Partitions are subdivisions of the whole disk, on Linux, they’re denoted with a number after the whole block device.
The kernel presents each partition as a block device, just as it would an entire disk. Partitions are defined on a small
 area of the disk called a partition table.
The next layer after the partition is the filesystem, the database of files and directories that you’re accustomed 
to interacting with in user space.

### Partitioning disk devices
There are many kinds of partition tables. The traditional table is the one found inside the Master Boot Record (MBR).
A newer standard is the Globally Unique Identifier Partition Table (GPT). Some linux partition tools are:

    * parted: A text-based tool that supports both MBR and GPT
    * gparted: A graphical version of parted
    * fdisk: The traditional text-based Linux disk partitioning tool. fdisk does not support GPT
    * gdisk: A version of fdisk that supports GPT but not MBR

#### Viewing a Partition Table
You can view your system’s partition table with parted -l. An MBR partition can be of type primary, extended, and 
logical. A primary partition is a normal subdivision of the disk. The basic MBR has a limit of four primary partitions 
so if you want more than four, you designate one partition as an extended partition.

#### Changing Partition Tables
Changing the partition table makes it quite difficult to recover any data on partitions that you delete because it 
changes the initial point of reference for a filesystem, yo need to ensure that no partitions on your target disk are 
currently in use. Different tools can be used to create the partitions, like _parted_, _gparted_, _gdisk_ or _fdisk_.
_fdisk_ and _parted_ modify the partitions entirely in user space.

#### Disk and Partition Geometry
The disk consists of a spinning platter on a spindle, with a head attached to a moving arm that can sweep across the 
radius of the disk,  even though you can think of a hard disk as a block device with random access to any block, 
there are serious performance consequences if you aren’t careful about how you lay out data on the disk.

#### Solid-State Disks (SSDs)
In olid-state disks (SSDs), random access is not a problem because there’s no head to sweep across a platter, but 
some considerations like _partition alignment_ (data is read in blocks of fixed size, if data is laid in two blocks 
you need to do two reads even if the amount of data to read is less than the block size).

### Filesystems
A filesystem is a form of database; it supplies the structure to transform a simple block device into the sophisticated 
hierarchy of files and subdirectories that users can understand. Filesystems are also traditionally implemented in 
the kernel, but there are also file System in User Space (FUSE) and Virtual File Systems (VFS).

#### Filesystem Types
These are the most common types of filesystems for data storage. The type names as recognized by Linux are in 
parentheses next to the filesystem names:

    * The Fourth Extended filesystem (ext4): current iteration of a line of filesystems native to Linux
    * ISO 9660 (iso9660): is a CD-ROM standard (most CD-ROMs use some variety of the ISO 9660 standard)
    * FAT filesystems (msdos, vfat, umsdos): pertain to Microsoft systems. Most modern Windows filesystems use the 
    vfat filesystem in order to get full access from Linux
    * HFS+ (hfsplus): Apple standard used on most Macintosh systems
    
#### Creating a Filesystem
To create filesystems as with partitioning, you’ll do this in user space because a user-space process can directly 
access and manipulate a block device. The _mkfs_ utility can create many kinds of filesystems:

```bash
# creates an ext4 filesystem
mkfs -t ext4 /dev/sdf2 
```
_mkfs_ is only a frontend for a series of filesystem creation programs, mkfs.fs, where fs is a filesystem type. So 
when you run mkfs -t ext4, mkfs in turn runs mkfs.ext4 located at _/sbin/mkfs.\*_

#### Mounting a Filesystem
The process of attaching a filesystem is called mounting. When the system boots, the kernel reads some configuration
 data and mounts root (/) based on the configuration data.In order to mount a filesystem, you must know the following:
    
    * The filesystem's device (where the actual filesystem data resides)
    * The filesystem type
    * The mount point—that: the place in the current system’s directory hierarchy where the filesystem will be attached
    
Example:
```bash
# The format of the command is: mount -t type device mountpoint
mount -t ext4 /dev/sdf2 /home/extra
```

#### Filesystem UUID
Device names can change because they depend on the order in which the kernel finds the devices. To solve this 
problem, you can identify and mount filesystems by their Universally Unique Identifier (UUID), a software standard. 
To view a list of devices and the corresponding filesystems and UUIDs on your system, use the _blkid_ program. 
To mount a filesystem by its UUID, use `mount UUID=<uuid> <Mount point>`.

#### Disk Buffering, Caching, and Filesystems
Unix buffers writes to the disk, when you unmount a filesystem with umount, the kernel automatically synchronizes 
with the disk. You can force the kernel to write the changes in its buffer to the disk by running the _sync_ command.

#### Filesystem Mount Options
Some important options of the mount command:

    * -t: To specify the filesystem type
    * -r: To mount the filesystem in read-only mode
    * -n: To ensure that mount does not try to update the system runtime mount database (/etc/mtab)
    * -o: To activate a filesystem option (-o <option>). Some of these options:
        - exec, noexec: Enables or disables execution of programs on the filesystem
        - suid, nosuid: Enables or disables setuid programs
        - ro Mounts the filesystem in read-only mode (as does the -r short option)
        - rw Mounts the filesystem in read-write mode
        - conv=rule (FAT-based filesystems) Converts the newline characters in files based on rule, which can be 
        binary, text, or auto.

#### Remounting a Filesystem
To reattach a currently mounted filesystem at the same mount point with different mount options do 
`mount <options> -o remount <mounting point>`

#### The /etc/fstab Filesystem Table
Linux systems keep a permanent list of filesystems and options in _/etc/fstab_. Each line describes a filesystem with 
the following fields:

    * The device or UUID
    * The mount point Indicates where to attach the filesystem
    * The filesystem type
    * Options Use long options separated by commas
    * Backup information for use by the dump command 
    * The filesystem integrity test order

#### Alternatives to /etc/fstab
Alternatives to the _/etc/fstab_ file are _/etc/fstab.d_ directory that contains individual filesystem configuration 
files and to configure _systemd_ units for the filesystems.

#### Filesystem Capacity
To view the size and utilization of your currently mounted filesystems, use the _df_ command. The output contains:

    * Filesystem: The filesystem device
    * 1024-blocks: The total capacity of the filesystem in blocks of 1024 bytes
    * Used: The number of occupied blocks
    * Available: The number of free blocks
    * Capacity: The percentage of blocks in use
    * Mounted on: The mount point
    
If your disk fills up and you need to know where the space is allocated, use the _du_ command. 

#### Checking and Repairing Filesystems
If errors exist in a mounted filesystem, data loss and system crashes may result. The tool to check a filesystem is 
_fsck_ there are different version of fsck for each filesystem type that Linux supports). To run fsck in interactive
 manual mode (-n for automatic mode), give the device or the mount point (as listed in /etc/fstab) as the argument:

```bash
fsck /dev/sdb1
```

##### Checking ext3 and ext4 Filesystems
You may wish to mount a broken ext3 or ext4 filesystem in ext2 mode because the kernel will not mount an ext3 or ext4
 filesystem with a nonempty journal. To flush the journal in an ext3 or ext4 filesystem to the regular filesystem 
 database, run e2fsck as follows `e2fsck –fy /dev/disk_device`
 
##### The worst case
The debugfs tool allows you to look through the files on a filesystem and copy them elsewhere.

#### Special-Purpose Filesystems
Most versions of Unix have filesystems that serve as system interfaces. That is, rather than serving only as a means 
to store data on a device, a filesystem can represent system information such as process IDs and kernel diagnostics. 
The special filesystem types in common use on Linux include the following:

    * proc: Mounted on /proc. Each numbered directory inside /proc is the process ID of a current process on the system. 
    The file /proc/self represents the current process
    * sysfs: Mounted on /sys
    * tmpfs Mounted on /run and other locations. tmpfs allows to use your physical memory and swap space as temporary 
    storage
    
### Swap Space
If you run out of real memory, the Linux virtual memory system can automatically move pieces of memory to and from a
 disk storage (swapping). The disk area used to store memory pages is called swap space (to view the current swap 
 memory use the _free_ command).
 
#### Using a Disk Partition as Swap Space
To use an entire disk partition as swap, make sure the partition is empty and run `mkswap dev`, where `dev` is the 
partition’s device. Last, execute `swapon dev` to register the space with the kernel.

#### Using a File as Swap Space
Use these commands to create an empty file, initialize it as swap, and add it to the swap pool:

```bash
dd if=/dev/zero of=<the swap file> bs=1024k count=<desired size in mb> 
mkswap <the swap file>
swapon <the swap file>
```

#### How Much Swap Do You Need?
Unix conventional wisdom said you should always reserve at least twice as much swap as you have real memory.

#### Inside a Traditional Filesystem
A traditional Unix filesystem has two primary components: a pool of data blocks where you can store data and a 
database system that manages the data pool. The database is centered around the _inode_ data structure. An _inode_ is a 
set of data that describes a particular file, including its type, permissions and where in the data pool the file data 
resides. _inodes_ are identified by numbers listed in an _inode_ table.

#### Viewing Inode Details
To view the _inode_ numbers for any directory, use the `ls -i` command. For more detailed _inode_ information, use the 
_stat_ command.


## Chapter 5: How the Linux Kernel Boots<a name="Chapter5"></a>
