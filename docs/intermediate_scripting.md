# Intermediate Scripting

This document covers some more intermediate scripting techniques one can use
(mainly centred around bash, with some being more generally applicable).

## Wildcards and Regular Expressions

Wildcards and regular expressions are used for filtering, and several useful
commands used for filtering are given in the section on filtering, below.
Visually, they appear very similar, but there are some very important
differences. The usual use for wildcards is for selecting file and directory
names on the command line as input to a command such as `ls`. Any wildcards are
expanded to a list of arguments which is then passed to the command. In
contrast, regular expressions are strings with very specific formats which are
used by other commands for filtering. Since some aspects of regular exressions
are very similar to wildcards, when they are specified as command arguments
they will generally need to be protected from bash so that the command receives
the correct input.

### Wildcards

The main wildcard which most users will already know is the asterisk, or star,
`*` to match any number of characters. In a similar way, the question mark `?`
will match a single character. To match any one of a set of characters, use
square brackets `[ ]` to enclose the set, or to match anything not in the set,
start with an exclamation mark `!`. Note, however, that none of these wildcards
will match a leading dot `.` in a filename. Although these are not the only
wildcard combinations which could be used for the same filtering, the following
few examples serve to demonstrate the principles, first simple matching:

```
$ ls
data1_in Day1out.log
Data1.out Day2out.log
data2_in Do_day.R
Data2.out No_data.txt
$ ls *.out
Data1.out Data2.out
$ ls ?ata1*
data1_in Data1.out
```

The set of characters to match using `[ ]` can include a range of characters
specified using a dash `-`. Note that just as filenames are case sensitive, so
are any matches, so use `[a-zA-Z]` to match any letter (from the latin
alphabet). Although not a wildcard, another useful construct to include here is
list expansion using curly braces `{ }`. Each alternative match in the list is
separated by a comma:

```
$ ls *o[_g]*
Day1out.log Do_day.R
Day2out.log No_data.txt
$
$ ls *a[1-9]*
data1_in data2_in
Data1.out Data2.out
$ ls *a[!1-9]*
Day1out.log Do_day.R
Day2out.log No_data.txt
$
$ ls Da{ta,y}1*
Data1.out Day1out.log
```

In each case, the wildcard is expanded to a list of arguments before being
passed to the command, here ls. So, for example, '*.out' is expanded to
'Data1.out Data2.out' before being passed to ls. Note that if the expansion is
likely to result in a very long list of matches, and this list is passed to a
command which is not one of the built in bash commands, then care is needed not
to exceed the limit of the bash exec argument, usually 2^21 (just over 2
million) characters. In practice, this would require a match to most of the
files in a directory of 100,000 files with each filename being 20 characters
long. The limit is more likely to be an issue if a full, long path is included
in the wildcard match, and the path is retained in the result. For example,
when listing the contents of a directory with ls the simplest option is to just
give the name, or a wildcard, for the directory name. But what if a wilcard was
also required for matching the filenames? Different results would be obtained
by piping the list from ls through, for example, grep before the main command
required. Compare the following two examples which compile a list of files from
a directory using wildcards, though only the first line of each output is
given:

```
$ ls /topfs/host/projects/Research_Project_MRC123456/UKBiodata/ABCD_Genetic
_data/imputed_data*/Xsub*/BEST* | head -1
/topfs/host/projects/Research_Project_MRC123456/UKBiodata/ABCD_Genetic_data
/imputed_data_best_guess/Xsub_log_files/BEST_GUESS.e123456

$ ls /topfs/host/projects/Research_Project_MRC123456/UKBiodata/ABCD_Genetic
_data/imputed_data*/Xsub* | grep "^BEST" | head -1
BEST_GUESS.e123456
```

In the first case, each wildcard match returns the full path with 133
characters, in the second each returns just the filename with 18 characters.
However, even including the full path, if this directory had 15,650 files in it
there would still be room on the command line for a few more options!

## Regular Expressions

Use of square brackets `[ ]` in regular expressions is similar to their use in
wildcards, however, to negate a set of characters, the caret `^` is used
instead of `!`. Single characters are matched by a dot `.` not `?` and the star
`*` is still used to match any number of characters, however, it requires a
preceding character to match against, or a dot `.` to match any character any
number of times (including none). Note that the caret is also used to match the
beginning of a line, when not within a character set, and the dollar `$`
matches the end of a line. A plus `+` is similar to `*`, however, the preceding
character must be present at least once and will usually need to be escaped:
`\+`.

