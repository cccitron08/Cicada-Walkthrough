<img width="285" height="121" alt="icon" src="https://github.com/user-attachments/assets/86a31e60-803e-4fdd-95e2-8afb4ed63a69" />
Walkthrough — `Jerry`

I found a http-proxy on port 8080, visited http://<ip>:8080, found myself a Tomcat Manager. 
Authenticated to the Tomcat Manager using the creds tomcat:s3cret, and used Metasploit’s multi/http/tomcat_mgr_upload module to upload a malicious .war. The uploaded WAR executed on the server and gave a SYSTEM-level session. From that session I recovered both user and root flags that were sitting in the Administrator directory.

What I discovered first

A web service was running on 8080 and the Tomcat Manager UI was reachable at /manager/html. When I navigated there, the page prompted for a username and password.
<img width="530" height="137" alt="nmap" src="https://github.com/user-attachments/assets/c0531a73-03c0-4216-bbb0-c522aa759b71" />

<img width="1430" height="475" alt="tomcatversion" src="https://github.com/user-attachments/assets/cb70940b-c152-433d-97d2-2838b4769f0d" />

Which i found out to be tomcat / s3cret. Logging in revealed the manager interface and the deploy/upload capability.

<img width="1300" height="420" alt="creds" src="https://github.com/user-attachments/assets/86a5d8f2-e0e4-46cf-a7cf-4b83d3fc52b2" />

What a .war is (very simply)

A .war (Web Application Archive) is just a zip-style package that contains a Java web application — its code, resources, and configuration. Tomcat can accept a WAR and automatically unpack and run it as a web app. That “automatic unpack-and-run” behavior is exactly what makes WAR uploads valuable to an attacker: if you can upload a WAR you can get your code executed by the server.

<img width="1174" height="195" alt="war" src="https://github.com/user-attachments/assets/e178853d-51cb-4d33-86a5-4424686175e4" />

How the compromise happened

I used Metasploit’s multi/http/tomcat_mgr_upload module to take advantage of the manager’s upload feature. With the manager credentials in hand, the module uploaded a crafted WAR which Tomcat deployed and executed. I didn’t need to do any manual post-upload steps in the web UI — the module handled the upload and triggered the payload. The resulting session showed the server identity as SYSTEM, giving full control over the host.
<img width="760" height="521" alt="msfconsole" src="https://github.com/user-attachments/assets/a12d1213-79fc-4495-b43c-713d25235d14" />

<img width="585" height="131" alt="set1" src="https://github.com/user-attachments/assets/4a9bf838-bcb3-4c0d-af67-1504bf7ba4a5" />

<img width="828" height="205" alt="set2" src="https://github.com/user-attachments/assets/79ed28fd-3cf8-4465-852d-c3872ee0ba79" />

What I found on the host

Once the session was established I checked the Administrator directory and recovered both the user and root flags located there. (Screenshots show the manager page, deployment confirmation, session identity, and the flags — redact flag values if you publish publicly.)

<img width="371" height="78" alt="system" src="https://github.com/user-attachments/assets/aa07849d-3d0f-4d43-b286-85366359c4d0" />

<img width="631" height="254" alt="flags" src="https://github.com/user-attachments/assets/dec166fd-6377-4987-9ade-14b3816f581a" />
Why this worked (in plain terms)

The Tomcat Manager was exposed and accessible.

Valid manager credentials were available.

The manager allowed WAR uploads and Tomcat automatically ran uploaded apps.

The uploaded WAR contained code compatible with the host, so it executed and gave a high-privilege shell.

Impact

Anyone who can upload and deploy a WAR on this Tomcat instance can run arbitrary code as the Tomcat process — on this host that led directly to SYSTEM-level compromise and access to all files, including the flags.

Quick mitigation notes

Don’t expose the Tomcat Manager publicly; restrict it to admin-only networks.

Use strong, unique credentials for manager access and rotate them regularly.

Disable remote deployment in production, or require stricter controls around who can deploy.

Run Tomcat with the least privileges possible so an exploited app can’t fully compromise the OS.

Monitor and alert on manager uploads and unusual deployments.

<img width="696" height="618" alt="finish" src="https://github.com/user-attachments/assets/f43448b0-86cc-4e18-8b26-79dff30041dd" />
