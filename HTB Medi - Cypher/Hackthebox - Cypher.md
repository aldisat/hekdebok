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
try open using jd-gui
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
# 3. Privilege Escalation
## a. ..
# 4. Post Exploitation
## a. User Flag
## b. Root Flag
