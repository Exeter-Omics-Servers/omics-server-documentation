# Basic Linux

## The Linux System

Linux is a multi-user operating system (OS). That is, more than one user can
actively use the system at any given time. In order to manage the system
resources the administrator may restrict what users can do or how they should
use the system. These restrictions will be covered elsewhere, however, some of
the commands which can help the user to manage their own account and resources
will be outlined here.

## The bash Shell

A linux (or UNIX) shell is a command line interpreter which interprets users'
commands in the context of the linux OS. It is used either as an interactive
session in which the user can type commands and see the results, or it is used
to run a set of commands which have been saved in a text file (script). There
are many different types of shell and each provides the user with a slightly
different set of commands, and different scripting functionality. When you
login to a linux system and are presented with a command prompt, that prompt is
part of the login shell. Alternatively, if you login to a graphical window
manager then you are given a shell when you start a terminal window. In each
case, which shell you are given is set by the administrator of the system. This
document gives an overview of the 'bash' shell which has become a popular
standard on many current linux systems. However, the bash man page (covered
below) is the definitive source of help. Most commands which are used within a
bash shell are not actually part of bash. Rather, they are separate programs
which have been compiled to run on the linux OS being used. However, a basic
understanding of bash is a useful tool which will enable the user to get the
most out of their linux experience. Here, elements of bash will be introduced
as needed throughout this and other documents rather than being treated as a
separate topic. To illustrate commands and their output the following display
format will be used:

```
$ command
Output from command
```

The `$` is the shell prompt. The prompt which you see may be more elaborate
than this showing either your username or the current directory (see below), or
both. Here, the basic `$` prompt is used for clarity and the prompt following
any output may be omitted. Note that all commands and arguments are case
sensitive. The `ls` command, introduced below, is not the same as `Ls` or `LS`.
Although not covered further here, it is also possible to give multiple
commands at once, separated by `;`.

Commands, or options, which can be dangerous are emphasised with a lightning
bolt in the margin. The first such warning relates to the simple use of a mouse
through remote connections to bash. The mouse on a traditional Unix system,
before scroll wheels were introduced, had three distinct buttons and the X11
graphics system was written to use a middle button click to paste the contents
of the clipboard. A scroll wheel now comes as standard on many mice and this
can be pressed to go into an auto-scroll mode. However, when working through an
X interface, for example a remote shell with X forwarding, this also has the
effect of pasting the clipboard contents. Although this can be useful, the
sensitivity of the scroll wheel on many mice means that this often happens when
not intended. For example, normal text can be pasted to the command line when
the intention was simply to scroll through the text on the screen. Although
there is a good chance that what gets pasted gives an error from bash, there is
also the chance that something might get pasted which makes sense to bash and
actually carries out a valid command. If this becomes a problem or to make sure
that what is pasted is not something dangerous like `rm -r` (see below), then
there are several options. There may be ways of telling X11 not to use the
middle button, however, success on different clients (Windows or MacOS, for
example) vary and different settings might need to be tried until something is
found to work. Alternatively, you should get into the habit of emptying the
clipboard once its contents are no longer required, or, simply replace its
contents by copying something which can do less damage, like a single space.

## Help and man Pages

Most, if not all, of the standard commands used on a linux system come with
help in the form of a 'man' (manual) page. On slade there are over 2,500 man
pages for standard programs and shell commands. Add the pages for library
calls, system commands and other types of information and the figure is closer
to 14,000! However, despite the glut of information available, man pages also
have a long standing reputation for being very terse and unintelligible for the
novice user. In practice, they are a good source of information for the correct
syntax to use for a command, and for command line options which can be used.
Where they often fall short is in having a lack of usefully explained examples.
See below on how to find the correct command to use from the thousands which
are available.

When viewed, the man page is displayed using 'less', described later in this
guide. 'less' enables the user to scroll through the text one screen at a time
(use the space bar, then 'q' to quit). For bash this is just as well as its man
page has nearly 5500 lines of text! The top few lines of the man page for
`less` are:

```
$ man less
LESS(1) General Commands Manual LESS(1)
NAME
less - opposite of more
SYNOPSIS
less -?
less --help
less -V
less --version
less [-[+]aABcCdeEfFgGiIJKLmMnNqQrRsSuUVwWX~]
[-b space] [-h lines] [-j line] [-k keyfile]
[-{oO} logfile] [-p pattern] [-P prompt] [-t tag]
[-T tagsfile] [-x tab,...] [-y lines] [-[z] lines]
[-# shift] [+[+]cmd] [--] [filename]...
(See the OPTIONS section for alternate option syntax with long option
names.)
```

Each man page will have a 'SYNOPSIS' giving the syntax for the command, or a
list of alternatives, each on a separate line. Various options, or flags, will
usually be detailed further down the text. Optional arguments are given in
square brackets `[ ]`, and alternatives are listed in curly brackets `{ }` or
separated with a `|`. Here, the first two options are `-?` and `--help`.
Reading further within the OPTIONS section, it states: "This option displays a
summary of the commands accepted by less". Many commands have their own built
in 'help' option, and most are invoked with `-?`, `-h` or `--help`. If in doubt
it is often useful to try `--help` as this is unlikely to be used for anything
else, and if it hasn't been used for the command in question then you will
probably get an error along with an indication of what to try instead. In the
case of `less` the most useful information in the help, or man page, is the
section on 'COMMANDS' as this gives information on how to navigate through the
text being displayed.

What happens, though, if you do not know which command you want to look up?
Putting aside a web search, one option is to use the little known command
'apropos'. As its man page states it is used to "search the manual page names
and descriptions". Since a search can produce a lot of output, it is useful to
send this output through `less` using a 'pipe' (`|`, also described later in
this guide):

```
$ apropos viewer | less
evince (1) - GNOME document viewer
evince-previewer (1) - show a printing preview of PostScript and PDF doc...
gcm-viewer (1) - GNOME Color Manager Profile Viewer Tool
ghostscript (1) - Ghostscript (PostScript and PDF language interpre...
(END)
```

