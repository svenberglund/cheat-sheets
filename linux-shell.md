
# Table of Contents
1. [vi(m)](#vim)
2. [grep](#grep)
3. [scp](#scp)
4. [ssh](#ssh)
5. [telnet](#telnet)
6. [Misc file manipulations](#msic-file-manip)
7. [System resources and diagnosing](#system-diagnose)


In this file I collect cheats on my most used console tools under unix like OS. 
Might spawn new cheet sheats of the sections when they grow large enough (git is in a separate one). For now this is what it is, quite a mixture. 



---------------------------------------------------------
# vi(m) <a name="vim" /> 
---------------------------------------------------------


## Enter command mode: 

```
[escape]
```

## Quit

```
:quit :q :q! (quit and ignore changes)
```

## Enter edit mode: 


`i` or `a`


## Search:  

`/` (forward)
`?` (back)


...repeat/continue search: `n`	
	

( use `.` for any char and `*` to repeat, example: `pr.*ties` finds properties)


## Replace all occurances of a string

```
:%s/pattern/replace/
```

## Move in editor

Move forward to line 50: `50G`


## Save and exit

save: `:x` (saves and exits)
exit: `:q`
exit and ignore unsaved changes: `:q!`



## Find and replace
```
:%s/foo/bar/gc
```
Change each `foo` to `bar`, but ask for confirmation first.




-------------------------------------------------
# grep
-------------------------------------------------


## Most important flags

Ignorecase: `-i`

Recursively: `-r`

List only (not display rows): `-l`



## Search recursively from current dir for a string, 
```
grep -r -i 'somethingtosearchfor' ./
```


## Exclude 
( eg exclude all .svn dirs):
```
grep -r -i 'somethingtosearchfor' ./ *|grep -v "\.svn/*"
```


## Pipe find to grep
Pipe from find into grep to search recursively from current dir for a string in particular file type:
```
find . -name '*.js' | xargs grep 'foobar'
```


## Excude
Exclude a certain file name pattern (*webpack*) in search for a string (*foobar*):
```
grep -ril --exclude='*webpack*' 'foobar'
```




---------------------------------------------------
# scp 
---------------------------------------------------

## Download
Bring home file ( *file* ) from the server (to *newFile* on local machine)
```
scp server@<ip_of_server>:file newfile
```

## Upload
To put a file ( *file* ) onto the server (as *newFile* )
```
scp file server@<ip_of_server>:newFile
```

## Examples
Copy file from remote machine home folder to local root folder without name change:
```
scp sven@myserver.com:myfile ./
```




----------------------------------------
# ssh 
----------------------------------------

Run with correct user according to config file under home folder

## TODO: rsa key generation and ssh key login

Write a step-by-step on this, don't forget the file permissions on the keys, that is usually what causes problems...
```
ssh-keygen
```
TODO: write notes on this, extract from pdf cheat sheet





----------------------------------------------
# telnet
----------------------------------------------

## Open session
```
telnet <server> <port>
```

## Close session:
Here's the (only) tricky part, how do you close a telnet session?\
First enter telnet command mode by (+ means combine):

```
Ctrl + ] 
```
...then close the telnet prgram (if that's what you want):

```
quit

```
The `quit` command can be abbreviated `q`. Alternatively you can use `close`.




```
----------------------------------------------------------------------
# Misc file manipulations <a name="msic-file-manip" /> 
----------------------------------------------------------------------


## Automate edits

### Pipe find to sed

Replace all occurrances of 'foo' with 'bar' in all '.php' files
```
find . -name "*.php" -print | xargs sed -i 's/foo/bar/g'
```
...check first that you get the correct set with: 
```
find . -name "*.php" -print
```


## Remove dir (myDir) and all of its content
```
rm -rf myDir
```

## chmod

Set read, write and execute by owner, read and execute by anyone else on the file 'file.cgi':
```
chmod 755 file.cgi
```

Set the permission of *file.txt* to "read and write by everyone" (-rw-rw-rw-).
```
chmod 666 file.txt
```

Set full permissions to owner and only read pernission to others:

```
chmod 744 file.txt
```
```

Set permissions on some folder (ex: htdocs) recursively:
```
chmod -R 755 /opt/lampp/htdocs
```




```
--------------------------------------------------------------------
# System resources and diagnosing <a name="system-diagnose" /> 
--------------------------------------------------------------------

## List all mounted volumes with labels
```
sudo lsblk -o name,mountpoint,label,size,uuid
```

## Monitoring cpu temperature 
Done by the following work flow:

(1) first running 
```
sudo sensors-detect
```
... then follow the instrucitons in the output.


(2) Monitoring programs won't work until the needed modules are
loaded. You may want to run `service kmod start`
to load them.


(3) Run `sensors`

