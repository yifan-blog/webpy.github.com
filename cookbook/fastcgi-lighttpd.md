---
layout: default
title: Webpy + LightTTPD with FastCGi
---

# Webpy + LightTTPD with FastCGi

The following applies to lighttpd version 1.4.18

Earlier version may organize the xxx.conf files differently but the same principles applied to them as well.

Below are instructions for the following files:  
  * lighttpd.conf  
  * mod_rewrite.conf  
  * code.py  

<code>/etc/lighttpd/lighttpd.conf</code>

<pre>
server.modules              = (
            "mod_access",
            "mod_alias",
            "mod_accesslog",
            "mod_compress",
)
server.document-root       = "/path/to/webpy/app/root-dir"
</pre>

In my case I used postgresql and therefore runs lighttpd as postgres in order to grant permissions to the database, therefore I added the line:

<pre>
server.username = "postgres"
</pre>

<code>conf-enabled/10-fastcgi.conf</code>

<pre>
server.modules   += ( "mod_fastcgi" )
server.modules   += ( "mod_rewrite" )

 fastcgi.server = ( "/code.py" =>
 (( "socket" => "/tmp/fastcgi.socket",
    "bin-path" => "/path/to/code.py",
    "max-procs" => 1,
   "bin-environment" => (
     "REAL_SCRIPT_NAME" => ""
   ),
   "check-local" => "disable"
 ))
 )

 url.rewrite-once = (
   "^/favicon.ico$" => "/static/favicon.ico",
   "^/static/(.*)$" => "/static/$1",
   "^/(.*)$" => "/code.py/$1",
 )
</pre>

<code>/code.py</code>
at the top of the file add:

<pre>
#!/usr/bin/env python
</pre>