A successful search depends on how well the man page names and descriptions
have been written, and on choosing the right search term to look for. With a
bit of luck, a suitable command should be listed for what you want to do. You
can then check the man page to see if it is what you need, and to find out how
to use it. Searching the web for information on what command to use and how to
use it is always an option. However, a lot of care should be used to ensure
that the information presented is valid and does not have undesired side
effects. In this regard, peer moderated forums are probably more reliable than
obscure blog postings.

## Search Path and Other Variables

For a command to be run it has to be set as 'executable'. This is done through
permissions covered later in this guide. Then, either the full path to the
program or script has to be given, or the shell must know where to find it. For
example, commands covered later in this guide, such as `ls` and `cp` are not
programs sitting in every possible directory ready to be used. Instead, the
shell has a number of environment variables which tell bash where to find
things. Specifically for running commands is the search path variable `PATH`
which has a list of directories to look in. This can be examined by using the
bash command `echo` with the variable name preceded by a dollar `$`:

```
$ echo $PATH
/home/abc123/.local/bin:/home/abc123/bin:/usr/local/bin:/usr/bin:/usr/local
/sbin:/usr/sbin
```

Directory paths are explained later. Here, there there is a list of six
directories separated by colons (`:`) If the command you want to use is present
in more than one of these directories then the shell will use the first one it
finds. However, if the command you wish to use is not in any of these
directories then the full path to the command must be given. To avoid this for
standard software, loading software modules will add the correct paths to this
list so that the required software can be found. (See the guide on Software
Modules.) Other standard shell variables include `USER` (the logged in user),
HOME (the user's home directory) and `LS_COLORS`, a list of colours used by
`ls`. Variables can be created, modified and removed and are used a lot in
scripting. Further details of variables and their use is covered in the
Advanced bash guide.

## TAB Completion

A number of shells, including bash, incorporate a form of TAB completion. In
the bash man page this is simply termed 'completion' since the feature is
highly programmable. However, since even the default setup using `<TAB>` is
very useful, that will be described briefly here. Completion can be used as a
shortcut to typing at the command line, or as an aid to finding the command or
filename required. It can be applied to both the command being run and, in most
cases, filename arguments. It is best explained through a demonstration. For
clarity here, the command line is repeated on a separate line after each
`<TAB>` along with any further text which is typed; in practice the command
line itself would remain on a single line. A double `<TAB>` usually produces a
list of possible alternatives, with the command so far then repeated on the
following line:

```
$ ta<TAB><TAB>
tabs tail tar targetclid taskset
tac tapestat targetcli targetctl
$ tai<TAB>
$ tail -1 f<TAB>
$ tail -1 file3
-rw-r--r-- 1 abc123 users 152 Feb 19 10:17 file3
```

A single `<TAB>` will complete the command or filename up to the first
non-ambiguous point. A second `<TAB>` will then list the options available.
Typing further, followed by another `<TAB>` then completes more of the command
or filename. File names found are those in the current directory, or along any
relative or absolute path typed so far. The command names found are those
available through the search path described above. Executable scripts in the
current directory must be preceded by `./` in order to be seen as commands.

## The bash history

All commands issued in an interactive bash shell are recorded in the bash
history, up to a maximum of 1000 commands. This allows previous commands to be
recalled in several ways. Probably the most useful method is by using up and
down arrows on the keyboard to go back and forward through the shell's history.
As the history is traversed, the 'current' command is echoed to the command
line. Once a command has been recalled it can be edited at the command line,
using left and right arrows to move along the command line, before the return
key is used to issue the new (edited or repeated) command.

The history can also be viewed using the `history` command. This will echo the
full list to the screen so is best piped through `less` or `grep` (see
Redirection, below). Alternatively, giving a number, n, to the history command
will list the last n commands. Each previous command will have a number
associated with it. Any given command can be re-issued simply by typing the
number preceded by an exclamation mark (`!`). Or the exclamation mark can be
used to precede a command name or partial name:

```
$ pwd
/home/users/abc123
$ history 3
134 tail -1 file3
135 pwd
136 history 3
$ !135
pwd
/home/users/abc123
$ !ech
echo $PATH
/home/abc123/.local/bin:/home/abc123/bin:/usr/local/bin:/usr/bin:/usr/local
/sbin:/usr/sbin
```

Be careful to make sure that the command you recall is the one you intended to
run. If you issue `!r` you do not want to inadvertently run a remove `rm`
command (see below) instead of an `rsync` copy (covered in the File Transfer
guide). A description of the more sophisticated selection and editing which can
be performed is beyond the scope of even the Advanced bash guide.

## Data Storage

Each user will be setup with a home directory. That is, an area of data storage
(disk space) in which to store their own files and data. (Shared storage for
projects and groups will be covered in the guide on File and Data Management.)
In order to manage your data you should use a hierarchy of directories. How to
create directories and move around them is covered below. First, however, you
need to know how to find out where you are in that hierarchy (your current
directory), and what files and directories exist in that directory.

## Paths, Files and Directories

When a shell is opened (started) you will be in your home directory. This is
often designated with the tilde character `~`. You might see this as part of
your prompt. To find out where you are, use 'pwd' (present working directory)
and use `ls` to list any files and directories:

```
$ pwd
/home/users/abc123
$ ls
```

The `pwd` command returns a path. A path is a string used to designate the
location of a file or directory in the system. This is one or a series of
directories separated by the slash `/` character. If the first character is a
slash then the path is absolute and fixed. The initial slash (or a slash on its
own) is termed the `root` directory (not to be confused with the admin user
`root`). If there is no leading slash then the path is relative to the current
location.

There was no output from `ls` in the example above, as no files or directories
had been added. However, there are some hidden system files setup for each user
which control various aspects of the user account and login shell. Adding the
`-a` flag (option) to `ls` will show these 'dot' files, by default, in
alphabetical order:

```
$ ls -a
. .. .bash_history .bash_logout .bash_profile .bashrc
```

The files which are present may vary depending on how the system has been
setup. If your shell is a bash shell then you should have a minimum of a
.bashrc file, a file which is read in whenever a shell is opened. You will also
see the directories `.` and `..`, the current and parent directories,
respectively. Giving a file or directory path to `ls` will ask for a listing of
that file or directory. For example, the parent directory can be listed by
specifying the relative path `..`. If you want a recursive list of all files
and directories under the given path then add `-R`, but be prepared for a long
list if there are a lot of files. In contrast, using lowercase `-r` will
reverse the order of the output list:

```
$ ls ..
abc123 def456 pqr789 xyz246
$ ls -r ..
xyz246 pqr789 def456 abc123
```

To display more information about the files there are a number of different
flags which can be used with ls. The most useful is probably `-l` for a long
listing. This gives the permissions, owner, group, size (in bytes) and last
modified date and time for each file. To get file sizes in K, M or G instead of
bytes, add `h` to the flag. Permissions will be covered further below. Combined
with `a` to show the dot files, a user's initial files might look like this:

```
$ ls -la
total 25
drwxr-xr-x 3 abc123 users 78 Feb 16 10:20 .
drwxr-xr-x 28 root root 28 Jan 8 12:49 ..
-rw------- 1 abc123 users 39 Feb 18 09:27 .bash_history
-rw------- 1 abc123 users 18 Aug 30 2019 .bash_logout
-rw------- 1 abc123 users 141 Aug 30 2019 .bash_profile
-rw------- 1 abc123 users 312 Aug 30 2019 .bashrc
```

If you want a slightly more compact list without the owner and group then use
`-og` (or `-ogh`). Or, for a list with only size and file type use `-sF`. For
example, assuming that user pqr789 has some data files and directories which
you can see, `-sF` along with the relative path to their directory might give
you:

```
$ ls -sF ../pqr789
1 analyse.sh* 4 DataProc2/ 1 ReadMe
2 DataProc1/ 1 DataRaw@ 1876543 mysql_dump.sql
```

Here `/` indicates a directory, `@` a symbolic link and `*` indicates that a
file can be run (see below). Note that the units for size may depend on the
system; here they are 1K blocks. Also, the size for a directory depends on the
number of files and directories which it contains. Adding `h` gives more
'human' readable output:

```
$ ls -shF ../pqr789
512 analyse.sh* 4.0K DataProc2/ 512 ReadMe
2.0K DataProc1/ 0 DataRaw@ 1.8G mysql_dump.sql
```

Another useful option, not demonstrated here, is `-t` to order the files and
directories by last modified date. The newest files will be listed first. To
reverse the order add the `-r` option, mentioned above.

## Permissions

An example of a long listing of all of a user's initial files was given above,
along with a brief description of the columns which are output. More
specifically, the third and fourth columns give the owner of each entry and
then the group. (The second column gives the number of hard links and can
usually be ignored.) These are followed by the size, the date last modified and
the file or directory name. Extending the output, with an extra file and
directory, the listing might now look like the following:

```
$ ls -la
total 28
drwxr-xr-x 3 abc123 users 78 Feb 16 10:20 .
drwxr-xr-x 28 root root 28 Jan 8 12:49 ..
-rw------- 1 abc123 users 39 Feb 18 09:27 .bash_history
-rw------- 1 abc123 users 18 Aug 30 2019 .bash_logout
-rw------- 1 abc123 users 141 Aug 30 2019 .bash_profile
-rw------- 1 abc123 users 312 Aug 30 2019 .bashrc
drwxr-x--- 1 abc123 projK 39 Feb 17 17:23 data2
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
```

The first column is more complicated and gives the access permissions. Briefly,
depending on the type of the entry, the first letter is a `d` for a directory,
a dash `-` for a file, or an `l` for a symbolic link, described below. This
first character is followed by three sets of three characters. The three sets
indicate permissions for the file's owner ('user'), anyone in the specified
group and anyone else ('other'). The three main characters, `r`, `w` and `x`,
correspond to read, write and 'execute' permissions being allocated to each of
the three sets. If any permissions have not been granted then a dash `-` is
shown. For a file, `x` does mean 'execute' and indicates that the file is a
script or program which can be executed (run). For a directory, however, `x`
indicates that the directory can be entered with `cd`. In the above example,
any user can look at the contents of 'file2'. However, only users who are in
group 'projK' can look at the contents of directory 'data2' and `cd` into it.
No other users can view the contents of the four `.bash*` files.

The full intricacies of the permissions system is beyond the scope of this
guide. However, in order to manage shared data areas securely it is very
important that at least the basics are understood. Whereas the `ls -l` command
shows what the permissions are for a file or directory. The `chmod` command is
used to change those permissions. It is important when changing permissions to
get the details right. If the wrong change is made then it is possible to
prevent yourself from accessing the file or directory, or being able to fix
that change!

Each set of three characters detailed above can be mapped to an octal number
(the whole numbers 0 to 7). Traditionally, three octal numbers would then be
used to define the full set of three. Getting those numbers right can be a
challenge so the following alternative is recommended. For example, suppose
'other' users should not be allowed to read file2. This can be achieved by
giving `o-r` ('other minus read') as an argument to `chmod`:

```
$ ls -l file2
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
$ chmod o-r file2
$ ls -l file2
-rw-r----- 1 abc123 users 18 Feb 17 16:51 file2
```

Similarly, if users in group 'projK' should have write access to data2 then
`g+w` ('group plus write') is used. Settings can also be combined, or set
explicitly. Note here the use of the extra `d` on `ls -l` to list the entry for
directory data2 rather than its contents:

```
$ ls -ld data2
drwxr-x--- 1 abc123 projK 39 Feb 17 17:23 data2
$ chmod g+w data2
$ ls -ld data2
drwxrwx--- 1 abc123 projK 39 Feb 17 17:23 data2
```

Here different settings are applied to the group and other permissions of
file2:

```
$ ls -l file2
-rw-r----- 1 abc123 users 18 Feb 17 16:51 file2
$ chmod g+w,o+r file2
$ ls -l file2
-rw-rw-r-- 1 abc123 users 18 Feb 17 16:51 file2
```

Alternatively, the same settings can be applied to both sets of permissions:

```
$ chmod ug-w file2
$ ls -l file2
-r--r--r-- 1 abc123 users 18 Feb 17 16:51 file2
```

Or settings can be applied explicitly:

```
$ chmod u=rw file2
$ ls -l file2
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
```

Permissions can be applied recursively on a directory hierarchy using `-R`.
However, to add the ability to `cd` into directories, without adding execute
permission on files, requires the capital `X` variation of `x`:

```
$ ls -la data2/
drwxr-x--- 1 abc123 users 39 Feb 17 17:23 .
drwxr-xr-x 3 abc123 users 39 Feb 16 10:20 ..
drwxr-x--- 1 abc123 users 18 Feb 17 16:42 dir1
-rw-r----- 1 abc123 users 18 Feb 17 16:38 file1
drwxr-x--- 1 abc123 users 18 Feb 17 16:44 subdir2
$ chmod -R o=rX data2
$ ls -la data2/
drwxr-xr-x 1 abc123 users 39 Feb 17 17:23 .
drwxr-xr-x 3 abc123 users 39 Feb 16 10:20 ..
drwxr-xr-x 1 abc123 users 18 Feb 17 16:42 dir1
-rw-r--r-- 1 abc123 users 18 Feb 17 16:38 file1
drwxr-xr-x 1 abc123 users 18 Feb 17 16:44 subdir2
```

There are a number of other permissions settings which can be applied to files
and directories. These include settings which determine how files inherit
permissions from the directory they are in. A discussion of these is well
beyond the scope of this guide.

## Ownership

A normal user cannot change the owner of files. They can, however, change the
group of files (or directories) which they own, as long as they are already a
member of the new group. This is done using the `chgrp` command. The example
above showed directory data2 as having group 'projk'. This could have been
achieved using the following command:

```
$ ls -ld data2
drwxr-x--- 1 abc123 users 39 Feb 17 17:23 data2
$ chgrp projK data2
$ ls -ld data2
drwxr-x--- 1 abc123 projK 39 Feb 17 17:23 data2
```

To apply the change to all files and directories below a directory the `-R`
flag is needed. It is also prudent to use the additional flags `-h` and `-P` so
that any symbolic links have their group changed rather than the file or
directory pointed to, so the combined flag would be `-hPR`.

## Disk Usage

As well as using `ls` to look at the sizes of individual files and directories,
`du` can be used to find out how much disk space a file or whole directory
hierarchy is using:

```
$ du -s DataP*
2345678 DataProc1
56123 DataProc2
```

Here 'DataP*' has been used to ask for the size of all files or directories
with names which start with 'DataP'. The `*` is a 'wildcard' which matches any
number of any characters. See the bash man page or Advanced bash guide for more
wildcard options. The `-s` asks for a summary; without that, a size would be
given for every directory in each hierarchy! Just as for ls, `h` can be used
here to use suitable units for each entry:

```
$ du -sh DataP*
2.3G DataProc1
56M DataProc2
```

However, `du` will also take `-B` plus `K`, `M` or `G` (for example) to specify
the units required, and adding `c` will give a grand total:

```
$ du -scBM DataP*
2345M DataProc1
56M DataProc2
2401M Total
```

If you are more interested in how much space is available to store new data
then you should use `df`:

```
$ df -h .
Filesystem Size Used Avail Use% Mounted on
/dev/sdc1 123T 12T 111T 10% /home
```

Here the dot `.` means to give usage for the filesystem which the current
directory sits on. Alternatively, any absolute or relative file or directory
path could be specified. If `df` or `du` are not given a path as an argument
then details will be given for all of the standard (and pseudo) filesystems on
the linux system. This introduces the concept of a 'filesystem'. Most linux
systems will be setup with several filesystems. Each may be a different
physical disk or partition, or an area on a collection of disks, controlled by
other software. Usually it is not important to know how the different
filesystems are setup. In general, separate filesystems enable different areas
of storage to be allocate protected space, or to be setup with different
settings which might affect how data is stored. Although the details are not
important here and will not be covered further, what is important is an
understanding that different areas exist and each may be setup in a different
way.

## File and Directory Management

This section will outline a few important commands which are used to manage files and directories. Finer
details are given where these may be critical for safe or secure data management. The main points of danger
have been highlighted, however, do be aware that not every unsafe scenario can be covered here. If in any
doubt, be very, very wary. For further details not covered in any accompanying guide, the relevent man pages
will give definitive usage.

### mkdir

Directories can be created using `mkdir`. The path can be a simple name in
which case it is created as a subdirectory of the current directory:

```
$ ls -F
file1 file2
$ mkdir data1
$ ls -F
data1/ file1 file2
```

Multiple paths can be supplied to create multiple directories at once. More
extensive relative or absolute paths can also be given, and paths are created
in the order given on the command line. When being created, each path must
exist up to the last name:

```
$ mkdir data2 data2/subdir2
$ ls -F
data1/ data2/ file1 file2
$ ls -F data2
subdir2/
```

However, adding `-p` will allow a longer path to be created in one go:

```
$ mkdir -p data3/subdir3
$ ls -F
data1/ data2/ data3/ file1 file2
$ ls -F data3
subdir3/
```

Once directories have been created you can change your current working
directory using `cd`. To move to another directory simply give the destination
as an argument. This path can be relative or absolute:

```
$ cd data2
$ pwd
/home/users/def456/data2
```

If no path is given to `cd` then the destination defaults to your home directory:

```
$ cd
$ pwd
/home/users/def456
```

### mv

Files and directories can be moved using the `mv` command. This can either
change the directory in which the files or directories are found, or it can
change their name. If the source is a single file or directory and the
destination does not exist then the source will be renamed:

```
$ ls -F
data1/ data2/ data3/ file1 file2
$ mv data1 dir1
$ ls -F
data2/ dir1/ data3/ file1 file2
```

Essentially, one or a number of source files or directories can be given,
followed by a single destination. If the destination is an existing directory
then all of the sources will be moved into that destination directory:

```
$ mv *1 data2
$ ls -F
data2/ file2
$ ls -F data2
dir1/ file1 subdir2/
```

If, however, the destination is an existing file then that will be overwritten,
no questions asked! To avoid this from happening the `-n` (no-clobber) option
can be used to prevent `mv` from overwritting existing files, or the `-i`
option can be used to ask for a prompt, as demonstrated for `rm` below. If both
`-n` and `-i` are given then the last one on the command line takes precedence.

### cp

Files and directories can be copied using the `cp` command. This is similar to
the `mv` command in that it can copy both files and directories, however, the
source files or directory remain untouched. As with `mv`, if the destination is
an existing file then that will be overwritten, no questions asked! The `-n`
and `-i` options can be used as for `mv` to prevent files from being
overwritten or to ask for a prompt. Since new files are being created there are
more options to be aware of for controlling how those new files are created. By
default, the new copies will take the current date and timestamps rather than
those of the original sources. They will also take the group setting from the
user rather than the originals. Permissions, however, are taken from the
original sources. (This default behaviour can be changed, however, details are
beyond the scope of this guide).

Permissions were covered in more detail in an earlier section. However, it is
important to note here that, by default, permissions are copied from the
originals but ownership is not, so a default copy of a file can change who has
access to that file. Suppose the group of file2 has been changed to projK and
then copied:

```
$ ls -l file2
-rw-r----- 1 abc123 projK 18 Feb 17 16:51 file2
$
$ cp file2 file5
$ ls -l file2 file5
-rw-r----- 1 abc123 projK 18 Feb 17 16:51 file2
-rw-r----- 1 abc123 users 18 Feb 19 09:48 file5
```

The original file2 can be read by users in projK but not other users. After a
default copy to file5, all users In the group 'users' can now read the new
file5 and the modification time of file5 is the current date and time.To avoid
this scenario, the `-p` option should be used with `cp` to preserve group
ownership of the files when being copied. This will also keep the timestamps.

```
$ cp -p file2 file6
$ ls -l file[256]
-rw-r----- 1 abc123 projK 18 Feb 17 16:51 file2
-rw-r----- 1 abc123 users 18 Feb 19 09:48 file5
-rw-r----- 1 abc123 projK 18 Feb 17 16:51 file6
```

Now, after copying with `-p` to give file6, the new file6 retains the original
modification time, and group, so now only users in projK can read the new file,
as is the case for the original file2. (Use the `--preserve` flag if only
certain attributes should be preserved, as detailed in the man page). When
copying a directory it does not make sense to copy it without its contents.
However, this is not done automatically. To recursively copy a directory,
include the `-r` option. (In this case, `-R` can also be used.) As with copying
files, it is recommended to also include `-p` in order to preserve ownership
and permissions.

### rm

The `rm` command is used to remove files. Be very careful with this, especially
if using wildcards (covered in more detail in the Advanced bash guide). By
default there are no checks and you could easily remove the wrong file or files
without a second chance to say no!

```
$ ls -F
data2/ data3/ file2 file5 file6
$ rm file5
$ ls -F
data2/ data3/ file2 file6
```

To be safe, the `-i` option can be used to ask for a prompt before each file:

```
$ rm -i file6
rm: remove regular file 'file6'? Y
$ ls -F
data2/ data3/ file2
```

By default, `rm` does not remove directories. An empty directory can be removed
using `rmdir`. To remove a directory plus all of its contents (no questions
asked!) `rm` can be used by adding the recursive option, `-r`. As with `cp`
both `-r` and `-R` can be used.

```
$ rm -r data3
$ ls -F
data2/ file2
```

Extreme caution should be used when removing directories to ensure that the
correct directory is removed, especially if you are in the habit of working in
different directories or projects with similar subdirectory names. Get into the
habit of either specifying the full path to a directory which you wish to
remove, or Double checking where you are using 'pwd' before using 'rm'. It is
not unheard of for a user to lose months or even years of work by using 'rm' in
the wrong directory.

Again, for safety, the `-i` option can be used with `-r`. However, this can be
tedious if removing a lot of files. The `-I` option (capital `i`) will prompt
once if removing more than three files. However, it will not prompt at all if
there are three or less. Note that on some systems an `alias` is used so that
`-i` is automatically included For any `rm`. However, having this safeguard in
place and getting used to relying on it can then be dangerous when using a
system where it is not in place. (Using aliases in bash is covered 
[on this page](./intermediate_scripting.md)).

###  ln

One type of file which has not been covered yet is the 'symbolic link'. This is
also known as a 'soft' link to Distinguish it from a 'hard' link which is
rarely used and not covered here. (In this guide all links referred to are
symbolic links, unless explictly stated otherwise.)

A link can be thought of as a pointer to another file or directory either in
the same directory, or somewhere else on the system. The path pointed to can be
either a file or directory relative to the current directory, or it can be an
absolute path. As such, an important use for symbolic links is sharing data. A
user can have a link in their own area which points to shared project data
elsewhere on the system. Reference to the shared data Can then be made using
the link rather than giving the full path to the data each time its required. A
symbolic link is created using `ln -s`. As when copying a single file or
directory, the first path given is the existing path and the second is the name
for the new link:

```
$ ln -s file2 file5
$ ls -l file2 file5
-rw-r----- 1 abc123 projK 18 Feb 17 16:51 file2
lrwxrwxrwx 1 abc123 users 5 Feb 19 09:48 file5 -> file2
```

If the first path points to a file or directory in another directory then the
second path can be omitted. In this case, the name of the link will be the same
as the file or directory being pointed to.

```
$ ln -s data2/file1
$ ls -l file1
lrwxrwxrwx 1 abc123 users 5 Feb 19 09:51 file1 -> data2/file1
```

The effective access permissions are those of the file pointed to, not those of
the link itself. However, using the link as an argument to a command behaves as
if the name of the file pointed to had been given. One key exception is for the
`rm` command, unless the link is to a directory (see below):

```
$ rm file1
$ ls -l file1 file2
ls: cannot access 'file1': No such file or directory
-rw-r----- 1 abc123 projK 18 Feb 17 16:51 file2
To confirm that it is indeed the link which is removed, the '-i' option can be given:
$ rm -i file5
rm: remove symbolic link 'file5'? Y
$ ls -l file2 file5
ls: cannot access 'file5': No such file or directory
-rw-r----- 1 abc123 projK 18 Feb 17 16:51 file2
```

Also, if `ls` is given a symbolic link to a directory as an explicit argument,
then it behaves slightly differently depending on which options it has been
given. Consider the link data4 which points to directory data2:

```
$ ln -s data2 data4
```

A short listing for `ls` (without `-l`) lists the contents of the directory
being pointed to regardless of whether a trailing slash is present or not:

```
$ ls -F data2 data4
data2:
dir1/ file1 subdir2/
data4:
dir1/ file1 subdir2/
```

In contrast, `ls -l` lists details for the link itself if the link name is
given without any trailing slash:

```
$ ls -l data4
lrwxrwxrwx 1 abc123 users 5 Feb 19 10:16 data4 -> data2
```

A trailing slash must be added to the link name for `ls -l` to follow the link
and lists the contents of the directory being pointed to:

```
$ ls -l data4/
drwxr-xr-x 1 abc123 users 18 Feb 17 16:42 dir1
-rw-r--r-- 1 abc123 users 18 Feb 17 16:38 file1
drwxr-xr-x 1 abc123 users 18 Feb 17 16:44 subdir2
```

Similarly, if `rm` is given a symbolic link to a directory then it will act on
the link if there is no trailing slash, or the directory pointed to if there is
a trailing slash.

## Redirecting or Piping Output

Sometimes the output of a command needs to be saved in a log file. Although
some commands allow the user to specify this using a command line option or
argument, many commands simply send their output to the terminal. Sending this
output to a file is termed redirection. Alternatively, it may be useful to send
the output of one command directly to another command as input. This is termed
piping. In order to capture the output from a command, either using redirection
or a pipe, it is first necessary to understand that there are two types of
output: standard output and standard error. By analogy, if a command accepts
input directly from either the keyboard or from another command (using a pipe)
then that is termed the standard input.

## Redirection

The simplest way of capturing output into a file is by redirection using the
`>` operator:

```
$ ls -l > file3
$ cat file3
total 2
drwxrwx--- 1 abc123 projK 39 Feb 17 17:23 data2
lrwxrwxrwx 1 abc123 users 5 Feb 19 10:16 data4 -> data2
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
-rw-r--r-- 1 abc123 users 0 Feb 19 11:14 file3
```

Here `cat` is used to list the file contents. The list includes file3 which is
created before `ls` is run. Note that, if the output file already exists then
it will be overwritten, no questions asked! This may be what is required,
however, a safer option might be to use `>>` which will append to the end of an
existing file. Or, if the file does not exist then it will be created:

```
$ ls -l >> file3
```

file3 now contains two directory listings from ls:

```
$ cat file3
total 2
drwxrwx--- 1 abc123 projK 39 Feb 17 17:23 data2
lrwxrwxrwx 1 abc123 users 5 Feb 19 10:16 data4 -> data2
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
-rw-r--r-- 1 abc123 users 0 Feb 19 11:14 file3
total 2
drwxrwx--- 1 abc123 projK 39 Feb 17 17:23 data2
lrwxrwxrwx 1 abc123 users 5 Feb 19 10:16 data4 -> data2
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
-rw-r--r-- 1 abc123 users 209 Feb 19 11:17 file3
```

If the command outputs error messages then these usually go to the standard
error rather than to standard output. By default, the redirection operators `>`
and `>>` only capture standard output. To also capture the standard error, a
slightly more complicated version is required. First, using the standard
redirection `>` with a command which gives an error, and then the alternative
`&>`:

```
$ ls -l file2 file4 > file3
ls: cannot access 'file4': No such file or directory
$ cat file3
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
$ ls -l file2 file4 &> file3
$ cat file3
ls: cannot access 'file4': No such file or directory
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
```

Similarly, to append both standard output and standard error to a file use
`&>>`.

## Piping

Instead of sending output to a file with redirection it is possible to send
output to the input of another command. This is frequently used to filter the
output of one command to give a more compact output. In the following example
the `awk` command is used to pick out the first and last columns from output
from `ls`:

```
$ ls -l file? | awk '{print $1, $9}'
-rw-r--r-- file2
-rw-r--r-- file3
```

The `grep` command can be used to select lines which have matching text. Here
it is used to select the larger directory hierarchy from the `du` example given
in the earlier section:

```
$ du -sh DataP* | grep "G"
2.3G DataProc1
```

(See the Advanced Linux guide for using `awk` and `grep` for filtering and
processing output and file content.) Alternatively, the output from the summary
`du` command, also covered in the earlier section, can be piped into `tail -1`
(digit 1, see below) so that only the last summary line is displayed:

```
$ du -sch file? | tail -1
10K Total
```

Again, standard error can be redirected so that it is also piped. First,
without the redirection and then with:

```
$ du -sch file2 file3 file4 | tail -1
du: cannot access 'file4': No such file or directory
10K Total
$ du -sch file2 file3 file4 2>&1 | tail -1
10K Total
```

Note that when redirecting to a file, the ampersand `&` came before the
redirection operators `>` and `>>`; here it must come after. Also, the numbers
indicate what is being redirected, in this case `2` (standard error) is being
redirected to the same as `1` (standard output). If the numbers were reversed
then standard output would continue to go to the terminal, along with standard
error, and nothing would go through the pipe.

## Viewing or Editing Files

Full details of different ways of viewing or editing files are given in other
guides, and only a brief overview is given here.

The `cat` command was introduced earlier to list the contents of a file to the
terminal. More than one file can be listed (concatenated) by giving more files
as arguments. To only view the first, or last, section of a file the commands
`head`, or `tail`, can be used with the number of lines required being
specified, as shown in the example given above for piping. See the relevant man
pages for other useful arguments. For an overview of other more advanced
filtering and processing techniques, see the Advanced Linux guide.

For viewing longer output it is useful to see a single 'page' at a time rather
than have the whole file contents listed at once. The traditional command for
doing this is `more`. However, this has its limitations and much greater
functionality is available from the aptly named command `less`. Both of these
commands put you in an interactive session for viewing the contents of the
file. To advance to the next page use the space bar, or 'f' (forward), or for
half a page use 'd' (down). To scroll one line at a time use the return key. To
quit the interactive session type 'q'. With `more` you will see the output you
have viewed; `less`, however, leaves you at the command line prompt without any
extra output showing in the terminal. Another advantage of `less` over `more`
is the ability to go back through a file's contents (type 'u' or 'b', for up or
back) rather than being restricted to only going forwards. It is also possible
to go to specific lines, for example, '1G' goes to line 1 and 'G' with no
number goes to the end. See the man page for more advanced navigation
techniques. Also, both `less` and `more` will take any number of filenames to
view, one after the other with a prompt between. Although `less` can be used
for viewing existing files, it is often used with a pipe, as shown above, to
provide a paging facility for long output. It is also useful if you do not want
the output left showing in the terminal:

