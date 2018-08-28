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
Linux distributions, and /bin/sh is normally a link to bash on a Linux system. When you install Linux, you should 
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
available documentation into '/usr/share/doc'.

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
In addition to /usr/bin, /usr/sbin, and /usr/lib, /usr contains the following:
    
    * /include: Holds header files used by the C compiler
    * /info: Contains GNU info manuals
    * /local: Is where administrators can install their own software
    * /man: Contains manual pages
    * /share: Contains files that should work on other kinds of Unix machines with no loss of functionality
    
#### Kernel Location
On Linux systems, the kernel is normally in /vmlinuz or /boot/vmlinuz. A boot loader loads this file into memory and
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
To run sudo, you must configure the privileged users in your /etc/sudoers file. An example of this file is below:

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
 users by adding `(ALL)` to the /etc/sudoers line: `ADMINS ALL = (ALL) NOPASSWD: ALL`
 
 ## Chapter 3: Devices<a name="Chapter3"></a>