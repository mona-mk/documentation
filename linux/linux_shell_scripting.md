# Linux shell scripting
[Udemy course - Linux shell scripting: a project-based approach to learning](https://www.udemy.com/share/1014QM3@-Vtd5Zu5dVsxMOpM4_cw4ar0SdV-3MDRXydWwP-G6URdN7Z9cUfUf6F13RsWhZ91ug==/)

A shell script is a text file that when you run it, it will run each line one by one like you would do on the command line, line by line copy pasting those.

* Write a shell script for things that you rarely do, so that each time you don't have to again check what you needed to do!
* Another sign you need to write a shell script, is when you see yourself writing notes of a process that needs to be done in order.

why would you choose shell over a compiled language like C?
* speed of development: you can do tasks with just couple of lines while writing same code in C will take much longer time.
* shell scripting is high level language where you don't need to be worried about bytes, data types etc.
* ease of learning it. Of course advance things may take some time.
---
### Vagrant & virtual machine

Allow you to create an entire network of linux systems quickly. The setup first includes a virtualisation software that allows you to run linux inside a virtual machine on your local system. Virtualbox is one of the open source virtualisation software. Virtualbox is a full virtualisation product meaning that allows you to run an unmodified operating system with all of its installed operating system in a special environment on top of your local operating system. This special environment is called a virtual machine and is created by the virtualisation software by intercepting access to certain hardware and features. The physical computer is the host while the virtual machine is a guest. Vitualbox provides software that can be installed on guest operating system. This software is called virtualBox guest additions which allows for instance the guest to access some folders on the host. You can manually create virtual machines from the VirtualBox UI (you first define a name and allocate some resources, and then you need to download a Linux ISO from a distribution website and tell virtualBox to use that ISO for installing OS). But to automate this process, we use a commandline tool named Vagrant (free). By default vagrant provisions virtual machines using virtualBox. But it can be configured to provision virtual machine to VMWare, AWS or other providers.

#### Vagrant terminology & syntax
- BOX : operating system image
- `vagrant box add USER/box` will download and store that box on your local system. You only need to download that once, as it will be cloned and available for vagrant
- `vagrant box add jsonc/centos7` he has a user on vagrant's public box catalogue system, he created a box specifically for this course named centos7. Many other public boxes are available to download and you can experiment with them.

- Vagrant project: folder with a vagrant file
- initialize the vagrant project. This creates vagrantfile which contains the box name and any configuration for the virtual machin/s.
```
mkdir vm1
cd vm1
vagrant init jsonc/centos7
```
- now you can bring up an instance of that box by running `vagrant up`. The first time you run this command, vagrant will import the box into the virtualbox and start it. The next time, it only starts it. The virtual machine is started in headless mode (i.e. there is no user interface).
- `vagrant up <VM-name>` by default, it brings all VMs defined in vagrantfile. But to only bring up one of the machines, just give its name.
- `vagrant ssh <VM-name>` secure shell is the network protocol used to connect to linux systems.
- `vagrant halt <VM-name>` stops/shuts down machine, and with vagrant up will brings them up again, and you won't loose any information
- `vagrant suspend <VM-name> & `vagrant resume <VM-name>` to suspend and resume
- `vagrant destroy <VM-name>` destroys and your work is lost.
- `vagrant` lists options

#### vagrantfile
All configurations happen after configure and before end. Number 2 is the configuration version. You can assign a static IP address to the virtual machine. The VM can be accessed by this IP address from the host machine or any other machines running on the same network. Each box starts with a setup defaults like whether to start in headless mode and how much memory to allocate. You can overwrite these in provider block. Vagrant has a provisioning system built in it. Provisioners in vagrant allow you to automatically install software etc as part of the vagrant up process. We will be using “shell” provisioners in this course.
```
Vagrant.configure(2) do |config|
    config.vm.box = “jsonc/centos7”
    config.vm.hostname = “serve01”
    config.network “private_network”, ip: “10.2.3.4”
    config.vm.provider “virtualbox” do |vb|
           vb.gui = true
           vb.memory = “1024”
     end
    config.vm.provision “shell”, path: “setup.sh”
end
```
```
Vagrant.configure(2) do |config|
    config.vm.box = “jsonc/centos7”
    config.vm.define “server1” do |server1|
 	 server01.vm.hostname = “server1”
	 server01.vm.network “private_network”, ip: “10.2.3.4”
     end
	 config.vm.define “server2” do |server2|
 	 server02.vm.hostname = “server2”
	 server02.vm.network “private_network”, ip: “10.2.3.5”
     end
end
```
---
To know if a command is a program on the system or is a shell builtin, you can use type command:
- `type head` -> head is /usr/bin/head -> i.e. it is a program on the system
- `type read` -> read is a shell builtin
If it is a builtin, you can use `help <command>` to view its documentation instead of using `man <command>`

---
**Single quote vs double quote**

if you want echo to print exactly what you have specified put it in single quote. If you want variables to be interpreted, use double quotes. Double quotes prevents an argument to be interpreted as multiple arguments. For instance providing an argument to a command `-c "${COMMENT}"` , if comment has spaces, it will be split at first space and assigned to -c broken.

---
**ls -l**

Permission definitions: (read, write, execute)

`-rw-r--r--`

- The first three characters rw- represent the permissions of the owner of the file (last dash is a place for x which means execute)
- The second three characters are permissions for the group of the file
- The last three characters are permissions of everyone else on the system for the file

---
**chmod**

With chmod command you can change permissions of a file. Typically, you as the owner of the file should have rwx permissions and the rest should have r-x This can be achieved by `chmod 755 <filename>`
```
r = 4
w = 2
x = 1
```
`chmod +x <filename>` is another method to make a file executable.

---
If you want the output of a command as a new variable, you use this syntax:
```
x=$(id -un)
x=`id -un`
```

To get output:
```
echo “$var”
nasty way: echo $var
```
---
**if statement**
```
if TEST-COMMAND1
then
   STATEMENTS1
elif TEST-COMMAND2
then
   STATEMENTS2
else
   STATEMENTS3
fi
```

In Bash, the test command takes one of the following syntax forms:
* test EXPRESSION
* [ EXPRESSION ]
* [[ EXPRESSION ]]
The new upgraded version of the test command [[ (double brackets) is supported on most modern systems using Bash, Zsh, and Ksh as a default shell. `man test` will help you find syntax for testing purposes. For instance for string operations `s1=s2` but for integers `i1 eq(i2)`

```
and &&
or ||
```
```
if [[ -z ${table} ]] || [[ -z ${file} ]]
then
      command
fi
```
---
**semicolon ;**

is used as command separator which equals line break. For instance, when writing in terminal, instead of line break, you can write all command in one line separating them with semicolon:
`if TEST-COMMAND1; then STATEMENTS1; else TEST-COMMAND2; fi`

---
**exit status**

if you want the user to exit the script in certain circumstances, you can use the "exit" keyword and pass a non-zero value to denote some failure in running the script to the end. By convention, "exit 0" means the script ran successfully.

---
The root user has UID of 0

---
${?} is a special variable which holds the exit status of the most recent command

---
**=**

sign in test statement does not mean assignment. Anything inside [[ statement ]] is a test statement. `==` sometimes you see a double equal sign inside the test statement. That means we are doing a pattern comparison. However, someone may use `==` for comparison as well even though it means a specific pattern comparison (but the objects are the same anyway). So, just be aware.

---
There are three default types of input and output:
* Standard input
* Standard output
* Standard error
By default, standard input comes from the keyboard, but it can also come from a pipe (a pipeline, i.e. from another command) and standard output and error are shown on display.

---
**read**

reads from standard input and assign it to a variable. The -p flag will prompt a message to user to enter the value.
`read -p 'Type something: ' VARIABLE`

---
`sudo useradd -c LOGIN (i.e. username)`

will create a user in linux system. You need to have super user privileges. `-c` is for comment to add description about who the user is for. `-m` related to home_directly creation. To login as an other user `sudo su - doughstamper`, the `-` will start the user environment with that similar to a login shell. `ps -ef` shows the processes.

---
**passwd**

is the command to change your password, and only root can change other people's password. 

`echo "${PASSWORD}" | passwd --stdin ${USER_NAME}`

`--stdin` flag reads input from standard input which can also be a pipe

`passwd -e <username>` expires the password, so that the user is forced to change the password in next login attemp.

---
Looking at `man bash`, you can search to find a command, like RANDOM. By convention, variables are in all capital letters

---
`!.`

This command will run the previous command that started with `.`. This is a shortcut to run quickly your previous command. This is a bash special parameter (you can find it under `special parameters` in `man bash`)

---
Use `date` command for printing dates in many different formats, for instance, local time now in seconds and nano seconds since epoch: `date %s%N`

---
**checksum or cryptographic hash functions**

A checksum is a small-sized block of data derived from another block of digital data for the purpose of detecting errors that may have been introduced during its transmission or storage. By themselves, checksums are often used to verify data integrity but are not relied upon to verify data authenticity.
The procedure which generates this checksum is called a checksum function or checksum algorithm. Depending on its design goals, a good checksum algorithm usually outputs a significantly different value, even for small changes made to the input. For example, if you download a file, and you what to make sure it is not corrupted in some way, you find the published checksums for the file, and compare it with the file you downloaded. `sha1sum <filename, data etc>` is a command to compute and check SHA1 message digest (i.e. SHA1 algorithm), which gives you a unique result back that represents all information in that file.

checksum is hexadecimal numbers with 0-9 representing values 0-9 and a-f representing values 10-16. 

There are other checksum programs in bash. You can get a list by: `ls -l /usr/bin/*sum`
```
-rwxr-xr-x. 1 root root 33136 Nov  5  2016 /usr/bin/cksum
-rwxr-xr-x. 1 root root 41504 Nov  5  2016 /usr/bin/md5sum
-rwxr-xr-x. 1 root root 37456 Nov  5  2016 /usr/bin/sha1sum
-rwxr-xr-x. 1 root root 41600 Nov  5  2016 /usr/bin/sha224sum
-rwxr-xr-x. 1 root root 41600 Nov  5  2016 /usr/bin/sha256sum
-rwxr-xr-x. 1 root root 41592 Nov  5  2016 /usr/bin/sha384sum
-rwxr-xr-x. 1 root root 41592 Nov  5  2016 /usr/bin/sha512sum
-rwxr-xr-x. 1 root root 37432 Nov  5  2016 /usr/bin/sum
```

---
**head**

prints the first part of a file / standard input. Following styles are all valid.
```
head -n1 <filename>
head -n 1 <filename>
head -1 <filename>    #old style
```
Also, to get the first couple of characters, use c flag which returns bytes. Noting that depending on character encoding, one character could be 1 byte, for instance in ASCII one character typically takes 8 bit=1 byte `head -c 1`


---
**shuf**

generates random permutations

---
**fold**
```
echo mona | fold -w1
m
o
n
a
```
wraps each input to fit in specified width

---
UNIX and Linux philosophy that each program should only do one thing, and it should do it very well.

---
**PATH**

is a shell variable. The search path for commands.  It is a  colon-separated  list  of  directories. A zero-length (null) directory name in the value of PATH indicates  the  current  directory.   A null  directory name may appear as two adjacent colons, or as an initial or trailing colon.  The default path is system-dependent, and is set by the  administrator who installs bash. 

Every time you type a command on command line bash tries to:
* find a function with that name and execute it.
* If it doesn't find a function with that name, it searches for that command in its list of builtin commands
* If it is not a function and not a builtin, it then searches in the list of directories in PATH variable, and runs that one.
* If no match is found, command not found will be displayed.

If you want to control the PATH, you can do so in your script by manipulating the path variable there. There is small problem with quickly adding or removing programs from your PATH. Bash uses a hash table to remember the name of the executable files. When it finds once the location of file to the executable, it will then remember it. This means bash will look for the command in the hashed location without looking at the PATH.

`export PATH=${PATH}:/user/local/mysql/bin`

---
**which**

shows the full path of shell commands. It tells us what program will be executed when you type that command on the command line.

---
**hash**

Determine and remember the full pathname of each command NAME (also explained under PATH)

`hash -r` You can make bash to forget all these hashed location (in case you change PATH and now the old one is remembered).

A hash function is used to map data to other data of fixed size. Every input has one fixed output. A cryptographic hash function is used for verification. With a cryptographic hash function you should not be able to compute the original input. A very common use case is password hashing. This allows the verification of a password without having to save the password itself. A service provider only saves a hash of a password and is not able to compute the original password. If the database of password hashes gets compromised, an attacker should not be able to compute these passwords as well. This is not the case, because there are strong and weak algorithms for password hashing. You can find more on that on this very site.

A **checksum (such as CRC32)** is to prevent accidental changes. If one byte changes, the checksum changes. The checksum is not safe to protect against malicious changes: it is pretty easy to create a file with a particular checksum.
A hash function maps some data to other data. It is often used to speed up comparisons or create a hash table. Not all hash functions are secure and the hash does not necessarily changes when the data changes. A **cryptographic hash function (such as SHA1)** is a checksum that is secure against malicious changes. It is pretty hard to create a file with a specific cryptographic hash. To make things more complicated, cryptographic hash functions are sometimes simply referred to as hash functions.

---
**Parameter vs. arguments**

Parameter is a variable that is used inside the shell script. An argument is the data passed to the shell script. So an argument passed to the shell script becomes a parameter in the shell script.

---
**positional parameters**

These are variables that contain the contents of the command line.

`${0}`

First positional parameter. Expands  to the name of the shell or shell script. However, if the command is in the PATH, then it gives the full path to that command.

`${1}`

The first argument pass, and so on.

`#`

One of the “special parameters” from “man bash”. It expands to the number of positional parameters in decimal THAT THE USER PROVIDED, i.e. ${0} is not included.

`@`

Expands to the positional parameters, starting from one.  When the expansion occurs within double quotes, each parameter expands to a separate word.  That is, "$@" is equivalent to "$1" "$2" ...  If the double-quoted expansion occurs  within a  word,  the expansion of the first parameter is joined with the beginning part of the original word, and the expansion of the last parameter is joined with the last part of the original word.  When there are no positional parameters,  "$@" and $@ expand to nothing (i.e., they are removed).

`@` refers to the parameter array, so if you call a function foo like foo "a b" c then the parameter array has two entries a b and c.

`*`

Expands to the positional parameters, starting from one.  When the expansion occurs within double quotes, it expands to  a single  word with the value of each parameter separated by the first character of the IFS special variable.  That is, "$*" is equivalent to "$1c$2c...", where c is the first character of the value of the IFS  variable.   If  IFS  is  unset,  the parameters are separated by spaces.  If IFS is null, the parameters are joined without intervening separators.

Check difference between these two:
```
for OPTION in “${*}”
do
  echo "${OPTION}"
done
```
```
for OPTION in "${@}"
do
  echo "${OPTION}"
done
```

---
**basename**

Strip directory and suffix from filename

---
**dirname**

Strips last component from filename

---
**for loop**

```
for NAME [in WORDS ... ]
do
  COMMANDS
done
```

Execute commands for each member in a list. The `for` loop executes a sequence of commands for each member in a list of items.  If `in WORDS ...;` is not present, then `in "$@"` is assumed.

---
**while**

`help while`
```
while TEST-COMMANDS
do
    COMMANDS
done
```

---
**true**

`help true` Returns exit status 0 and does nothing

---
**sleep**

delay for a specified amount of time.
```
sleep 1s
sleep 1m
sleep 1h …
```

---
**shift**

Shift positional parameters. It removes the N positional parameters.

---
**>**

redirect STDOUT **standard output to a file** (this is the difference of this method with a **pipe** where the **output comes from a command, and is given to another command**)

`head -n1 FILE > /tmp/filename`

if you don't want to overwrite and just append to it:

`>>`

---
**<**

redirect STDIN e.i. **input from a file into a command**. This is in contrast to **pipe |** where we redirect the **output of one command as input to another command** while **here we redirect a file content as input to a command**.

`read VARNAME < /tmp/data` reads data and gives the file content as input to read, and stores the file content in variable VARNAME

`echo “secret” > password && sudo passwd --stdin einestein < password`

---
**file descriptors FD**

Simply it is a number that represents an open file. For us human, it is easier to reference files by their names but for computers, number is easier. By default, every new process opens with three open file descriptors, they are:
```
FD0 - STDIN
FD1 - STDOUT
FD2 - STDERR
````

Linux represents practically everything by a file whether it is a keyboard or display or terminal etc.

---
**Explicit redirection**

So far, pipe or <> are implicit ways to redirect input and output. To be explicit, we need to use file descriptors. For example when redirecting STDIN, FD0 is assumed:

`read X 0< /tmp/data`

`echo “Mona” 1 > mona.csv`

By default, the normal output is STDOUT. Therefore, if you don't specify number, it will assume STDOUT, and it will write output to file and the error part will be echoed to screen. If you want to be specific, you have to explicitly define the FD:

`head -n1 /etc/passwd /etc/hosts /myfakefile >std.out 2>std.err`

If you want to write both stdout and stderr to the same place, you need to use & which is used when you want to use a file descriptor instead of a filename:

`head -n1 /etc/passwd /etc/hosts /myfakefile >both 2>&1` # old syntax

`head -n1 /etc/passwd /etc/hosts  /myfakefile &> both` # new syntax:

---
STDERR does not flow through the pipe. In case you need the STDERR e.g. along with STDOUT in your pipe, you need to append STDERR to STDOUT (two syntax, both correct):
```
head -n1 /etc/passwd /etc/hosts /myfakefile 2>&1 | cat -n
head -n1 /etc/passwd /etc/hosts /myfakefile |& cat -n
````

---
It is possible to do even the reverse and append STDOUT to STDERR: `echo 'error' 1>&2`. One clear example of this, is when you write an exit statement somewhere in your code, and you add a print statement just before that. That print statement is something you want to output to STDERR.

---
**null device / bit bucket (some people call it)**

is a special file that throws away anything that is written to it.
You can delete STDOUT and STDERR  by redirecting them to /dev/null. One use case is when you want to run a command in a script but you don't want the user to see the output of the command. If you want to know if a command succeeded you just need to check its exit status, you don't need to see its output.

`head -n1 /etc/passwd /etc/hosts /myfakefile 2> /dev/null`

---
**case statement**

If you are making a very big if statement to compare different options against the same variable, use case statement instead.
```
case EXPRESSION in

  PATTERN_1)
    STATEMENTS
    ;;

  PATTERN_2)
    STATEMENTS
    ;;

  PATTERN_N)
    STATEMENTS
    ;;

  *)
    STATEMENTS
    ;;
esac
```
esac is reverse of case! You can use multiple patterns separated by the | operator. The ) operator terminates a pattern list. Check “man bash” for more information on “Pattern Matching”.

`status|state|--status|--state)`

---
**function**

`function name { COMMANDS ; }` or `name () { COMMANDS ; }`

A group of commands that you call in your script using a single name. There are two syntax for it. To call a function just use its name without any parentheses unlike other programming languages.

local

Local variables can only be used within a function; they are visible only to the function where they are defined and its children. `local [option] name[=value] ...`

global

global variables can be accessed anywhere in the script including the function. A global variable defined inside a function (i.e. by omitting the “local” keyword, cannot be accessed within the script until the function is executed once.

Functions act as scrips within your script. Just note that ${0} still refers to the name of the script even if you call inside the function. However, ${1}, ${@} etc acts just as is for functions as well, i.e. they give you arguments passed to the function call.

It is a common belief that global variables are evil and you should avoid them at any cost (for instance inside functions), but be pragmatic about it, and use it when you find approperiate.

exit vs. return

exit is for the entire script, while return is an exit just for the function itself. Therefore, inside a function we do “return 1” rather than “exit 1”

---
**readonly**

is shell version of saying a variable cannot be changed, similar to `const` keyword in other programming languages.

`readonly VERBOS=true` or `VERBOSE=true; readonly VERBOSE`

---
**logger**

send message to the system's log. A shell command interface to the syslog(3) system log module
```
logger [options] [message]
logger --tag dummy.sh “This is the message sent to syslog(3)`
```

---
**getops**

Process command line options. This is a shell builtin. There is a similar executable “getopt` which does almost the same thing and is used in old scripts.

`getopts optstring name [arg]`

- OPTSTRING contains the option letters to be recognized; if a letter is followed by a colon, the option is expected to have an argument, which should be separated from it by white space.
- Each time it is invoked, getopts will place the next option in the shell variable $name, initializing name if it does not exist, and the index of the next command line argument to be processed into the shell variable OPTIND.  OPTIND is initialized to 1 each time the shell or a shell script is invoked.  When an option requires an argument, getopts places that argument into the shell variable OPTARG.
```
while getopts vl:s OPTION
do
   case ${OPTION} in
      v)
          COMMAND
       ;;
      l)
          COMMAND => ${OPTARG} since this option has : which means it has an argument
       ;;
      s)
          COMMAND
       ;;
       ?)
	   COMMAND
       ;;
   esac
done
```
You use getopts first to handle options of the script. After that you use shift and do as you used to do to handle other command line arguments `shift $(( OPTIND - 1 ))`

[getops tutorial](https://wiki.bash-hackers.org/howto/getopts_tutorial)

getopts will parse options and their possible arguments. It will stop parsing on the first non-option argument (a string that doesn't begin with a hyphen (-) that isn't an argument for any option in front of it).

There are three implementations that may be considered:
- Bash builtin getopts. This does not support long option names with the double-dash prefix. It only supports single-character options.
- BSD UNIX implementation of standalone getopt command (which is what MacOS uses). This does not support long options either.
- GNU implementation of standalone getopt. GNU getopt(3) (used by the command-line getopt(1) on Linux) supports parsing long options.

---
**arithmetic expansion**

`$(( expression ))`

Bash does not support floating point arithmetic. For instance `$((  6 / 4 ))` returns 1. Bash just drops the decimals. If you need to do arithmetic calculations you need to use other programs like `bc` (basic calculator). If you don't have it, you can install it with: `sudo yum install -y bc`

`bc -l`  if you want to do floating math with bc, you have to turn on the mathlib with -l option

`echo '6 / 4' | bc -l` bc accepts pipe

`awk 'BEGIN {print 6/4}'` You may see some scripts using awk, but it is cumbersome.

You can also use variables inside the arithmetic expression `TOTAL=$(( DICEA+DICEB ))`. You don't need $ sign for DICEA DICEB that are variables. You don't do that when using arithmetic expansion. You can assign or change the value stored in a variable. Here, you don't need a $ sign, that leading sign is used for substitution. We don't want to substitude the command for its value, we just want to change the variable.
```
NUM='1'
(( NUM++ ))
echo ${NUM}
```
This is equivalent to `NUM=$(( NUM+=1 ))`. If this is more clear for you, do this.

let

Another way to perform math operation is let bash builtin. Let pretty much does the same thing than (( ))
```
let NUM='2 + 3'
let NUM++
```

expr

(( )) is built into bash, so that is the recommended way, but expr is again another external program that you can rely on. This command processes an expression given to it, and prints the result into STDOUT
```
expr 1 + 2
NUM=$(expr 1 + 2)
```

---
**How to find a command that is not in the path?**

**locate**

locate - finds files by name. locate  reads one or more databases prepared by updatedb. Updatedb's database updates around once per day, so it is not very accurate. You can force updating it, by:
updatedb

---
**grep**

print lines matching a pattern. ^carror refers to beginning (location). $ refers to end (location)

`grep '^first,last$' example.csv`

-v

Selected lines are those not matching any of the specified patterns. `grep -v '^first,last$' example.csv`

---
**!! bang bang**

`!!` The most recently executed command.

`sudo !!` The most recently executed sudo command

---
**linux folder hirearchy**

It is good to know that configurations are typically located under /etc/ and executable also called binaries are located under bin.
Normal files that any user can run is in bin directory, while files that only administrators can run are located in sbin directory.

---
**find**
another command to look for file. Unlike `locate` it searches in realtime therefore it is slower than locate.

`find / -name userdel 2>/dev/null`

`find / -name mysql` find for all files in / directory where the word mysql is part of the filename

---
**userdel**

delete a user and related files

`ls -l /home` you can see all users

By default, normal users have home directories that live in /home/${USERNAME}. However, an application account or a system account, that lives somewhere else, for instance the root directory lives in /root, or an app /var or /www for a web app.

`/etc/logib.defs` a file that have information about min/max of UIDs for users and administrators. In general, UIDs below 1000 are related to admin accounts.

---
**tar**

tape archive! `tar` saves many files together into a single tape or disk archive, and can restore individual files from the archive.
```
tar -cf archive.tar foo bar  => create achive.tar from files foo and bar
tar -tvf archive.tar   => List all files in archive.tar verbosely
tar -xf archive.tar   => extract all files from archive.tar
```

**gzip**

what people usually do with archives is that they compress them to save some disk space. This is typically a two step process. First you archive the file and then you gzip it.
```
tar -cvf catvideos.tar catvideos/
gzip catvideos.tar   => catvideos.tar.gz
```
```
tar -zcvf catvideos.tar.gz catvideos/
you can do both steps in one command
```

`tar -ztvf catvideos.tar.gz` you can view the contents of the zip file without extracting it

**gunzip**

will unzip it

`.tar.gz.  = .tgz` same extension

NB. tar will remove the leading / from filename so that you don't accidentally overwrite your files when you unzip the tar file. For example /etc/ will be just etc/

`tar zcvf` without hyphen - is also a valid syntax from old style

---
**!$**

is again one of the event designators in bash. It will return the last argument from the previous command.

---
**chage**
```
chage -E 0 einestein  # expires an account
change -E -1 einestein # unlock and enables the account
```

`sudo passwd -l einestein`

another method to lock an account. However, the cons of this method is that it does not lock the user account from ssh login.

`sudo passwd -u einestein` # unlock the account.

`sudo usermod -s /sbin/nologin einestein`

Another method that sets the shell to something that is not a shell or somthing that simply exits when user logs in. However. again some ssh commands do not require shell like “port forwarding` therefore, use chage instead.

`ls /etc/shells`

list available shells on the system

---
**… (3 dots)**

these dots in man page refer to multiple options

`[-dar] USER [USER] …`

means multiple users can be provided

---
**#! /bin/bash -xev**

this is used for debugging

---
**cut**

remove sections from each line of files (with no FILE, or when FILE is -, read standard input). You can cut pieces of information by character position, byte position or a delimiter. This makes cut ideal for extracting columns from a csv file.
```
cut -c 7 /etc/passwd     => 7th character from each line
cut -c 4-7 /etc/passwd   => range 4-7 characters from each line
cut -c 4- /etc/passwd     => all characters starting from the 4th character
cut -c -4 /etc/passwd     => characters up to 4th
cut -c 5,3,1 /etc/passwd    => take 5th, 3rd and 1st. It won't rearrange characters (1,3,5 is the output)
cut -f        => f is field, consider it equivalent to column in a csv
echo -e 'one\ttwo\tthree' | cut -f 1
```

`grep -v '^first,last$' people.csv | cut -d ',' -f 1`

NB. cut only handles a single character delimiter files

---
**Carriage return**

A carriage return, sometimes known as a cartridge return and often shortened to CR, <CR> or return, is a control character or mechanism used to reset a device's position to the beginning of a line of text. In computing, the carriage return is one of the control characters in ASCII code, Unicode, EBCDIC, and many other codes. It commands a printer, or other output system such as the display of a system console, to move the position of the cursor to the first position on the same line. 

`echo -e "hello\r"`    => enable interpretation of backslash escapes

---
**awk**

`awk [ -F fs ] [ -v var=value ] [ 'prog' | -f progfile ] [ file ...  ]`

Awk scans each input file for lines that match any of a set of patterns specified literally in prog or in one or more files specified as -f progfile.  With each pattern there can be an associated  action  that  will  be performed  when  a  line  of a file matches the pattern.  

A pattern-action statement has the form: pattern { action }

A missing { action } means print the line; a missing pattern always matches.

The option -v followed by var=value is an  assignment to be done before prog is executed; any number of -v options may be present.  The -F fs option defines the input field separator to be the regular expression fs.

we say awk command, but it is more like a scripting language. More advance awk command requires a separate study of awk, [example youtube tutorial](https://youtu.be/FbSpuZVb164)

An input line is normally made up of fields separated by white space, or by the regular expression  FS.   The fields  are  denoted $1, $2, ..., while $0 refers to the entire line.

with awk in contrast to cut, you can change the order of items shown. awk handles very easily files which the number of spaces between columns are not uniform, because by default the field separator is space.

awk has some builtin variables (check man page)
```
NF     number of fields in the current record.
NR     number of rows (The total number of input records seen so far)
FS      field separator
OFS   output field separator (default space).
```
examples:

`awk -F 'DATA:' '{print $2, $1}' peaple.dat`

(field separator here is DATA:)  - (the comma in print statement means the default output file separator which is space, if you leave it out, the two columns will be attached)

`awk -F 'DATA:' -v OFS=',' '{print $2, $1}' peaple.dat ` 

you can directly add strings to the print action:

`awk -F 'DATA:' -v OFS=',' '{print “COL2: `$2`, “ “COL1:` $1}' peaple.dat`  (comma + space between two columns)

You can do math with awk, just surround it with paranthesis:

`awk -F 'DATA:' -v OFS=',' '{print $(NF - 1)}' peaple.dat`

```
awk 'NR<=3 {print ${0}}'
awk 'NR>=3 && NR<=5 {print ${0}}'
awk '/V/ {print ${0}}' # search for any rows that have capital V (slash refers to search)
awk '$7==5 {print ${0}}' # find rows where the 7th column is 5
seq 1 2 12 | awk '{print $1, $1^2}'
```

to print the output better use printf instead (6.3f 6 characters and 3 of those after decimal and float, and minus does the left adjustment)

`seq 1 2 12 | awk '{printf “%-4d %-4d %-6.3\nf `, $1, $1^2,$1*50/12}' | awk '{print $0, $1+$2}'`

This is the general awk command. BEGIN is everything before processing and END is everything after processing.

`awk 'BEGIN{} {print $0} END()'`

BEGIN and END only have access to first and last row while in middle the awk actions apply to all rows one by one.

`seq 1 2 13 | awk 'BEGIN{FS=“ ”, OFS=“|”;sum=0} {sum=sum+$1;print $1,sum} END{print “Last row is”, $1}'`

---
**netstat**

The netstat command symbolically displays the contents of various network-related data structures.  There are a number of output formats, depending on the options for the information presented.  The first form of the command displays a list of active sockets for each protocol.  The second form presents the contents of one of the other network data structures according to the option selected. And so on…

`netstat -nutl | grep -Ev “Active|^Proto”` (E refers to extended grep)

---
**du**

shows disk usage

`du -h` # human readable disk usage

---
**sort**

sort lines of text files

`sort -r` #reverse the order of sort

`sort -n` #sort numerical values

`sort -nu` #unique and numberical

`sort -h` #human numeric sort (G gigabytes, M megabytes etc)

---
**uniq**

uniq only works if it is first sorted, as it only compares neighboring values. 
Since sort command already has a unique option, this command is only of use if you also want to know the number of occurrences of duplicated values.

`sort -n | uniq -c`

`sudo cat /var/log/messages | awk '{print $5}' | sort | uniq -c | sort -n`

---
**wc**
Print newline, word, and byte counts for each FILE, and a total line if more than one FILE is specified.
wc does not understand language, it considers anything separated by a space to be a word.
```
-w (word count)
-c (byte count)
-l (line count)
-k (sort key)
-t (--field-separator=SEP- use SEP instead of non-blank to blank transition)
```

`grep bash /etc/passed | wc -l`

`grep -c bash /etc/passwd`    => grep already has a count option

`cat /etc/passwd | sort -t ':' -k 3 -n`

---
**sed**

works line by line.

Stream editor for filtering and transforming text.  A stream editor is used to perform basic text transformations on an input stream (a file or input from a pipeline).  While in some ways similar to an editor which permits scripted edits (such as ed), sed works by making only one pass over the  input(s),  and  is  consequently more efficient.  But it is sed's ability to filter text in a pipeline which particularly distinguishes it from other types of editors.

use cases:
* substitute some text for other text
* remove lines
* append text after given lines
* insert text before certain lines

`sed 's/search-pattern/replacement-string/flags'`    => s stands for substitute

flags:
```
i or I	case insensitive search pattern
g		global (otherwise in one life it only substitutes once)
w		requires providing a filename after the flag and sed will right only the lines that replacement 		happens to this file, although stdout of sed shows all lines.
````

`sed 's/love/like/gw like.txt' love.txt`

-i	inplace changes the file. The default is output is redirected to stdout and original file is untouched. If you want to save a copy if the file as backup, supply some text to -i and sed will append that text to the filename of its copy.

`sed -i.bak 's/my wife/sed/g' love.txt`    => this produces backup file love.txt.bak

/ forward slash

if the pattern you have has forward slash, you can escape those with backslash \ . However, there is an easier way to this by using a feature which says, you can use any character as he delimiter is sed instead of forward slash

```
echo '/home/json' | sed 's/\/home\/json/\/export\/users\/jsonc/'
echo '/home/json' | sed 's$/home/json$/export/users/jsonc$'
```

delete a line, d command
```
sed '/search-pattern/d'
sed '/This/d' love.txt
```

remove all comment # lines

`sed '/^#/d' config`

remove all empty lines

`sed '/^$/d' config`

To do multiple sed expressions, we separate them by ; or use multilple -e options
```
sed '/^$/d ; /^#/d : s/apache/http/' config
sed -e '/^$/d' -e  '/^#/d' -e 's/apache/http/' config
```

sed also allows you to provide a file containing all the sed commands you have, one command per line:
```
echo '/^$/d' > script.sed
echo '/^$/d' >> script.sed 
sed -f script.sed config
```

An address will determine what lines the sed command will be applied on.
```
sed '2 s/apache/http/' config		apply only on line 2
sed '2s/apache/http/' config		also works
sed '/Group/ s/apache/http/' config 		only applies to lines that match the pattern Group
```

You can also specify a range by specifying two addresses with a comma

`sed '1,3 s/apache/http/' config		only searches in lines 1-3`

We can even use range with patterns, giving a starting pattern for starting line and end pattern as the end line

`sed '/$User/ , /^$/ s/run/execute/' config`

---
**ping**

ping uses the ICMP protocol's mandatory ECHO_REQUEST datagram to elicit an  ICMP ECHO_RESPONSE  from  a  host  or  gateway.   ECHO_REQUEST  datagrams (pings) have an IP and ICMP header, followed by a struct timeval and then an arbitrary number of pad bytes used to fill out the packet.

-c Stop after sending count ECHO_REQUEST packets

`ping -c 3 10.9.8.12`

When attempting to resolve a name to an ip address, the /etc/hosts file is checked first by default. And if no entry is found there, DNS is queried. Therefore, you can quickly add your IP address to /etc/hosts to call those IP addresses with their names.

---
**tee**

read from standard input and write to standard output and files

-a append to the given FILEs, do not overwrite

You have to use this when you want to write with >> to a file that requires sudo permissions.

`sudo echo test >> /etc/hosts` won't work. Instead you have to use `echo '10.9.8.11 server01' | sudo tee -a /etc/hosts`

---
**ssh-keygen**
generates a key in a file (it will recommend a file to write it in). Then you need to copy the public key to the remote machine: `ssh-copy-id server01` . After this you can ssh:`ssh server01`

NB. from man ssh, ssh exists with the exit status of the remote command or with 255 if an error occured with ssh itself.

---
**uptime**

uptime - Tell how long the system has been running.

---
to run a command on a remote server via ssh: `ssh server01 command`

to run multiple command, separate them with ; and put them in quotes. If you don't do that, the first command will run in remote and the second will run in local.
```
ssh server01 'command1 ; command2'
ssh server01 “${CMD1} ; ${CMD2}”
```

---
`set -o pipefail`

to make sure all commands were successful. If  set, the return value of a pipeline is the value of the last (rightmost) command to exit with a non-zero status, or zero if all commands in the pipeline exit successfully.  This option is disabled by default.

```
ssh server01 'set -o pipefail; false | true'
echo “${?}` will be 1 instead of 0
```

NB. `ssh server01 sudo id` : ssh is run with current user which is the vagrant user and id is run with the vagrant user on the remote machine, however, we then run the command with sudo so id will be 0

`sudo ssh server01 id` ssh is run with root user and that means we are connecting with root user to server01, and therefore we need to provide the password for the root user in server01. Since we are now root user, id will return again 0.

---
**pushd**
The end result of pushd is the same as cd, but there's an additional intermediate result: pushd echos your destination directory and your point of origin. This is your directory stack, and it is what makes pushd unique. A stack, in computer terminology, refers to a collection of elements. In the context of this command, the elements are directories you have recently visited by using the pushd command. You can think of it as a history.

```
$ pwd
one
$ pushd two/three
~/one/two/three ~/one
$ pwd
three
```
```
$ pushd four
~/one/two/three/four ~/one/two/three ~/one
$ pushd five
~/one/two/three/four/five ~/one/two/three/four ~/one/two/three ~/one
```
```
Once you've built up a stack, you can use it as a collection of bookmarks or fast-travel waypoints. Two commands results the same:
$ pushd +0
~/one/two/three/four ~/one/two/three ~/one ~/one/two/three/four/five
```
```
$ dirs -v
0  ~/one/two/three/four
1  ~/one/two/three
2  ~/one
3  ~/one/two/three/four/five
```
```
$ pushd +2
~/one ~/one/two/three/four/five ~/one/two/three/four ~/one/two/three
$ pwd
~/one
```
```
You can also move backward in your stack:
$ pushd -0
~/one/two/three ~/one ~/one/two/three/four/five ~/one/two/three/four
```
```
You can add anything to stack:
$ pushd /tmp
/tmp ~/one/two/three ~/one ~/one/two/three/four/five ~/one/two/three/four
```
```
You can add to it with pushd or remove items from it with popd
$ popd +2
When used like this, popd does not change your working directory. It only manipulates your stack. But just popd will move to the directory as well:
$ popd
```

---
**-- dash dash hyphen hyphen**

More precisely, a double dash (--) is used in most Bash built-in commands and many other commands to signify the end of command options, after which only positional arguments are accepted. Example use: Let's say you want to grep a file for the string -v - normally -v will be considered the option to reverse the matching meaning (only show lines that do not match), but with -- you can grep for the string -v like this: `grep -- -v file`

---
By convention, environment variables (PAGER, EDITOR, ...) and internal shell variables (SHELL, BASH_VERSION, ...) are capitalized. All other variable names should be lower case. Remember that variable names are case-sensitive; this convention avoids accidentally overriding environmental and internal variables.
Keeping to this convention, you can rest assured that you don't need to know every environment variable used by UNIX tools or shells in order to avoid overwriting them. If it's your variable, lowercase it. If you export it, uppercase it.

---
**source vs export**

1. `source some_script.sh`, or the POSIX-compliant equivalent, `. some_script.sh`, brings variables in from other scripts, while
2. `export my_var="something"` pushes variables out to other scripts/processes which are called/started from the current script/process.

---