```
$ ls -l file? | less
-rw-r--r-- 1 abc123 users 18 Feb 17 16:51 file2
-rw-r--r-- 1 abc123 users 101 Feb 19 11:20 file3
(END)
q
```

Which will then appear as the following after quitting less:

```
$ ls -l file? | less
```

The traditional linux editor is `vi`. On many systems, running `vi` will often
run the similar ("Vi IMproved") editor `vim`. A read-only option for `vi` can
be invoked using `view`, or add `-R` to `vim`. Both of these editors take
commands from the keyboard to navigate and make changes to the file contents.
For a more graphical editing experience, two popular editors are `gedit` and
`nano`. (Both require 'X forwarding' described in the guide on Server Access.)

## Background Jobs

Some programs can run without input from or output to the terminal. If the
program is likely to take some time to run then it can be run in the background
by adding `&`, thus freeing up the terminal for other use. Running commands in
the background is also usually done for graphical interfaces such as `rstudio`
or `nano`.

Assuming that the required software modules have been loaded (see the guide on
Software) then `rstudio` can be run as follows:

```
$ rstudio &
[1] 321567
```

A list of current background jobs can be seen using the `jobs` command:

```
$ jobs
[1]+ Running rstudio &
```

A running job can also be switched from background to foreground using `fg`:

```
$ fg
rstudio
```

The special control sequence Ctrl-Z (`^Z`) can be used to stop (suspend, not kill) the foreground job:

```
^Z
[1]+ Stopped rstudio
$ jobs
[1]+ Stopped rstudio
A stopped job can be put in the background using 'bg':
$ bg
[1]+ rstudio &
$ jobs
[1]+ Running rstudio &
```

If more than one job is in the background, or stopped, then the relevent job
needs to be selected using the job number. This number is the number in square
brackets in the jobs list. For example, if there are two jobs stopped then
number 2 can be put in the background using `bg %2`. When a background program
is closed the ending of the job is reported to the terminal next time the
prompt is refreshed:

```
[1]+ Done rstudio
```

Note that if jobs are running in the background when you log out of a shell
then those jobs will usually be killed. However, some programs have other
processes attached to them which do not automatically die when the main program
is killed. For example, `rstudio` has an associated 'rsession' process running
which does not get killed when rstudio is killed. If you use rstudio, then it
should be properly closed down before you log out of the shell used to start
it. If the shell is terminated due to a network glitch, or if rstudio was not
shut down properly, then the rsession process will continue to run and use up
resources. See the following section on managing processes.

## System Resources

Linux is a multi user OS. That means that, as well as sharing storage space,
users' programs must share resources such as memory and cpu when they run. A
number of commands can be used to find out what system resources are being
used, and by what. However, some output is easier to interpret than others! In
practice, actual resource usage is only informative if the limits of the system
are also known. For example, knowing that a directory has 200GB of data has a
different meaning if the data sits on a 238GB SSD drive in a laptop compared
with a 15TB partition in a storage array. The commands `ls`, `du` and `df` have
already been covered. In terms of resource limits, 'df' is the only one of
these to indicate space available in addition to space used.