For the following examples, the same directory contents as for the examples
above are piped through grep, though similar results could also be obtained by
giving suitable arguments to ls. Note that, by default, ls outputs one entry
per line when output is piped to another command. (Also, see below for more on
grep.)

```
$ ls | grep "." # all files
data1_in
Data1.out
data2_in
Data2.out
Day1out.log
Day2out.log
Do_day.R
No_data.txt
 
$ ls | grep "o[_g]"
Day1out.log
Day2out.log
Do_day.R
No_data.txt
 
$ ls | grep "^d"
data1_in
data2_in

$ ls | grep "a.*t"
data1_in
Data1.out
data2_in
Data2.out
Day1out.log
Day2out.log
No_data.txt
 
$ ls | grep "a.\+t"
Day1out.log
Day2out.log
No_data.txt
 
$ ls | grep "[^ng]$"
Data1.out
Data2.out
Do_day.R
No_data.txt
```

There are also more complicated, extended pattern matching options, such as
specifying the number of repeats or finding patterns which repeat. However,
these are beyond the scope of this guide.

## Aliases

Aliases are an intrinsic part of the interactive bash shell. The bash man page
states that aliases "allow a string to be substituted for a word when it is
used as the first word of a simple command". There are two main reasons for
using aliases. The first is to effectively replace use of one command with
another without removing the original command and replacing it with the
replacement, or symbolic links. For example, it was mentioned earlier that
running sh actually runs bash. This is achieved through a symbolic link as
illustrated in the section on backticks, below. In contrast, that section also
highlights that the traditional 'vi' editor is an alias for 'vim'.

A list of aliases can be obtained by running 'alias' with no arguments. Or by
giving alias an argument, any alias for that command will be reported, or an
error given if there is no alias:

```
$ alias vi
alias vi='vim'
$ alias vim
-bash: alias: vim: not found
```

The 'which' command will report which command will be run when issuing the
command. Here it is used to report which version of vi would be run when
calling vi, showing that it is an alias. Then, adding the option '--version' to
the underlying vim command reports its version (amongst a lot of other
information):

```
$ which vi
alias vi='vim'
/usr/bin/vim
$ /usr/bin/vim --version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Jul 23 2019 13:28:34)
...
Huge version without GUI. ...
...
```

On some systems, however, which is also an alias defined to report if a command
is an alias. To use the non- alias version, precede the command with a slash
'\':

```
$ \which vi
/usr/bin/vi
$ /usr/bin/vi --version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Jul 23 2019 13:27:50)
...
Small version without GUI. ...
...
$
```

Applying this technique to vi, it can be seen that running 'vi' invokes the
alias and runs vim 'Huge version', whereas running '\vi' avoids the alias and
runs the 'Small version':

```
$ vi --version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Jul 23 2019 13:28:34)
...
Huge version without GUI. ...
...
$ \vi --version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Jul 23 2019 13:27:50)
...
Small version without GUI. ...
...
$
```

The second reason for using aliases is convenience. Perhaps a command needs to
be run regularly with the same arguments, or a command's fixed arguments are
long winded to type each time. For example, to use a remote 'phpMyAdmin'
installation for accessing a MySQL database, the following can be used to
launch the web page interface from firefox:

```
$ firefox --new-window https://localhost/phpmyadmin/ &
Instead of typing this every time, an alias can be used. Here it is set and then used:
$ alias phpmyadmin='firefox --new-window https://localhost/phpmyadmin/'
$ phpmyadmin &
```

