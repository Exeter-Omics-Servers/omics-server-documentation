# Shell Scripting Basics

Although the bulk of this guide concentrates on using the `bash` script, it is
convenient to start with a brief introduction to scripts in general.

Example scripts are given throughout the various sections below, and in the
separate guide on using commands such as `awk`. In the `awk` example, the
script followed the syntax of the awk language. Other scripts are given using
bash to highlight features of bash. Essentially, a script is simply a
collection of commands, using a designated language, which can be used to carry
out a prescribed task. Usually, scripting languages are taken as being those
where the script is interpreted at run time. This is distinct from programming
languages such as C where a program is first compiled into run time code, then
the compiled version is run. However, this distinction is not perfect as some
languages can be used in both ways.

In general there are two ways of invoking a script, though not all languages
allow both methods. For the first method, as per the `awk` example, the file
name is given to the command which should be used to run the script.
Alternatively, the script is set as executable (see Permissions in the Basic
Linux guide) and a setting in the file tells the parent shell which command to
use. This setting uses the so-called 'shebang' (`#!`) though exactly what
should come after it can vary, as described below.

It is not possible to cover all of the different scripting languages here.
Instead a small selection will be highlighted with a brief outline of their
capabilities. Others will also be covered within the section on bash.

## The shebang

The shebang (`#!`) is used on the first line of a script to tell the shell
which command (interpreter) to use if the script is run as an executable. If
the script is given as an argument to the command to be used then the shebang
is ignored, and can be omitted entirely.

If the shebang is given in a script then either the full path to the required
command is given, or the path to 'env' is given followed by the command name.
These two options both have their pros and cons, though the pros for one tend
to be cons for the other, and vice versa! The following examples refer to
`bash`, however, the same principle applies to any command used for running
scripts.

### Providing exact path

``` bash
#!/bin/bash 
# rest of script follows
```

Pros: 

- Only the specified executable will be used to run the script.
    - This ensures portability by reducing the risk of the script breaking due
    to changes in the version being used.

Cons: 

- A different version of the interpreter can only be used by giving it
explicitly on the command line.

### Passing responsibility over to PATH

```bash
#!/usr/bin/env bash
# rest of script follows
```

Pros: 

- The command used to run the script will be obtained from the user's
search path. 
    - This enables the user to choose which version is used simply by changing
    the search path, or by making an alternative version available earlier in
    the search path.

Cons: 

- A different version of the command may not be compatible with the script.
- The script author cannot enforce the use of a specific version without adding
code to the script as a safeguard.

### Verdict

Both options also have the same restriction that the specific path must exist
on the system the script is to be run on so may not be entirely portable.

The choice of which option to use is probably not critical in most situations.
In practice, the choice may be determined by practicalities of the software
being used. For example, the 'Rscript' command may only be available if the
relevent R software module has been loaded. The path to the module may change
with time, and is probably quite long. It is much easier to specify the first
of the following two options:

```bash
#!/usr/bin/env Rscript
#!/software/easybuild/software/R/3.6.3-foss-2020a/bin/Rscript
```

However, if the script is known to fail for R version 4 due to an updated
package then it would be up to the script author to implement a safeguard for
this, or to ensure that anyone running the script is fully aware of this
limitation. bash or sh

The traditional 'sh' shell is a core, basic shell. Most other shell commands
(for example, 'bash', 'csh', 'tcsh', 'zsh') are more functional variations of
'sh'. Which of these are available on any particular system, and which is set
as your default shell, is determined by the OS and the system administrator. If
a script only uses core sh functionality then it could probably be invoked by
any available (compatible) shell. If shell specific features are used, however,
then the correct shell must be used. On some systems, however, the traditional
sh may not be available. For example, on slade and ennis, sh points to bash so
running sh actually runs bash, as highlighted in the section on bash, below.

### Other possible solution

It should be noted that a shebang is only required when running scripts like
an executable (*i.e.* `./script.sh`), if the software requirements are well
defined, one can always just run the script with said software 
(`bash ./script.sh`).

## More bash

Much of the functionality of the bash shell is taken for granted. As well as
the standard commands such as 'ls' and 'cd' covered in the Basic Linux guide,
bash has a rich selection of other features, some of which are outlined below.
However, a full understanding of some of the more intricate behaviour of bash
is well beyond the scope of this guide. The "Advanced Bash-Scripting Guide" on
"The Linux Documentation Project" web site, is a single web page. However, if
printed to pdf it stretches to over 700 pages! Although it was last updated in
2014, it covers everything, or almost everything, you may ever need to know
about bash. It also has plenty of examples:
https://tldp.org/LDP/abs/html/abs-guide.html

