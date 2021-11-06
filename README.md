
The server header may specify the underlying technology used by an application. Attackers can conduct reconnaissance on a website using these response headers. This header could be used to target attacks for specific known vulnerabilities associated with the underlying technology. Removing this header will prevent targeting your application for particular exploits by non-determined attackers.
While this is not the only way to fingerprint a site through the response headers, it is more challenging. It prevents potential attackers from targeting your website.

# HTTP Header Server - RFC 7231

The Server header describes the software used by the origin server that handled the request — that is, the server that generated the response.

**Warning:** Avoid overly-detailed Server values, as they can reveal information that may make it (slightly) easier for attackers to exploit known security holes.

**Reference:**
https://httpwg.org/specs/rfc7231.html#header.server

_____________________________________________


# Apache

**Verify:**

Verify the ServerTokens and ServerSignature directive is present in the Apache configuration (e.g: httpd.conf) and has a value of Prod or ProductOnly and Off.

**Remediation:**

Add or modify the ServerTokens and ServerSignature directives as shown below to have the value of Prod or ProductOnly and Off:

```ServerTokens Prod```
```ServerSignature Off```
 
**TL;DR:**

- ServerTokens Prod: This will configure Apache not to send any version numbers in the server response header. The server line will be" Server: Apache." Prod is the value that provides the tiniest information (product name only). If no ServerTokens directive is provided, it is equivalent to ServerTokens Full (with the result being, for example, Server: Apache/2.4.2 (Unix) PHP/4.2.2)
- ServerSignature Off: This will ensure that Apache does not display the server version in the footer of server-generated pages. Note that this is the default setting.
- The above solution would still not allow you to hide that you are using Apache since the Server HTTP header will still say Apache. However, you can change it to whatever you want using modSecurity.

**References:**
https://httpd.apache.org/docs/2.4/mod/core.html#servertokens
https://httpd.apache.org/docs/2.4/mod/core.html#serversignature

**Demonstration:**
- Clone this repository.
- `$ docker build -t httpd:zerobanner - < httpdDockerfile`
- `$ docker run -it -p 8080:80 httpd:zerobanner`
- `$ telnet localhost 8080`

# Apache Tomcat


**Verify:**
In $CATALINA_HOME/conf/server.xml, check for the server directive as shown below in the tag "Host".

```<Valve className="org.apache.catalina.valves.ErrorReportValve" showReport="false" showServerInfo="false"/>```

**Remediate:**
In $CATALINA_HOME/conf/server.xml, add the server directive to the <Host> as shown below.

```<Valve className="org.apache.catalina.valves.ErrorReportValve" showReport="false" showServerInfo="false"/>```

**Demonstration:** 
- Clone this repository.
- `$ docker build -t tomcat:zerobanner - < tomcatDockerfile`
- `$ docker run -it -p 8080:8080 tomcat:zerobanner /bin/bash -c "/usr/local/tomcat/bin/catalina.sh run"`
- `$ telnet localhost 8080`

**References:**
https://tomcat.apache.org/tomcat-9.0-doc/security-howto.html#server.xml
https://stackoverflow.com/questions/52637285/replacing-server-header-in-tomcat-with-sed


# Nginx 

**Verify and Remediate:**
In nginx.conf, check for the server directive as shown below. If the directive is different or not present, then it must be remediated. This directive disables emitting the nginx version on error pages and the “Server” response header field.
```server_tokens off```

**References:**
http://nginx.org/en/docs/http/ngx_http_core_module.html#server_tokens


**Demonstration:**
- Clone this repository.
- `$ docker build -t nginx:zerobanner - < nginxDockerfile`
- $ docker run -it -p 8080:80 nginx:zerobanner
- $ telnet localhost 8080


# IIS

**Verify and Remediate:**

- Open "Internet Information Services (IIS) Manager".
- If you want to set the settings globally, click on your main server node: select iis node
- Open the "Configuration Editor" open configuration editor
- To remove 'x-aspnet-version' response header, go to system.web >> httpRuntime >> enableVersionHeader and set it to 'false' disable server response header
- To remove the IIS 'server' response header, go to system.webServer >> security >> requestFiltering  >> removeServerHeader and set it to 'true' remove IIS server header 

**References:**

https://docs.microsoft.com/en-us/iis/configuration/system.webserver/security/requestfiltering/
https://techcommunity.microsoft.com/t5/iis-support-blog/remove-unwanted-http-response-headers/ba-p/369710