### ps

Programs which run on the system are classified as processes. There are many
system processes which run in the background which most users are oblivious to,
and, typically, the number will run to several thousand. Even standard programs
which are run by a user can run many other processes as part of their normal
behaviour. The `ps` command is used to see the processes which are running.
Note that the options for `ps` can be confusing since there are two styles for
some of the options: BSD-style such as `u` and standard linux style such as
`-e`. Some of these cannot be mixed whereas others can. Further, the option `u`
(without dash) is not the same as the option `-u` (with dash).

By default, if the user has nothing else running, `ps` will list the 'ps'
process itself and the shell running it:

```
$ ps
PID TTY TIME CMD
1438754 pts/13 00:00:01 bash
2389456 pts/13 00:00:00 ps
```

Useful usage information can be obtained by adding the option `u`. However,
more processes are listed which correspond to other shells currently being run
by the same user:

```
$ ps u
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
abc123 1438754 0.0 0.0 26068 6036 pts/13 Ss 08:56 0:01 -bash
abc123 1548745 0.0 0.0 24832 5600 pts/6 Ss+ 09:23 0:00 -bash
abc123 2389456 0.0 0.0 57820 4008 pts/13 R+ 14:24 0:00 ps u
abc123 2491623 0.0 0.0 54608 8548 pts/6 S+ 10:32 0:00 mysql -u abc123 -p
```