## Variables

Shell variables are intrinsic to most of what bash does. They essentially store
information or settings so that bash, or other commands, can access those
settings when needed. For example, when a command is issued within a bash shell
bash checks the `PATH` variable to see which directories to look in for that
command. If the shell being used has X forwarding turned on then there should
be a variable called `DISPLAY` which tells any command which needs to know
where to send any graphical output.

The following examples include simple bash scripts. Details of the shebang on
the first line in a bash script are given above. It is also assumed that
execute permission has been set to enable them to be run.

### Environment and Local Shell Variables

Most system environment variables have names which are fully capitalised, for
example the `PATH` variable used for the bash search path. When using your own
variables within a script, or the bash shell, it is prudent to always use lower
case for the variable names to avoid inadvertently changing any environment
variables.

One set of variables available to bash scripts are the special variables `$*`,
`$@`, `$#` and positional variables `$0`, `$1`, `$2`, `$3`, and so on. The
positional variables correspond to the script name plus the arguments passed to
the script, with `$0` being the script name, `$1` the first argument, and so
on.

Considering the actual arguments, that is, excluding the script name, `$*` and
`$@` contain all of the arguments. However, `$*` contains all of the arguments
as a single string whereas `$@` contains a list of separate strings. The other
special variable, `$#`, contains the number of arguments. The 'shift' command
will remove the first argument `$1`, at which point `$2` will become the new
$1. These are demonstrated by the simple bash script 'show_args.sh'

```bash
#!/usr/bin/bash
# show_args.sh
echo Num: "$#"
echo 1: "$1"
echo 2: "$2"
echo All: "$@"

shift

echo Num: "$#"
echo 1: "$1"
echo 2: "$2"
echo All: "$@"
```

The output could look something like this:

```text
$ ./show_args Hello there
Num: 2
1: Hello
2: there
All: Hello there

Num: 1
1: there
2:
All: there
```

Note that to run a file in the current directory as a script the name must be
given as an absolute path or a relative path (hence the preceeding `./`). If
just the filename is given it will be looked for in the search path (given by
the `PATH` environment variable) and will probably not be found, unless the
current directory is on the search path. Worse still, the name may be found
elsewhere and the wrong program or script may be run.

A few other useful ways of using these variables are shown below in the section
on Manipulating Variables.

### Setting Variables

Variables can be set within a shell simply by running `name=value` (imporantly,
with *no spaces* around the '='). However, there are a few extra details,
including use of the `export` command, which affects where that variable can
then be used. The following examples serve to demonstrate the most useful or
important aspects. Consider the bash script 'foobar' which simply echoes the
values of the two variables 'foo' and 'bar':

```bash
#!/usr/bin/bash
# foobar.sh
echo "foo:${foo}"
echo "bar:${bar}"
```

```
$ source foobar.sh
foo:
bar:
```

The bash `source` command runs the contents of the file in the current shell
(as if you copied and pasted these commands into the shell yourself). Nothing
is printed after either "foo:" or "bar:" because the two variables have not yet
been set.

In the following, the two variables are set using slightly different methods.
First foo is set by simply assigning the value to the variable. Then bar is set
in a similar manner, but as part of the export command. Now source shows that
both variables have indeed been set:

```
$ foo="Hello"
$ export bar="Goodbye"
$
```

```
$ source foobar
foo:Hello
bar:Goodbye
```

However, when the script is run as a subshell, only the value of bar is
printed, though using source again shows that both variables are still set in
the current shell:

```
$ ./foobar
foo:
bar:Goodbye
```

```
$ source foobar
foo:Hello
bar:Goodbye
```

Any variables such as 'bar' which are set with the `export` command are passed
on to all subshells. In contrast, without the `export` command, the value of
'foo' is not passed to the script when run as a subshell. However, a variable
can also be passed explicitly to a command, and without changing the setting in
the current shell:

```
$ foo="Hi there" ./foobar
foo:Hi there
bar:Goodbye
$ source foobar
foo:Hello
bar:Goodbye
```