Note that there are no spaces around the equals '=' and there are quotes around
what the alias is to be set to. In this regard, it is very similar to setting a
bash variable. Also note that an alias will not call itself, so a definition
such as the following (which gives ls it's colour output) will not result in a
circular definition:

```
$ alias ls='ls --color=auto'
```

If user defined aliases are required then they are often added to the .bashrc
file so that they become active in every interactive shell. For convenience,
another option is to define aliases in a separate file, for example '.aliases'
and then to source this file from the .bashrc file.

If an alias is set or used as part of a more complex, compound statement then
there are added complications relating to when it is expanded. A full
discussion is well beyond the scope of this guide. Suffice it to say that if an
alias is defined on a separate line and not used in compound statements then no
problems should arise.

## cd and pushd

The 'cd' command is a very basic bash command. However, there is one useful way
in which its behaviour can be changed. Also of interest to some users is the
'pushd' command which allows the user to keep a 'stack' of directories which
have been visited, and to switch easily between them.

### cd

The main option of interest for using with cd is the '-P' flag. Without it, if
cd is used to follow a symbolic link then the current directory path will
contain the original location plus the link:

```
$ ls -logd data*
drwxr-x--- 1 39 Feb 17 17:23 data2
lrwxrwxrwx 1 5 Feb 19 10:16 data4 -> data2
$ pwd
/home/abc123
$ cd data4
$ pwd
/home/abc123/data4
```

If '-P' is added then the new location is resolved so that the path is the true
path of the destination:

```
$ cd
$ pwd
/home/abc123
$ cd -P data4
$ pwd
/home/abc123/data2
```

The '-L' option is very similar to '-P', the difference being at what stage any
occurrances of '..' are resolved when resolving any links in the path. Compare
the following using '-P':

```
$ ls -log subdir2
lrwxrwxrwx 1 5 Feb 19 16:56 subdir2 -> data2/subdir2
$ pwd
/home/abc123
$ cd -P subdir2/..
$ pwd
/home/abc123/data2
with the following which uses '-L':
$ cd
$ pwd
/home/abc123
$ cd -L subdir2/..
$ pwd
/home/abc123
```

With '-P' the link was resolved first: 'subdir2' was resolved to
'data2/subdir2' and then the '..' took the path up to 'data2'. With '-L' the
'..' was resolved first: 'subdir2/..' was resolved to '.' which removed the
link and left the path at the current directory.

### pushd, popd and dirs

The 'pushd' command is very similar to cd except that the previous directory
path is remembered on a stack. To see what is currently on the stack, use
'dirs', which should at least have the current directory:

```
$ cd
$ dirs
~
$ pwd
/home/abc123
```

Using pushd to change directory and add to the stack also lists the new stack:

```
$ pushd data2
~/data2 ~
$ pwd
/home/abc123/data2
$ pushd subdir2
~/data2/subdir2 ~/data2 ~
$ pwd
/home/abc123/data2/subdir2
```

The pushd command can also be used to switch between paths already on the
stack. With no options given it will switch the top two paths:

```
$ pushd
~/data2 ~/data2/subdir2 ~
$ pwd
/home/abc123/data2
With a number given it will rotate the stack:
$ pushd +2
~ ~/data2 ~/data2/subdir2
$ pwd
/home/abc123
$ pushd -0
~/data2/subdir2 ~ ~/data2
$ pwd
/home/abc123/data2/subdir2

```

If a symbolic link is on the path used given to pushd then it is used as is .
That is, the path is not resolved to its true path:

```
$ pushd
~ ~/data2/subdir2 ~/data2
$ pushd data4
~/data4 ~ ~/data2/subdir2 ~/data2
$ pwd
/home/abc123/data4

```

If the true path is preferred then 'cd -P' can be used after the use of pushd:

```
$ cd -P . # 'fix' the path from the previous pushd
$ dirs
~/data2 ~ ~/data2/subdir2 ~/data2
$ pwd
/home/abc123/data2
$ pushd . # add an extra path to the stack (any path)
~/data2 ~/data2 ~ ~/data2/subdir2 ~/data2
$ cd -P ~/subdir2 # change to the required true path
$ dirs
~/data2/subdir2 ~/data2 ~ ~/data2/subdir2 ~/data2
```

To remove paths from the stack use 'popd'. As with pushd, a number can be also
given:

```
$ popd
~/data2 ~ ~/data2/subdir2 ~/data2
$ popd +2
~/data2 ~ ~/data2

```

The stack can be cleared completely by passing '-c' to the dirs command. Using
Other Commands in Bash

A few simple examples of using other commands within bash were given in the
Basic Linux guide. More advanced usage including filtering and combining
commands is given in a separate guide.
