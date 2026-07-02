# grep command notes
---

The below command search for key word shut down.
[anything which starts with shut and ends with down] and then if it find shutdown keyword.
It will display two lines before and three lines after [-B before, -A after]  
---
```
# grep -I -n -B 2 -A 3 "shut*down" /var/log/messages 
```

Print line numbers 

```
# grep -i -n "shut*down" /var/log/messages 
```

Search exact word in Grep command 

```
# grep -i -w "system" /var/log/messages 
``` 
 
Find any line which does not find "system" word 

```
# grep -v "system" /var/log/messages 
```

Count number of line which does not have "system" keyword into /var/log/messages 

```
# grep -v -c "system" /var/log/messages 
```
 
Recursive search keyword "apachi" from /var directory and its subdirectories 

```
# grep -i -w -r "apache" /var 
```

If you want to know file names use below command 
 
```
# grep -i -w -r -l "apache" /var 

# cat messages | grep ERROR | awk -F":" ' {print $4} ' | sort | uniq -c
```

- Given the following file

hello how are you
i am fine
let's go, you!
let's go, baby!
grep with look-behind allows to print only some parts:

```
$ grep -Po "(?<=let's go, ).*" file
you!
baby!
```

--
grep '^[0-9]' raid_log.txt