In the first example, the script receives the value of "Hi there" for foo. The
current shell, however, still has the original value of "Hello" set, confirmed
again by using source to run the script. Another way of calling bash without
using a file as a script is to pass it commands using the '-c' argument,
separating individual bash commands with a semicolon (';'). Be warned, though,
that this is starting to get more complicated:

```
$ foo="Hi there" bash -c 'echo foo:$foo; echo bar:$bar'
foo:Hi there
bar:Goodbye
$
$ foo="Hi there" bash -c "echo foo:$foo; echo bar:$bar"
foo:Hello
bar:Goodbye
```

Note the different outputs obtained when different quotes are used. When single
quotes are used around the argument to bash the value of foo passed on the
command line is used. However, when double quotes are used the value of foo
which is echoed is that from the shell, not the value passed on the command
line. What is happening in the second case is that the string within the double
quotes has all variables replaced by the current bash shell before the string
is passed to the call to bash. This is therefore equivalent to:

```
$ foo="Hi there" bash -c "echo foo:Hello; echo bar:Goodbye"
foo:Hello
bar:Goodbye
```

The situation becomes much more complicated if a string on the command line
needs to include quotes or other special characters. then the backslash
character can be used to 'escape' the following character:

```
$ foo="It's \"Summer\"!" bash -c 'echo foo:"$foo"; echo bar:"$bar"'
foo:It's "Summer"!
bar:Goodbye
```

Often a bit of trial and error is required to get the right combination of
quotes and escapes. In the example above, not only do the double quotes in the
string passed in for foo need to be escaped, but an extra set of double quotes
are needed around the two variables within the string passed to bash. Without
these extra quotes, bash tries to interpret the single quote and the
exclamation mark within the foo string, and gives an error. If there is an
unbalanced quote (as above) then the prompt changes to '> ' (or whatever the
environment variable `PS2` is set to) and bash will wait for you to finish
closing the string which has been passed to it:

```
$ foo='It\'s "Summer"! ' bash -c 'echo foo:"$foo"; echo bar:"$bar"'
> '
bash: Summer!: command not found...
bar:Goodbye
```

Pressing Ctrl-C to terminate the command is often the safest option if this
happens as the command is obviously not doing what was intended. Ctrl-D could
be used instead of the close quote to end the input and exit the subshell (by
sending the 'end of file'/EOF marker), but bash will still try to interpret the
string passed to it. Therefore, it is always better to test any complicated
combination of quoted strings and escaped characters in a controlled way first,
rather than using something untested in a command where getting it wrong could
cause damage to files or data.

For example, the foobar script, above, does not include quotes around '$foo'
and '$bar' when echoing them. If the echo statements were simply 'echo $foo'
and 'echo $bar' and either foo or bar contained something like "rm *" then this
could remove all files from the current directory, no questions asked! More
realistically, if the statement was 'rm $file_name' and the file_name contained
spaces, for example, then this would result in the wrong files being deleted.

### Manipulating Variables

Variables are useful for a lot more than just being echoed to the output. Being
echoed is a simple example of passing a variable to another command as an
argument. Any command can take a variable as an argument. One of their
strengths, however, is the ability to manipulate their content. The following
examples are not an attempt to fully detail the use of bash variables. Instead,
they aim to give a few examples of how variables can be used in order to
highlight their power when used correctly. The interested user with an
application in mind can then find out more.

Just as the special variable $# gave the number of arguments to the bash
script, the '#' qualifier can be used to report the length of a variable
string. Substrings can also be extracted by giving the first character position
(starting from 0) and number of characters required, separated by colons (':').
If the number is not given then the rest of the string is returned. The
'mangle_args' script shows some of these string manipulations:

```bash
#!/usr/bin/bash
# mangle_args.sh
echo -e "1: $1\nLen1: ${#1}"
echo Where: "${2:1}, $2"
echo -n Who: "${1:0:2}, ${2:1:3}"
echo ", ${2:0:3}y"
```

```text
$ ./mangle_args.sh Hello there
1: Hello
Len1: 5
Where: here, there
Who: He, her, they
```

Two options have also been used here on echo. The first, '-e', tells echo to
treat '\n' as a newline (rather than the literal '\n'). In contrast, the second
option, '-n', is used to suppress the newline at the end of what echo outputs.
The next output from the script then continues on the same line. Note, however,
that if there is no following script output then the next bash prompt will
continue on the same line instead.