Adding an `x` to give `ps ux` will also show background processes which are run
as part of the user's login and graphical environment. Specifics of the columns
`VSZ` and `RSS` (which refer to memory usage) are beyond the scope of this
guide. More useful are the `%CPU` and `%MEM` columns which indicate CPU and
memory usage as a percentage of the system resources.

The command `top` will give a dynamic view of the `top` processes currently
running. By default, the display will update roughly every three seconds until
told to stop (`q`) and will display processes ordered by CPU usage. The number
of processes shown is chosen to fill the terminal once the header lines have
been shown. For further details on options for customizing the display see the
man page. Be warned, however, that this currently stretches to nearly 2000
lines.

The header which `top` displays includes the overall CPU and memory usage plus
the number of processes running and the output from 'uptime'. The command
'uptime' can be used on its own and gives a one line display of the current
time, how long the system has been running since the last reboot, how many
users are currently logged on, and the system load averages for the past 1, 5,
and 15 minutes. Whether this load is stretching the system resources or not
depends on the CPU capacity of the system. The number of processor cores
available on the system can be shown using `nproc`. Memory usage can also be
shown using `free -m` or `free -h`:

```
$ uptime
14:38:58 up 125 days, 2:50, 7 users, load average: 2.23, 4.48, 5.80
$ nproc
192
$ free -h
total used free shared buff/cache available
Mem: 1.5Ti 932Gi 145Gi 4.1Gi 433Gi 566Gi
Swap: 4.0Gi 94Mi 3.9Gi
```

