# ShellDoc
*Copyright (C) Tim K 2018-2019. Licensed under MIT License.*<br>
**ShellDoc** is an open-source documentation generator for bash scripts, written entirely in bash. It generates 
markdown output from special ShellDoc comments (``#@`` ones) that are usually located right before a function (or 
variable) declaration starts.

## Example of ShellDoc output
Here is an example script with one function and one variable:
```
#!/bin/sh

#@ Variable: yes
#@ HELLOWORLD_STRING variable stored the default string that is echoed by hworld function.
HELLOWORLD_STRING="Hello world"

#@ AvailableSince: 1.0
#@ Usage: hworld <optional arguments to echo after the string>
#@ Arguments: space-seperated list of additional strings to print (all arguments, $*)
#@ hworld prints the value of $HELLOWORLD_STRING variable as well as $* after that string. It always returns 0.
hworld() {
	echo "$HELLOWORLD_STRING $*"
	return 0
}

```
ShellDoc will produce the following output:

### ``HELLOWORLD_STRING`` [*variable*]
#### Basic info
**Appears on:**: Line 5 in file "shelldoc-helloworld-demo.sh" <br>

#### Description
<br>
HELLOWORLD_STRING variable stored the default string that is echoed by hworld function. <br>
<br>

<br>

### ``hworld``
### Basic info
**Appears on**: Line 11 in file "shelldoc-helloworld-demo.sh" <br>
**Available since**:  1.0 <br>
**Required arguments:**  space-seperated list of additional strings to print (all arguments, $*) <br>
**Arguments passing method:** standard <br>

### Usage
``` hworld <optional arguments to echo after the string>``` <br>

### Description
<br>
hworld prints the value of $HELLOWORLD_STRING variable as well as $* after that string. It always returns 0. <br>
<br>

<br>

---------------------------------------                                                                                                                                         
*Automatically generated by shelldoc 0.2.1 from ./shelldoc-helloworld-demo.sh on Linux (PWD="/home/tim/shelldoc", USER="tim", 28.06.2019 19:25:40)* 

## Usage
```
shelldoc <space-seperated list of bash scripts> > "<markdown documentation file name>"
```
For example, if we have files "foo.sh" and "bar.sh" and we want to create a Markdown documentation file with the name "foobar-docs.md", then we would run shelldoc this 
way:
```
shelldoc foo.sh bar.sh > foobar-docs.md
```
Alternatively, we could directly generate HTML documentation by piping ShellDoc output to Pandoc, like this:
```
shelldoc foo.sh bar.sh | pandoc -f markdown > foobar-docs.html
```

## Documenting bash scripts themselves
ShellDoc parses all comments starting with ``#@``. All of the documentation related to a specific function or variable must be placed before the declaration of the 
function/variable itself.

You can provide tags to set the availability, the basic usage examples or the required arguments fields. To do that, we use ``AvailableSince``, ``Usage`` and 
``Arguments`` tags. To indicate that we use a tag, we put a ':' after its name. Here is an example:

```
#@ AvailableSince: 2.0
#@ Usage: brand_new_function <adjective>
#@ Arguments: adjective that will be used to describe current day (first argument, $1)
#@ niceday is a function that descibes the day as $1. It returns 1 on error and 0 on success.
niceday() {
	if test "$1" != ""; then
		echo "What a $1 day."
		return 0
	fi
	echo "Adjective not specified."
	return 1
}
```

If your function supports piping or any other arguments specification methods, use ``argspec_method`` tag to specify those:
```
#@ Usage: cat "<file>" | basepiper
#@ Arguments: pipe the string or command output to convert to base64
#@ argspec_method: via pipes
#@ basepiper converts each line of the piped string to base64. It always returns 0.
basepiper() {
	while read line; do
		echo "$line" | base64 -
	done
	return 0
}
```

You can mark functions as internal if they are not meant to be used publicly using a tag called ``Internal``:
```
#@ Internal: yes
#@ Usage: _rickroll
#@ Arguments: does not take any
#@ argspec_method: does not take any arguments
#@ This function, if guessed and called by the user, tries to detect a web browser program and display rickroll. And no, it won't appear in the docs.
_rickroll() {
	RICKROLL_URL="https://www.youtube.com/watch?v=dQw4w9WgXcQ"
	if command -v firefox > /dev/null 2>&1; then
		firefox "$RICKROLL_URL"
	elif test `uname` = "Darwin"; then
		open -a Safari "$RICKROLL_URL"
	else
		xdg-open "$RICKROLL_URL"
	fi
	return $?
}
```

To document variables, use the ``Variable`` tag:
```
#@ Variable: yes
#@ This variable contains a link to Tim K's website.
TIMKOI_WEBSITE="http://timkoi.gitlab.io"
```

## License
ShellDoc is licensed under GNU General Public License v3.
