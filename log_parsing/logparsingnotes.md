# messages Log

#### Full Messages Check
```
for i in $(ls -1tr /var/log/messages*); do cat $i >> messages-combined; done
egrep -i 'fail|fault|warn|invalid|conflict|crit|excep|cannot|unable|vmalloc|blocked|call trace' messages-combined | less
```


#### Short Message Check
```
egrep -i 'fail|fault|warn|invalid|conflict|crit|excep|cannot|unable|vmalloc|blocked|call trace' /var/log/messages | less
```


#### Short Message Check

```
grep -B5 -A45 "Call Trace" /var/log/messages* | grep -vi "getty\|of user"
grep "Call Trace" /var/log/messages*
```


#### Kernel Messages: Blocked Process
```
grep "blocked for more than 120 seconds" /var/log/messages*
```



#### Memory Allocation Messages

```
grep "to vmalloc" /var/log/messages* | awk '{$3="_TIME_"; print $0}' | uniq -c

grep "V-5-3-0\|V-5-0-2031" /var/log/messages* | awk '{$3=""; print $0}' | cut -d' ' -f1-11 | sort -n | uniq -c

grep "child failed" /var/log/messages* | awk '{$3=""; $NF=""; print $0}' | uniq -c
```

#### Remove Tape Messages
```
<> | grep -v "CDB\|hostbyte\|reservation conflict"
```

#### Remove SDC / IPS Messages
```
<> | grep -v "Ips\|Received request\|Received Override\|Policy supports\|Override policy"
```

---

### Extract & Check Memory Errors

```
tar xzvf sysinfo-*.tgz
grep -i " ECC\| Err\|POST Err" sysinfo-Log_*/sysinfo_log.txt | awk '{$1=""; print $0}' | awk -F":" '{$2=""; $3=":00"; print $0 }' | sort | uniq -c | grep Channel

grep DIMM_ -A2 sysinfo-Log_*/sysinfo_log.txt | grep -v "Slot\|==\|--\|\/20" | awk '{ORS=NR % 2? "    ": "\n"; print}' | awk 'NF'

grep "Slot:\|MemoryType:\|SerialNo:\|Manufacturer:\|PartNumber:" sysinfo-Log_*/sysinfo_log.txt | awk '{ORS=NR % 5? " ": "\n"; print}'
```