So for this system, the load averages are currently only 1-3% of capacity.
Memory usage is much trickier to assess. Although the 'total', 'used' and
'available' figures shown above suggest a usage close to 62%, many programs
reserve memory ahead of use and also use space on disk. As such, these numbers
do not really mean much unless you fully understand the details (which is well
beyond the scope of this guide).

### nice and renice

If a system is only being used by one user at a given time then most of the
resources could be used by them without affecting any other user. However, it
will usually be the case that a number of users will be trying to use the
system at the same time. Several users might be running R scripts and someone
else might be using Python. Further, R has a habit of trying to use as many
processor cores as it can if it runs anything in parallel. This can result in
even the most capable system being stretched to its limits.

There are a number of ways in which resources can be managed when this type of
competition occurs. One is to implement a queue system to limit the resources
which each process can use and to restrict how many user processes can run at
any given time. This is essentially the method used on most high performance
HPC systems such as ISCA. A queuing system has also been put in place on ennis,
details for which are given in a separate guide. The alternative which is being
tried on slade is to make use of the standard linux command 'nice' which is
used to alter the priority of running processes.

Two other columns of information which top will give are 'PR' (priority) and
'NI' (nice). Note that the two are essentially the same with PR=NI+20. Also
note that a PR=0 is the highest priority and PR=39 is lowest priority, which
may seem counter-intuitive. All standard processes run, by default, with a nice
value of 0 (PR=20). A user can be nice to other users by running their process
with lower priority, that is a higher nice value. This is done by giving the
command nice and the value required before the command to be run. However, if
nice is just given a command to run then it will apply a default nice value of
10:

