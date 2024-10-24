# Linux Notes

By: Dharneesh Ganesan Senthilkumar

---

These are my Linux notes for my personal reference but anyone else is free to use them. These notes are based on the "Linux Luminarium" dojo from [pwn.college](http://pwn.college), so full credit goes to them. Please note that some of this information may be inaccurate, as I'm not a professional Linux user.

# Paths

The Linux file system is built like a tree, with many files in each directory. Directories can be located inside of other directories, which makes the file system look like a tree. The location of a file or directory on a file system is referred to as its path.

## Root

The base of the file system is the root directory, indicated with `/`. Under this, all the configuration files, directories, and programs are stored. In many basic CTF challenges, the flag can also be stored in the root directory.

## Absolute Paths

Files can be located with absolute paths. The absolute path is the path of the file starting from the root directory. For example, `/home/dharneesh/script.sh` is the absolute path of the script stored in my home directory. The script is stored in the `dharneesh` directory, which is stored in the `home` directory, which is finally located in the `/`, the root directory. All absolute paths eventually lead back to the root directory

## cd (Change Directory)

Since the Linux file system is full of directories, we can use the `cd` command to **c**hange **d**irectories. The syntax for using `cd` is `cd <PATH TO DIRECTORY>` .  The path to the directory can be a absolute path or a relative path(explained in the next section). For example, if I wanted to change directories from my home directory to the `/tmp` directory, I would use the command `cd /tmp` 

## Relative Paths

The relative path of a file is the location of this file in relation to your `cwd` (Current working directory). For example, if your `cwd` is `/tmp` and you want to access a file at `/tmp/a/b/file.txt` , the relative path would be `a/b/file.txt` . Another example is if my `cwd` is `/tmp/a` the relative path to `file.txt` would be `b/file.txt` .  One final example is, if my `cwd` is `/tmp/a/b/c` then the relative path to `file.txt` would be `../file.txt` .

Extra Need-to-know Information:

1. `..` refers to the parent directory. For example, if my `cwd` is `/home/dharneesh` and I wanted to go into the `/home` directory, I can use the command `cd ..` to move up one directory. In the last example about relative paths, the relative path to `file.txt` was `../file.txt` . This is because the `cwd` is `/tmp/a/b/c` and the file is located at `/tmp/a/b/file.txt` . This means we had to move up one directory with `..` to access `file.txt` .
2. `.` refers to the current directory that you are in. This `.` is used in many situations in which we have to run some sort of executable program. For example, imagine you have this binary in your directory named `runme` and you wish to run it . Default Linux binaries usually run if you just type out the name of the binary in the terminal line and click enter. BUT, if you wanted to do that with your own binary, you would have to run `./runme` . This is a safety measure in Linux that prevents users from accidentally running core system utilities just because they were trying to run their own binary.

## Home directory(~)

Every user has their own home directory, which is stored in `/home` . My home directory name would be `/home/dharneesh` since that is my username. An easier way to access the home directory is with `~` . This symbol expands to your home directory whenever you use it. For example, `cd ~` would bring me to `/home/dharneesh` and `cd ~/scripts` would bring me to `/home/dharneesh/scripts` . Note that only the leading `~` expands to the home directory. `~/~` would not expand to `/home/dharneesh/home/dharneesh` . The `cd` command will always change your directory into the home directory if given no path. 

# Basic Commands

This section goes over the basic commands of Linux.

## cat

The `cat` command con**cat**enates multiple files. It’s usually used to read the contents of a file. Ex. 

```
dharneesh@kali:~$ cat firstfile
This is the contents of the first file!
dharneesh@kali:~$ cat secondfile
This is the contents of the second file!
dharneesh@kali:~$ cat firstfile secondfile
This is the contents of the first file!
This is the contents of the second file!

```

## grep

The `grep` command is used to search for a specific string in the contents of a large file. The syntax of this command is as follows, 

```
dharneesh@kali:~$ grep SEARCH_STRING /path/to/file
```

When run, the grep command will look for the specified string in the file indicated and return every case of the string. The `grep` command can also take input from other commands via piping(piping will be explained later in the notes).

## ls

The `ls` command is used to list out all the files in a directory provided to it through an argument. `ls` will list out all the files in the current directory if not directory is specified. The syntax for `ls` is `ls [options] /path/to/directory` . The flags I use the most is `-a`n and `-l` . When I say flag I don’t mean the CTF challenge flags, I mean the flags you use along with commands to do different actions.

1. `-a` flag lists all files in a directory including the hidden files and directories. Hidden files and directories in Linux start with a `.` . For example, `file` is not hidden while `.file` is hidden.
2. `-l` flag gives more information about each file and directory when used. I usually use this option to view the file permissions of a file or the owner name. 

## touch

The `touch` command in Linux will create a file with a specific name. The syntax of this command is `touch FILE_NAME` . There is some options that you can put for this command but it’s most commonly used with no options.

## rm

This is the opposite of the `touch` command, the `rm` command. The syntax for this command is `rm [options] NAME` . The only flag I use is the `-r` flag. The `-r` flag recursively deletes everything in a directory. Because of this, using the `-r` flag will delete the specified directory

## mkdir

The `mkdir` command is used to **m**a**k**e **dir**ectories. The syntax is straightforward, you just specify the name of the directory you want to make after typing `mkdir` . For example, `mkdir new_directory` would create a new directory called `new_directory` .

## find

The `find` command recursively searches a directory for a file. The syntax of the find command is,  `find [path] [options] [criteriaforfiltering]` . If a path isn’t specified, the find command just prints out every file in your current directory. The flags I use the most are the `-iname` , `-name` , and `-type` flags.

1. `-name` searches through the directory for any files or directories matching the name provided to it. For example, `find / -name dharneesh` will return any files or directories with the name `dharneesh` 
2. `-iname` does the same thing as `-name` except the name would be case-insensitive. Using the example above, `find / -iname dharneesh` would return any files or directories named `dharneesh` or `Dharneesh` .
3. `-type` specifies the type of item to look for. `find / -type f` would find every file in the file system. There are many different arguments (which you can look up on Google) for `-type` but I use `-type f` the most. 

These flags can also be used together. For example, `find / -name dharneesh -type f` would search for every file named `dharneesh` . `find` will also produce many errors when searching for a file because a regular user does not have access to certain parts of the filesystem.

## ln

`ln` command is used to make hard and symbolic links between files in Linux. I’m not very familiar with linking files in Linux but the main takeaways that I have noted is that there are two different types of links, hard links and soft links.

1. The command to create a hard link is `ln ORIGINALFILE HARDLINKFILENAME`. Hard links are two files that contain the same data and don't have any links between the actual files. For example, if the content of `file1.txt` is, `This is the content of the first file` , and you run `ln file1.txt file2.txt` , the content of `file2.txt` will also be `This is the content of the first file` . This means that creating hard links are creating a new file with the same inode number as the original file.
2. The command to create a soft link is `ln -s ORIGINALFILE SOFTLINKFILENAME` . Soft links are files that link to the original file. Using the example from above, if you create a file with specific content and create a link to it, instead of making a new file, a new link would be created. Ex.

```
dharneesh@kali:~$ cat originalfile
This is the content of the file!
dharneesh@kali:~$ ln -s originalfile linktooriginalfile
dharneesh@kali:~$ cat linktoorginalfile
This is the content of the file!
dharneesh@kali:~$ file originalfile
originalfile: ASCII text
dharneesh@kali:~$ file linktooriginalfile
linktooriginalfile: symbolic link to original file
```

As shown above, instead of making a new file with the same data, a link to the original file is created. The `file` command lets us view what type of file a certain file is. The `linktoorginalfile` is a symbolic link instead of an ASCII text file. Accessing the symbolic link will access the data at `originalfile` .

# Digesting Documentation

Documentation in Linux can be used to learn more information about a specific command and how to use a program.

## Arguments to commands

Most of the commands in the basic commands section require arguments. An argument to a command would be something like `-a` .  For example, consider `ls -a` . `ls` is the command and `-a` is the argument to the command. Some of the arguments are also referred to as flags. There are also arguments to arguments. For example, the `find` command mentioned above has arguments to its arguments. `find / -name dharneesh` has a `-name` argument, which has a `dharneesh` argument. This is an example of complex argument usage in commands. 

## man

the `man` command is a shortened form of manual. This command let’s us view the manual page, if available, for a command. For example, if I wanted to know more about the `find` command, I would run `man find` to view the manual page for that command. You can scroll the man pages with the arrow keys and use `/` to search through it. After searching, you can use `n` to move to the next result and `N` to move to the previous result. You can also run `man man` to view more information about the `man` command. The `-K` flag for the man command lets you search for specific text among the manual pages. For example, `man -K search` would show you all the manual pages with the word search in it.

## -h

the `-h` flag can be used to display the help page for a program. For example, `find -h` will display the help page for the `find` command. The extension for `-h` is `--help` . This means that either `--help` and `-h` both work as an argument for a program. 

## Help for builtins

Builtins are commands that are built into the shell itself. You can get a list of shell builtins by running `help` with no arguments. You can get help for a specific builtin if you provided an argument to the `help` command. For example, `help cd` will print the help page for the command `cd` .

# File Globbing

Globbing lets you reference files without typing them out fully.

## *

The `*` glob is like a wildcard character. The shell will try to replace the `*` with any files that match the pattern. Ex.

```
dharneesh@kali:~$ ls
file_1 file_2 file_3 file_ab
dharneesh@kali:~$ echo The files: file_*
The files: file_1 file_2 file_3 file_ab
```

Note: `echo` command echoes back whatever input you give into it.

As seen above, the `*` glob matched every one of the files in the directory. Also take note that the `*` glob let’s you match for multiple characters, as it matched `file_ab` when only one `*` was provided.

## ?

The `?` glob is the exact same as the `*` glob except it only matches for ONE character and not multiple. Ex.

```
dharneesh@kali:~$ ls
file_a file_b file_ab
dharneesh@kali:~$ echo The files: file_?
The files: file_a file_b
dharneesh@kali:~$ echo The files: file_??
The files: file_ab
```

Take note of how the `?` only matches with one character.

## []

The `[]` glob is like the `?` glob except it is more limited. It is limited because the shell will only match any character that’s in the `[]` . Ex.

```
dharneesh@kali:~$ ls
file_a file_b file_c
dharneesh@kali:~$ echo The files: file_[ab]
The files: file_a file_b
```

Notice how the command only returned `file_a` and `file_b` because the glob specified characters a and b. 

## Exclusionary globbing

The `[]` glob can also be used to filter out files. When the first character in the brackets is a `!` ( or a `^` in newer versions of bash) the characters inside the bracket will NOT be matched. Ex.

```
dharneesh@kali:~$ ls
file_a	file_b	file_c
dharneesh@kali:~$ echo The files: file_[!ab]
The files: file_c
dharneesh@kali:~$ echo The files: file_[^ab]
The files: file_c
dharneesh@kali:~$ echo The files: file_[ab]
The files: file_a file_b
```

## Mixing globs

All the different types of globs can also be mixed and used. For example, there could be a command that says `echo The files: f??e*_[abc]` . Notice how all three globs are used in the same statement.

# Piping

Piping is when the input or output of a command is redirected. Every process in Linux has three standard channels of communication. 

1. Standard Input(`stdin`) is the channel in which a process takes input. For example, the shell used standard input to read the commands that you input.
2. Standard Output(`stdout`) is the channel in which a process outputs data. For example, most commands such as `ls` output data onto the terminal.
3. Standard Error (`stderr`) is the channel through which processes output errors. For example, when you mistype a command, the shell will output an error.

## Redirecting output

`stdout` can be redirected using the `>` character. Ex.

```
dharneesh@kali:~$ echo something > file.txt
dharneesh@kali:~$ cat file.txt
something
```

The `echo` command outputs the string `something`. This string is then redirected using `>` into `file.txt`. Because of this, when you read the contents of `file.txt`, it will return the string `something`. The output can be redirected from any command, not just the `echo` command.

## Appending output

An important thing to remember is that `>` overwrites anything in the output file. For example, if the contents of `file.txt` was `This is random content` and `echo New content > file.txt` was run, the previous content of `file.txt` will be overwritten by the new content. This is why `>>` exists. `>>` appends the output to the file instead of overwriting it. Ex.

```
dharneesh@kali:~$ cat file.txt
contents
dharneesh@kali:~$ echo more contents >> file.txt
dharneesh@kali:~$ cat file.txt
contents
more contents
```

## Redirecting errors

Just like standard output is redirected, you can also redirect the error channel of commands. A File Descriptor (FD) is a number that describes a communication channel in Linux.

FD 0: Standard input

FD 1: Standard output

FD 2: Standard error 

When you redirect process communication, you do it by FD number, though sometimes you don’t have to specify it. For example, `echo something > file.txt` and `echo something 1> file.txt` are the exact same. This is because FD number 1 implies standard output. This means you can use FD number 2 to redirect errors. For example,`$ randomcommand 2> errors.log` will redirect the errors of `randomcommand` to the file `errors.log`. It is also possible to redirect multiple file descriptors at the same time. For example, `$ randomcommand > output.log 2> errors.log` will redirect the output to `output.log` and the errors to `errors.log`.

## Redirecting input

Just like you can redirect output and errors from commands to a file, you can redirect input into a command. Ex.

```
dharneesh@kali:~$ cat file
some random text
dharneesh@kali:~$ rev < file
txet modnar emos
```

In the example above, the contents of `file` were redirected as input into the `rev` command, which reverses the text.

## Pipe Operator (`|`)

The pipe operator lets us use the output of one command as the input of another command. Ex

```
dharneesh@kali:~$ echo yes-yes | grep no
dharneesh@kali:~$ echo no-no | grep no
no-no
dharneesh@kali:~$ echo no-no | grep yes
dharneesh@kali:~$ echo yes-yes | grep yes
yes-yes
```

Notice how the output of the first command, which is `echo`, is being inputted into the next command, which is `grep`.  The `grep` command then finds the specified string from the output of the `echo` command.

## `>&` operator

The `>&` operator let’s you redirect a file descriptor to another file descriptor. This means you can combine specific channels. Ex.

```
dharneesh@kali:~$ ls randomdir otherdir > file.txt 2>&1
dharneesh@kali:~$ cat file.txt
ls: cannot access 'otherdir': No such file or directory
randomdir:
file_1
file_2
file_3
```

In this example, the error and the output are being redirected into `file.txt`. This is because we specified `stderr`(FD 2) to be merged with `stdout`(FD 1)

## tee

The `tee` command lets you see the output of a command while also redirecting it into another command. The name for the `tee` command came from the “T-splitter”, which is a plumbing pipe. The `tee` command duplicates data through the pipes to any number of files provided and `stdout`.  

```
dharneesh@kali:~$ echo hello | tee file1 file2
hello
dharneesh@kali:~$ cat file1
hello
dharneesh@kali:~$ cat file2
hello
```

In the example above, the `tee` command split the input into three different ways, one to `file1`, one to `file2`, and one to `stdout`. This command is useful because you can use it to debug commands. Imagine using `tee` between two commands to capture what’s happening.

## Writing to multiple programs with tee

`tee` is used to duplicate data to two files(`echo hello | tee file1 file2`), a file and a command(`echo hello | tee file | rev`), and two commands. Redirecting to two commands is possible through Process Substitution. Process Substitution uses the concept of how “everything is a file” in Linux(you can Google this for more information). When a command is put inside a `>(...)` or `<(...)`, it goes through process substitution. For example, if you run `echo >(rev)`, the output will be something like `/proc/self/fd/13` on zsh and `/dev/fd/63` on bash. This file seems random but it is actually a file the shell creates called a named pipe that is linked to `rev`’s input. Because of this, we can use `>` to redirect input into `>(rev)` now. For example, `echo hi > >(rev)` does the same thing as `echo hi | rev`. An example of how `tee` can be used with process substitution is `echo hello | tee >(rev)`. This will print both `hello` and `olleh` because `tee` duplicates the data to `stdout` and to the temporary file `>(rev)`.

An example of how `<(...)` can be used is `diff <(ls dir1) <(ls dir2)`. This will compare the contents of `dir1` with `dir2`.

# Shell Variables

The Linux command line is actually a complex programming language in which you can write actual programs. Because the command line is referred to as a shell, programs written in this language are referred to as shell scripts. And like most programs, the shell has its own variables.

## Printing Variables

The `echo` command just prints stuff back to the terminal. You can use this to print variables. Variables can be printed out by adding a `$` before the variable name. For example, `echo $PWD` will print out the current working directory.

## Setting Variables

To set a shell variable, you can use `=`. For example, `VAR=1111` will set the value of `VAR` to `1111`. Notice how a `$` isn’t used before the variable name. This is because `$` is only used to access variables. In shell terms, prepending a `$` triggers what is called shell expansion. Also notice how there isn’t spaces around `=`. If you use spaces, the shell won’t recognize a variable assignment and instead will try to run the `VAR` command(which doesn’t exist). After setting a variable, you can use `echo` to print it out. In order to set multi-word variables, you need to use quotes around the value you want it to set it to. For example, if you want to set `VAR` to `1111 HELLO`, you need to run `VAR="1111 HELLO"`. If you don’t put quotes around the variable, the shell will interpret `HELLO` as a command instead of a value.

## Exporting Variables

Variables you set in a shell session are local to that shell process. That means that other shells won’t be able to access them. For example, if you set a variable in bash and try accessing it in a child shell, it won’t work. The way to fix this is to use the `export` command. This command is used to ensure the variables to be passed to child processes. For example, `export VAR=1111` will export `VAR` to have the value `1111`. You can print out every exported variable using the `env` command.  You can use a colon to append to existing variables too. For example, `export PATH=$PATH:/new-directory`.

## Storing Command Output

Sometimes, you would want to set a variable to the output of a certain command. For example, imagine you wanted to set the value of `VAR` to the contents of `file.txt`. You can do this by using `$(...)`. When using this, the shell will execute the command in the enclosed area in a subshell environment and will replace the `$(...)` with the output of the command. So you can run `VAR=$(cat file.txt)` to set `VAR` to the contents of `file.txt`.

## Reading Input and Files

The `read` command lets you read input from the user and store it in a variable. For example, `read VAR` will run, which then lets you input anything to to command line. The input is then stored in `VAR`.  Ex.

```
INPUT: dharneesh@kali:~$ read VAR
INPUT: Hello
INPUT: dharneesh@kali:~$ echo $VAR
OUTPUT: Hello
```

The input and output show that I am inputting values into the terminal to store it in a variable. You can also use `read` to read files. Ex.

```
dharneesh@kali:~$ echo "hello" > file
dharneesh@kali:~$ read VAR < file
dharneesh@kali:~$ echo $VAR
hello
```

Notice how we are using the concept of redirecting input to input the contents of the file into the `read` command. 

# Processes and Jobs

Computers execute software to get stuff done and in modern computing, this software is split into two categories, operating system kernels and processes. When Linux first starts up, it launches an inti(short for initializer) process that launches more process which launch more processes until you are looking at your command line.

## Listing Processes

The command for viewing the processes in Linux is `ps`. When `ps` is run, we can see things such as the PID(Process ID), the terminal session in which the commands are running, and the total amount of CPU time the process has eaten up so far. `ps` output would look something like this:

```
dharneesh@kali:~$ ps
    PID TTY          TIME CMD
   2716 pts/2    00:00:12 zsh
  10850 pts/2    00:00:00 ps
```

The shown processes are `zsh` (The shell I’m using) and the `ps` command itself. The command by itself isn’t very useful, so we have to add a couple of flags in order for it to be useful. Since `ps` is an old utility, there are two different syntaxes.

1. **Standard Syntax:** In this syntax, you can use the `-e` argument to list every process and `-f` for a full format output. These can be combined to a single argument `-ef`
2. **BSD Syntax:** In this syntax, you can use `a` to list processes for all users, `x` to list processes that aren’t running in the terminal, and `u` for user-readable output. These can be combined to make `aux`.

These two methods, will result in similar outputs with a few differences. They both output the user, the PID, the TTY(Terminal session), the start time of the process(`STIME` for `-ef` and `START` for `aux`), and the command(`CMD` for `-ef` and `COMMAND` for `aux`). `-ef` additionally outputs the PPID(Parent Process ID) while `aux` outputs the percentage of total system CPU and Memory that the process is utilizing.

## Killing Processes

You can terminate processes in Linux using the `kill` command. `kill` will terminate a process in a way that gives it a chance to get its affairs in order before it terminates. The argument to `kill` is the PID of the process. For example, if the PID of a process was `119`, you would need to run `kill 119` to terminate it.

## Interrupting Processes

The `kill` command allows you to kill any process that is running but sometimes you want to get rid of the process that is running at the moment on your terminal. The hotkey to do this is `CTRL+C`. This will send an interrupt signal to the command that is awaiting input at the moment and usually the process cleanly exits.

## Suspending Processes

The `CTRL+C` hotkey allows for processes to exit clearly but sometimes we want the process to be running in the background. This means you can continue doing whatever you want on the terminal and you can resume the process whenever you like. The hotkey to suspend processes is `CTRL+Z` 

## Resuming Processes(`fg`)

put When a process is suspended, you need a way to resume it so it can continue running. The way to do this is by using the `fg`(foreground) command. You can run `jobs` to view the jobs that are running and run `fg JOB_NUMBER` to specify the process to resume. You can also foreground processes in the background.

## Backgrounding Processes

Sometimes you want the processes to run in the background without it being suspended. The command to do this is `bg`. This command lets you run a process in the background and gives your shell back so you can continue using it. The syntax for `bg` is the same as the syntax for `fg`. 

## Starting Background Processes

You don’t have to always hit `CTRL+Z` then run `bg` to background a process. You can start a process running in the background by appending a `&` to the command. For example, `sleep 5000 &` will run `sleep` in the background for 5000 seconds.

## Process Exit Codes

Every shell command returns a exit code when it finishes running and terminates. You can access the exit code of the most recently run command by accessing the special `?` variable. 

```
dharneesh@kali:~$ touch file
dharneesh@kali:~$ echo $?
0
dharneesh@kali:~$ touch /dir
dharneesh@kali:~$ echo $?
1
```

A `0` exit code represents a success while any non-zero number, most commonly `1`, represent a failure. The error codes can show how a command failed.

# Permissions

Each file in Linux have certain permissions to follow. These permissions can tell you if you are allowed to read, write, and execute a file. The command to show the permissions of a file is `ls -l`. The output of this command will list every file but it will give more information along with it. One of the pieces of information it gives is the permissions. An example of what the permissions would look like is `-rw-r—-r—-`.  The first character shows the file type, a `d` shows that it’s a directory and a regular `-` shows it’s a normal file. There can be other characters at this position too to represent special permissions.

The next 9 characters represent the actual permissions of the file. The permissions are `r` for read permission, `w` for write permission, and `x` for execute permission. The first three characters of the nine characters represent the permissions of the owner, the next three character represent the permissions of the group that owns the file, and the last three is for every other user. Two of the columns in `ls -l` show the user and the group that owns the file.

## Changing File Ownership

The two most important users in Linux is the user account and the root account. The root account is the admin account. Root is allowed to run and edit anything it wants. The regular user account doesn’t have all the permissions like root does. This is why when trying to do something to a file sometimes, the kernel tells us that permission is denied. The command to change file owner is `chown` and the syntax for this command is `chown [username] [file]`. The command will change the owner of the file specified to another user. For example, `chown user2 file.txt` will change the owner of `file.txt` to `user2`.

## Groups and Files

Every file has an owning user and group. A group can have multiple users in it and a user can be in multiple group.  The command to check what groups you are in is the `id` command. The command to change the group ownership of a file is `chgrp` and the syntax is the same as the `chown` command. By default, a user is mostly part of a group with their own name. For example, the `hacker` user will be part of a group called `hacker` by default. 

## Changing Permissions

As explained in the second paragraph of the Permissions sections, the first character of the permissions is the file type, the next three are for the file owner, the next three are for the group that owns the file, and the last three are for every other user.  The different permissions are `r` for read, `w` for write, and `x` for executable permissions. The command for changing permissions is `chmod`. The syntax for this command is `chmod [options] [mode] [file]`. The mode can be specified in two ways, as a modification of existing permissions or completely overwriting the old permissions.

The first format is modifying an existing mode. The syntax to do that is `WHO`+/-`WHAT` where `WHO` is the user/group/other that you want to change permissions for and the `WHAT` is the permission you want to change. For example, if you wanted to add the read permissions for the owning user, you would user `u+r`. `g+wx` adds the write and execute permissions to the group user, `o-rw` removes read and write permissions from other users. `a-rwx` removes all read, write, and execute permissions for everyone.

You can overwrite permissions using the equal symbol. For example if the previous permissions for a file was `-rwxrwxrwx` and you ran `chmod u=x` on the file, the new permissions would be `---xrwxrwx`. The previous user’s permissions were overwritten by a new permission. You can also remove all permissions with the `-` symbol. For example, `o=-` will remove all permissions set for the other users. Chaining permissions is also possible with a comma. All you have to do is separate each permission set with a comma. For example, `chmod u=rwx, o=- file.txt` will set read, write, and executable permissions for the owning user and remove all permissions for other users.

There is also the numerical method to change permissions. A permissions is 12 bits long and separated like `000 000 000 000`. The first 3 bits are for SUID, SGID, and Sticky bits. The next three are owner permissions, then group, then world permissions. Each section can be changed according to the permission you want it to be. For example, if you wanted a permission such as `-rwxr-xr-x`, it would be represented as `000 111 101 101` each bit is set to true according to the permission you want. If you want to set the permission above, you would have to run `chmod 755 file.txt`. The 755 represents the binary number in octal format. An easy way to remember is add 4 for the read permission, add 2 for the write permission, and add 1 for the executable permission. This also applies to special permissions. Add 4 for the SUID bit, add 2 for the SGID bit, and 1 for the sticky bit. So for example, if you wanted to set the SUID bit, read, write, and executable for the owner, read and write for the group, and executable for world, you would run `chmod 4761 file.txt`.

## The SUID Bit

The Set User ID(SUID) permissions bit allows a user to run the file as the owner of that file. An example of a file with the SUID bit set is `-rwsr-xr-x`. The `s` part in place of the executable bit shows that the file has the SUID bit set. It means that no matter what user runs the program(they have to have executable permissions), it will run as the owner of the file. To set the SUID bit you can use `u+s`. If used wrong, it can be a vulnerability in your system because if a user has the permissions to edit the file being run, they can make it so that they obtain a root shell. 

# Untangling Users

The two users that you use the most when you use Linux is your default user and `root`.  There are actually many more users on a typical user system. We can view these users by viewing the `/etc/passwd` file where passwords for the users used to be stored. Most of the users listed are utility accounts. A typical format of an entry in the `/etc/passwd` file would be the username, an `x` for where the password used to be, the numerical user ID, the numerical default group ID, long-form user details, the home directory, and the default shell. These are all separated by `:`.

## su

The `su` command is used to switch users to become other users. The `su` binary has the SUID bit set. Using `su` with no arguments allows us to switch user to root. Since the SUID bit is also set, the shell checks if we know the root password before it allows us to become root. When a user is specified with the `su` command, it allows us to switch user to that specific user. For example, `su newuser` switches us to the `newuser` user. It will also ask for the password of the user before switching you.

## Cracking Passwords

When a password is entered for `su` it is checked against the stored password for that user. These passwords used to be stored in `/etc/passwd` but are now stored in `/etc/shadow` since the `passwd` file is a globally readable file. 

The format of an entry in the `shadow` folder would be the username, then the password. A value of `*` or `!` in the second field means that the password login for the account is disabled and a blank field means that there is no password. If there is something for the second field, it would be a hash for the password. The third field is when the password was last changed in terms of the number of days since Jan 1, 1970. Fourth field is the number of days left before the user is allowed to change their password again(empty field and 0 mean there are no minimum password age), fifth field is maximum days the password is valid(user is forced to change password once that day is reached), sixth field is the number of days before password is to expire that user is warned that the password must be change, seventh field is the number of days after password expires that account is disabled, and the last field is the date of expiration of the account in days since Jan 1, 1970. 

When a password is inputted into `su`, it one-way-encrypts the password and compares it to the stored value to see if it matches. If you have the hash of a password, you can crack it with tools such as `hashcat` and `John the Ripper` to find the password.

## sudo

The `sudo` command allows for a command to be run as the root user. 

```
dharneesh@kali:~$ whoami
dharneesh
dharneesh@kali:~$ sudo whoami
root
```

As shown above, when the `sudo` command is run with the `whoami` command, it runs as root, and this causes the `whoami` command to output root. Unlike `su`, which relies on authenticating via password, the `sudo` command relies on other policies to determine the user’s authorization to run things as root. These are defined in `/etc/sudoers`.

# Chaining Commands

When you pipe commands, you can control the data flow. But sometimes, you want to run multiple commands in succession quickly.

## Chaining with Semicolons

The easiest way to chain commands is to use `;`. Using a semicolon eliminates the need to click enter multiple times when running commands. For example,

```
dharneesh@kali:~$ echo somerandomtext > file1.txt
dharneesh@kali:~$ cat file1.txt
somerandomtext
dharneesh@kali:~$ echo somerandomtext > file2.txt; cat file2.txt
somerandomtext
```

As you can see, the third command that was run does exactly the same thing as the first two commands that was run. No data was being transmitted between the two commands, it was just run one after the other.

## Shell Scripts

Shell scripts are just scripts in which you can write commands in so that when you run the script, it runs all of the commands. For example,

```
dharneesh@kali:~$ cat script.sh
echo hello
echo hi
dharneesh@kali:~$ bash script.sh
hello
hi
```

As you can see, the script contained two `echo` commands, and when the script was run using `bash`, the commands were run one after the other. I can also redirect the output of the script into anything I want. Using the example above, if I run `bash script.sh > output`, it will redirect the output of the script into the `output` file. You can use all the piping methods with script output. 

You also don’t need to run `bash` every time you want to run the script. When `bash` is run with a file as an argument, it reads from the script instead of `stdin` and the script is executed. Instead you can have your script be an executable and run it with `./` if it has executable permissions.

# PATH

## The PATH Variable

The shell finds commands such as `ls` and `cat` using a special shell variable called `PATH`, that stores a bunch of directory paths in which the shell will search through when a command is run. If the `PATH` variable is empty, the shell won’t be able to look for the commands that you run. This technically means your shell is broken.

## Setting PATH

`PATH` can be very useful when you create a lot of scripts. The reason for this is because if you have a folder full of scripts you use the most, you can add the path to that folder to the `PATH` variable so that you can use the bare name of the script instead of the entire path when you run it. For example,

```
dharneesh@kali:~$ ls
script1 script2 script3
dharneesh@kali:~$ PATH=/home/dharneesh
dharneesh@kali:~$ script1
This is the output of script number one.
```

As you can see, I was able to run `script1` with its bare name just like I would run a command like `ls` because I added my home directory to the `PATH` variable.