The special variable `$@` is an example of an array variable. Arrays can be
defined in a number of ways. One way is by splitting a string on a specific
character. The `PATH` variable, for example, contains a list of directory paths
separated by colons (':'). Although the `PATH` is a single string it can be
split into an array and then just one element assigned to another variable:

```text
$ echo $PATH
/home/abc123/.local/bin:/home/abc123/bin:/usr/local/bin:/usr/bin:/usr/local
/sbin:/usr/sbin
$ bash -c 'IFS=":"; a=( $PATH ); b="${a[3]}"; echo "$b"'
/usr/local/bin
```

Here `IFS` is the 'internal field separator'. By default this is a space, tab
or newline: ' \t\n'. This is first set to a colon (':') within the script to
make sure that the main shell value is not changed. The standard search `PATH`
variable is then assigned to new variable 'a' using parentheses '( )' to force
the result to be an array. Since the field separator is now a colon, the `PATH`
string is split on the colons. The variable 'b' is then set to the third
element of a, and finally b is echoed. (This could just as easily be achieved
using 'awk', described below.) Note that when assigning the array, spaces are
required between the contents and the parenthesis. This is the opposite of the
requirement for no spaces around the equals sign '='. Arrays are most useful
within scripts so that a set of commands can be run using each value in a list
(see the section on [control flow](#control-flow), below). Whereas the example
above used a variable within the parentheses, any command can be used. In fact,
some commands are rarely used outside of this scenario. For example, the `seq`
command will generate a list of numbers. The following snippet from a script
will generate a set of 8 numbered log files with a summary analysis provided
from running the R script (see section on R, below):

```
$ for d in $( seq 1 8 ); do
>     file_name="Day${d}out.log"
>     echo "Day $d's results" > $file_name
>     Rscript Do_Day.R -day $d >> $file_name
> done

$ ls Day?out.log
Day1out.log Day3out.log Day5out.log Day7out.log
Day2out.log Day4out.log Day6out.log Day8out.log
```

An intermediate variable 'file_name' is used to store the file name which is
constructed using the day number variable 'd'. Note the curly brackets '{ }'
around the 'd' to separate the name from the following text within the string.
The same value of d is then passed to the R script.

### Arithmetic Expressions

Suppose that the example above needed another variable, say hours 'h', based on
the day number such that 'h=d*24-12'. There are at least three different ways
of doing this. The following all achieve the same result:

```bash
# set a value for h from d
# using backticks and expr
h=`expr $d * 24 - 12`
# using let
let h=d*24-12
# using double parentheses
h=$((d*24+12))
```

In general it is recommended to use double parentheses when working with bash
scripting, it is the most stable of the three and behaves most closely wo other
languages. In fact, the double parentheses construct was implemented in
bash to allow a number of C-like expressions, including: 

- The assignment operators '-=' and '+='
- The pre- and post- increment and decrement operators '--' and '++'
- The tertiary operator '?:' (for example 'y=x<0?0:x') which returns one of two
values depending on the preceding condition.

## Tests and Conditions

A condition can be used to test the value of a variable and to choose a course
of action based on the result of that test. Within an 'IF' statement, the test
can be enclosed within either single brackets '[ ]' or double brackets '[[ ]]'.
There are both obvious and subtle differences between the two. Essentially,
however, the double bracket construct is an extension to single brackets,
available in bash and some other shells, which allows additional test
operations and regular expressions (see section below).


Note:

If this extra functionality is not required, and if the script must remain
portable (*i.e.* be [POSIX compliant](https://en.wikipedia.org/wiki/POSIX)),
then use should be limited to single brackets.

Basic test conditions include arithmetic comparisons, string comparisons and
a variety of file based checks (like existence, type, permissions (*etc.*)).
To learn about all possible options, you can look at the man page (`man test`).

```bash
#!/usr/bin/bash
# get filename from arguments
file=$1
echo Name: "$file"
file_len=${#file}
echo Name length: "${#file}"
if [ "$file_len" -gt 20 ]
then
echo "A long filename!"
else
echo "A short filename"
fi
```

```bash
#!/usr/bin/bash
# get filename from arguments
file=$1
file_len=${#file}
if [ "$file_len" -gt 20 ]
then
echo "A long filename:"
echo Filename: "$file"
echo Name length: "${#file}"
break
fi
```

In the first example, an extra message is output which changes based upon the
length of the filename. In the second example, the loop only outputs a message
when a long filename is found, and then the 'break' statement exits the loop.
Note that for comparing numbers the test operators are '-eq', '-ne', '-lt',
'-le', '- gt', and '-ge'. The operators '==', '!=', '<', and '>' are used for
comparing strings with '<' and '>' referring to the lexicographical
(alphabetical) sort order in the current locale. These require double brackets.
Multiple conditions can be combined using the operators "&&" and "||" for
logical AND and OR. (These are not the same as the single character operators
'&' and '|' which provide bitwise AND and OR.) To negate a condition use '!'.
To override the default order of precedence, or to make intentions more
obvious, use parentheses '( )' which also require double brackets:

```
$ if [ 3 -gt 2 ]; then echo "3>2 - T"; else echo "3>2 - F"; fi
3>2 - T
$
$ if [ $((3-5)) -gt 2 ]; then echo "3-5>2 - T"; else echo "3-5>2 - F"; fi
3-5>2 - F
$
$ if [[ ( $((3-5)) -gt 2 ) && ( 3 -gt 2 ) ]]; then echo " T"; else echo " F"; fi
F
$
$ if [[ ( $((3-5)) -gt 2 ) || ( 3 -gt 2 ) ]]; then echo " T"; else echo " F"; fi
T
$
$ if [[ ( $((3-5)) -gt 2 ) || ! ( 3 -gt 2 ) ]]; then echo " T"; else echo " F"; fi
F
$
$ if [[ "lkjh" < "asdf" ]]; then echo " T"; else echo " F"; fi
F
```

If regular expressions are used then the operator '=~' is used for checking for
a match. Note, however, that the regular expression being matched should not
include surrounding quotes:

```
$ if [[ "lkjh" =~ "l..h" ]]; then echo " T"; else echo " F"; fi
F
$
$ if [[ "lkjh" =~ l..h ]]; then echo " T"; else echo " F"; fi
T
$
```

See the section below for more details of regular expressions.

## Control Flow

A simple for loop, iterating over a list of integers, was shown in the example
above. However, the list being iterated over does not have to be numerical. In
practice it is often required that a loop needs to be repeated for each string
in a list of list of strings. The following example demonstrates the principle:

```bash
#!/usr/bin/bash
# get list of filenames
files=$(ls)
# `;` is interpretted as a newline here, so alternatively 'do' could be
# placed on the next line and the `;` character can be removed.
for file in $files; do
echo Filename: "${file}"
echo Name length: "${#file}"
done
```

Also shown above were a few simple if statements. if statements can have more
than one condition with additional conditions being preceded by 'elif' or 'else
if'. An if statement can also be used to 'break' out of a surrounding for loop:

```bash
#!/usr/bin/bash
# get list of filenames
files=$(ls)
for file in $files
    do
    echo Name: "$file"
    if [ -f "$file " ]
        then
        echo "File"
    elif [ -d "$file " ]
        then
        echo "Directory "
    elif [ -L "$file " ]
        then
        echo "Symbolic Link "
    else
        echo "Something else"
    fi
done
```

```bash
#!/usr/bin/bash
# get list of filenames
files=$(ls)
for file in $files
    do
    file_len=${#file}
    if [ "$file_len" -gt 20 ]
        then
        echo "Found a long filename:"
        echo Filename: "$file"
        echo Name length: "${#file}"
        break
    fi
    echo "Still looking..."
    echo "Found a short filename:"
    echo Filename: "$file"
    echo Name length: "${#file}"
done
```

A while loop is similar to a for loop except that the loop is continued while a
condition is met. This requires a variable to be set outside the loop and
changed within the loop, and for it to be tested for the exit condition. The
alternative 'until' loop continues until the condition is met:

```bash
#!/usr/bin/bash
# get short day names into array
alldays="Mo Tu We Th Fr Sa Su"
dn=( $alldays )
# loop through days
day=1
while [ $day < 8 ]
    do
    echo Day: "$day" "${dn[$day]}"
    day=$((day+1))
done
```

```bash
#!/usr/bin/bash
# loop through days
day=1
until [ $day == 8 ]
    do
    echo -n "Day: $day "
    case $day in
        6 | 7) echo "weekend";;
        *) echo "weekday";;
    esac
    day=$((day+1))
done
```

Also shown above is the case statement which is similar to an if statement with
multiple conditions.