```
$ nice -10 R
$ # OR #
$ nice R
```

After which top will show the process with the non-default setting, here
alongside another user's process which is running with its default priority,
NI=0 (most of the header has been trimmed from the output):

```
$ top
...
PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
76431 abc123 20 0 7845232 941044 27208 R 7415 0.1 145181:30 R
424366 def456 30 10 8994292 968760 14280 R 5239 0.1 1:15 R
...
```

If a command is run without nice then the process priority can be changed later
using renice, either by the owner of the process, or by the root admin user:

```
$ renice -n 10 -p 76431
$ top
...
PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
76431 abc123 30 10 7845232 941044 27208 R 7415 0.1 145181:50 R
424366 def456 30 10 8994292 968760 14280 R 5239 0.1 1:35 R
...
```

Although nice takes a default value of 10, renice requires the new value to be
given explicitely. Note that a standard user cannot increase the priority of a
process by giving it a lower nice value. Also, if a running bash shell is given
a lower priority then all subsequent commands run in that shell will inherit
that lower priority. Similarly, by running `nice rstudio &` the accompanying
'rsession' process will also be run with lower priority.

So that all users on slade get a fair share of resources, any commands or
processes which are run which will take more than a few minutes of intense
processing to complete should be run with a nice value of 10 (the default). If
any users are seen to persistently flout this requirement then this limit may
be applied as an enforced limit on all of that user's sessions.

### kill and pkill

Sometimes it is necessary to kill processes which you no longer have control
over. For example, orphaned rsession processes left over from killed rstudio
sessions, described above. The ps command lists, amongst other things, the
process ID (PID) of each process. The `kill` command can be used to kill
specific processes by giving it a list of PIDs. Alternatively, all processes
for a given command can be killed with `pkill`. Needless to say, standard users
can only kill their own processes, though admin users can kill any processes as
and when required. However, this is usually only done if a process is obviously
out of control, and is rarely done without consulting the owner of the process
first. See below, and the relevent man pages, for more details. Using the `a`
option for `ps` to give all processes will give everything on the system. The
output can be piped into less or `grep` (described in the Advanced Linux
guide), or the `-C` option can be used to restrict output to a single command
name. Depending on other options given to `ps`, its output columns, and their
formats, vary considerably. The most variable is the command column: with some
options the full command path is given for each process complete with all
arguments which were given when it was run. Here a few extra options are given
to restrict the output columns and only list `rsession` and `rstudio`
processes:

```
$ ps -C rsession,rstudio --format user,pid,etime,time,ucmd
USER PID ELAPSED TIME CMD
abc123 1234567 4-23:08:48 00:12:34 rsession
abc123 1357913 7-16:49:37 00:19:13 rsession
pqr789 2468024 2-23:02:33 00:08:11 rsession
def456 124715 53-15:02:02 02:10:51 rsession
xyz246 2481350 4-16:44:52 00:11:19 rsession
xyz246 3191106 17:48:55 00:08:49 rstudio
xyz246 3196386 17:45:25 00:52:16 rsession
```

Since rsession processes are usually only created by rstudio, all but the last
of the rsession processes listed are probably orphaned, left over from killed
rstudio sessions. It can also be seen that one process, owned by user def456,
has been running for 53 days! User abc123 can kill all of their rsession
processes using either `kill` or `pkill`:

```
$ kill 1234567 1357913
$ # OR #
$ pkill rsession
```

After which the rsession processes for user abc123 can be seen to have gone:

```
$ ps -C rsession,rstudio --format user,pid,etime,time,ucmd
USER PID ELAPSED TIME CMD
pqr789 2468024 2-23:02:33 00:08:11 rsession
def456 124715 53-15:02:02 02:10:51 rsession
xyz246 2481350 4-16:44:52 00:11:19 rsession
xyz246 3191106 17:48:55 00:08:49 rstudio
xyz246 3196386 17:45:25 00:52:16 rsession
```

If a process appears reluctant to be killed then the option `-9` can be added
to kill. However, this should only be used as a last resort. Care should also
be taken to make sure that the right process number is given. Although a
standard user can only kill their own processes, it is still possible to `kill`
the wrong process. See the man pages for more details on `kill` and `pkill`.
