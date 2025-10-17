# File transfer

## What are SCP, SFTP and rsync?

There are, broadly, three main methods for transferring files between file
systems on different computers. These are SCP, SFTP and rsync. Although SCP and
SFTP refer to the underlying protocol (technical rules) used for performing
transfers, they are also command line programs which can be used to carry out
the transfers. An in depth discussion of the technical differences between the
protocols is well beyond the scope of this document. Instead, this document
will concentrate on basic usage of the three programs, and a few other programs
which use the protocols.

In terms of usage, SCP, SFTP and are usually used for slightly different
scenarios. Several technical differences are worth noting here: SFTP and rsync
can resume an interupted transfer whereas SCP cannot; SFTP can perform
operations such as directory listings; and rsync can compare hierarchies of
files. This second difference means that SFTP is usually used for interactive
use with SCP and rsync restricted to command line use when the files to be
transferred are known. Also, SCP tends to be used for transfering smaller
numbers of files, with rsync used for larger numbers.

## Copying Files To or From Linux or MacOS

Direct transfer of files between linux or MacOS systems can be achieved using
command line `scp` or `rsync`. Note that use of these commands for copying to a
Windows system from a linux-like terminal is possible, but unreliable. Although
the contents of the files should copy okay, if an attempt is made to keep
permissions then it is likely that the files may become unreadable and file
permissions will need to be reapplied recursively from a parent directory. For
specific Windows alternatives, see 
[this section](#copying-files-to-or-from-a-windows-desktop).

`scp` is very similar to the standard copy command, `cp`, with a few exceptions
to enable remote copying. rsync is similar in principle, however, the options
differ. Graphical interfaces, such as Filezilla, are also available
([see below](#filezilla)).

### Command line scp

The `scp` command is similar to the cp command for a standard copy. The main
exception is that, as well as indicating the file, or files, to be copied,
details must be given for the remote system. This is achieved by preceding the
remote file or directory with the remote user and system names. Although the
copy can be made in either direction, if more than one file is being copied it
is usually easier to copy files from the local system to the remote system,
rather than vice versa. Note that if the destination file already exists it
will be over-written. Also, a recursive copy will follow any symbolic links
found in the source so some care is needed if symbolic links may be present.
The following examples illustrate the general syntax. For further details see
the `man` pages.

```
$ pwd

/home/users/abc123/Data1

$ scp -p *.dat abc123@research2:Data2/

abc123@research2's password:

[status of each file transfer]
```

In the above example, files are copied from the local system to the remote
system. Here, all the `.dat` files from the local current directory (`Data1`)
are copied into the `Data2` directory in the home directory of user `abc123` on
`research2`. There is no slash after the colon in the remote path so the path
is relative to the user's home directory. The `-p` flag tells `scp` to keep the
same permissions and date-time stamps for the files, if possible.

```
$ scp -pr Out abc123@research2:Data2/

abc123@research2's password:

[status of each file transfer]
```

This second example recursively copies the directory Out, and all its contents,
from the local current directory to the user's Data2 directory on research2.
In each case, the command prompts the user for the password of the remote user
account.

Note that it is important to include the trailing slash `/` on directories if
the copy is to place files within the directory. Without the slash, the
directory could be silently overwritten with each of the files being copied.
For `scp`, trailing slashes are important for both the source and the
destination.

```
$ scp -pr Out abc123@research2:Data2/Out2

abc123@research2's password:

[status of each file transfer]
```

Here the directory `Out` is copied and renamed to `Out2` on the destination
server. If a slash was put at the end of `Out2` then the directory `Out` would
be copied below `Out2` giving `Out2`/`Out`.

If transferring files through VPN proves to be a problem then it is possible to
'tunnel' through another server, with VPN turned off. If the version of `scp`
used in the local shell allows then this can be achieved as follows:

```
$ scp -o ProxyJump=abc123@hashbang.ex.ac.uk abc123@remotehost.ex.ac.uk:file1 .

abc123@hashbang.ex.ac.uk's password:

abc123@remotehost.ex.ac.uk's password:
```

This will copy `file1` from the `abc123` home directory on remotehost to the
current directory on the user's local machine, via the machine called
'hashbang'. The usual options for permissions and recursion can still be used,
as can the shorthand `-J`. Windows users should note that the version of `scp`
which comes with MobaXterm ([described here](#mobaxterm)) does not recognise
the 'ProxyJump' option. However, the 
[Git for Windows](https://gitforwindows.org/) program comes with a 'Git BASH'
bash emulator which does allow this option (but not the `-J` shorthand). MacOS
users should find that this option works from the normal terminal as long as
the underlying `ssh` command does.

### Command line rsync

The `rsync` command is similar to the `scp` command. The main exception is that
it compares the files at both the source and destination and only copies files
which need to be copied. Note that the command has over 100 different options
to control its behaviour so a full description of its use is well beyond the
scope of this guide. However, for most copies, use the `-a` flag to apply a
collection of useful options. This includes `-r` for recursion and the `-l`
option to copy symbolic links as symbolic links, instead of following them as
`scp` does. The following examples illustrate the general syntax:

```
$ pwd

/home/users/abc123/Data1

$ rsync -aP *.dat abc123@research2:Data2/

abc123@research2's password:

[status of each file transfer]
```

This is much the same as the first `scp` example, above. However, if the copy is
interupted, the `-P` flag  will allow the rsync command to be repeated with the
transfer continuing from where it left off.

```
$ rsync -aP Out/ abc123@research2:Data2/Out/

abc123@research2's password:

[status of each file transfer]
```

The same flags are used for the recursive copy. As for `scp` it is very
important to include trailing slashes on a directory where they are needed.
However, usage is slightly different from `scp`: for `rsync`, trailing slashes
are only important for the source; they do not matter for the destination. For
the source: no slash means to copy the directory and its contents; with a slash
means to only copy the contents of the directory. The destination is always a
directory and everything is copied into that directory.

If the files to be transferred are large and can be highly compressed then it
is worth also including the `-z` flag. This will compress files before
transfer then uncompress them at the destination. For large, binary files,
however, the overhead of compressing and uncompressing the files may mean that
there is little benefit in using the `-z` flag.

If required and if the local `ssh` supports it, tunnelling can be achieved by
explicitly giving the ssh command to the `-e` option:

```
$ rsync -aP -e 'ssh -J abc123@hashbang.ex.ac.uk'
abc123@remotehost.ex.ac.uk:file1 .

abc123@hashbang.ex.ac.uk's password:

abc123@remotehost.ex.ac.uk 's password:

[status of each file transfer]
```

In addition to copying files, `rsync` can also delete files at the destination
which are not present at the source. However, this only makes sense when
mirroring directories, and should be used with extreme caution.

For further details on how to use `rsync`, see the `man` pages or various
online resources.

### Comparing Directories using rsync

One valuable use of `rsync` is to compare directory structures. For example,
once a copy has been made, `rsync` can be used to compare the source and
destination directories and files at a later date and to simply report any
differences rather than to attempt any transfer of files. To do this it is
important to use the `-n` flag which specifies a 'dry run', and `-v` so that
the differences are actually reported:

```
$ rsync -avun Out/ abc123@research2:Data2/Out/

abc123@research2's password:

sending incremental file list

analysis.log

sent 3537 bytes received 30 bytes 7134.00 bytes/sec

total size is 2555536960285 speedup is 716438732.91 (DRY RUN)
```

If there are files missing at the destination then they will be listed before
the summary lines. To see any files which are new at the destination the
`--delete` option should be specified; the files will be listed, one per line,
with the word 'Deleting' before each one. Here the file `analysis.log` is
listed indicating that that file would be copied by a subsequent `rsync`. If
the file then failed to copy, this could be due to the user not having
permission to read the file.

When comparing files, `rsync` uses a "quick check" algorithm, by default, which
essentially looks at changes in size or last modified time. This will not pick
up mutations in the data due to issues with a previous copy or transfer so the
`-c` flag can be used to ask for a checksum verification for any files which
are the same size at both the source and the destination. The checksums will be
compared instead of the last modified times. However, when using `rsync` to
copy files, a checksum comparison is always used to verify that each file has
been transfered correctly, even without the `-c` flag, so this should never be
explicitly required when using `rsync` to copy files. Further, adding `-c` to a
`-n` dry run comparison should not be necessary if `rsync` was used for the
previous copy. It should also be noted that adding an extra checksum
verification can also slow down the `rsync` process significantly.

For comparisons under different circumstances, for example, if files at the
destination may have changed independently of any copy, other `rsync` flags
will be required. The user is again directed to see the man pages or online
resources for further details of the options available and when they might be
used.

### Command line sftp

The secure sftp command is used in a similar way to the non-secure ftp command.
Although it can be used to retrieve files automatically from a remote system,
it is usually used interactively. For interactive use, the graphical program
Filezilla, described below, should be considered if X11 forwarding is not a
problem. It provides a good alternative which can also save session
information. Note that the remote system must have a suitable server running,
and access controls in place, for this to be available. To connect:

```
$ sftp abc123@research2
```

Whereas with `scp` and `rsync` the remote path is relative to the user's home
directory, ftp and `sftp` may place the user in an alternative data area set
aside for file transfers.

As for `scp`, described above, the `sftp` command should accept the `-o`
'ProxyJump' option and the `-J` shortcut to allow proxy tunnelling through
another server, as long as the underlying ssh command also accepts these.

Once authenticated, you can list available commands using either `help` or
`?`. Here are some of the useful commands (much of the help text has been
shortened):

```
sftp> help

Available commands:

bye Quit sftp

cd path Change remote directory to 'path'

...

exit Quit sftp

get [-Pr] remote [local] Download file

help Display this help text

lcd path Change local directory to 'path'

lls [flags] [path] Display a local directory listing

lpwd Print local working directory

ls [-1aflnrSt] [path] Display a remote directory listing

...

put [-Pr] local [remote] Upload file

pwd Print remote working directory

quit Quit sftp

...

? Same as help
```

Two of the previous `scp` examples could then be achieved as follows:

```
sftp> lpwd

Local working directory: /home/users/abc123/Data1

sftp> cd Data2

sftp> pwd

Remote working directory: /res2/home/users/abc123/Data2

sftp> put --P *.dat

sftp> put --Pr Out

sftp> quit
```

Within `sftp`, you can also use `df -h` to check that there is enough space
available on the remote file system before transferring files. Although there
are 'local' versions of many commands (for example, 'pwd' and 'lpwd') to
get a local `df` you need to use the shell escape to call the local system's
df: `!df -h`.

## Copying Files To or From a Windows Desktop

There are several options for transferring files to or from a Windows Desktop.
A few of these are outlined below. The choice of which to use may depend on
personal preference, how many files need to be transferred and how much control
is required over the transfer process.

### WinSCP

WinSCP is an open source free SFTP client, SCP client, FTPS client and FTP
client for Windows. Its main function is file transfer between a local and a
remote computer. Note that, as an alternative to a traditionally installed
version which requires administrator privileges for installation, WinSCP is
available as a portable version. WinSCP can be downloaded
[here](https://winscp.net/eng/downloads.php).

When you run WinSCP a login window will appear for you to fill in the remote
hostname and your remote username. A 'Save' button allows you to save the
session details for future use. Clicking the 'Login' button will prompt for
the remote user's password. Once successfully authenticated a file manager
window will be displayed. This drag and drop interface is a similar to Windows
file manager and it's use should be intuitive.

### MobaXterm

If MobaXterm is already being used for terminal or shell access then its
inbuilt file manager can be used for simple drag and drop file transfers. This
will be available by default. Below the file manager panel is a tick box. If
this is ticked then the current directory within the file manager should track
and mirror the current directory within the shell. Note that any files which
are dragged and droppped will be given the current date and time, not those of
the original files. Alternatively, a new session can be setup using SFTP as the
session type. This provides a more substantial remote file manager, instead of
the shell login plus a local file manager. These sessions can also tunnel
through a proxy, as described in the `scp` example given above.

It is important to note the warning given in the section on Copying Files to
and from Linux or MacOS, above. MobaXterm can open a local CYGWIN shell
terminal and the `scp` and `rsync` commands are available in this terminal.
However, the file permissions which are set on copied files may not be reliable
due to differences between how linux and Windows set these permissions.

### Filezilla

Filezilla is a free graphical file manager which supports various protocols
including SFTP. It is available for Linux, MacOS and Windows platforms
[here](https://filezilla-project.org/download.php?show_all=1).
A portable version of Filezilla for Windows, which does not require
administrator privileges for installation, is available 
[here](https://portableapps.com/apps/internet/filezilla_portable).

When running Filezilla, a quick remote connection can be established by
entering the remote user and host details into the text boxes provided on the
main window (use port 22 for SFTP). Alternatively, sessions can be setup and
saved for future use through use of the Site Manager, which is accessed from
the 'File' menu. Use of the Site Manager also allows default local and remote
directories to be set and saved.

### Globus

To quote its webpage, Globus is a non-profit service for secure, reliable
research data management. More specifically, it has been designed for
transferring large (or small) quantities of data between different machines
based at the same, or different institutions.
