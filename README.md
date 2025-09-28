
<img width="304" height="119" alt="jerry-icon" src="https://github.com/user-attachments/assets/05f07086-6d4d-43d0-a4a5-76810eba8941" />


Machine Info

* **Name:** `Jerry`
* **OS:** `Windows` 
* **Category:** `Web`
* **Difficulty:** `Easy`
* **Source:** https://app.hackthebox.com/machines/144
* **Date solved:** `2025-09-27`

---

## Summary
Scanned the host, discovered Apache Tomcat on port 8080. 
Using discovered credentials (tomcat:s3cret) I logged into the Tomcat Manager, 
uploaded a malicious WAR via Metasploit multi/http/tomcat_mgr_upload, and gained SYSTEM on the Windows host.
User/System flags were on the Administrator desktop.
## Open services (important ones)

Apache Tomcat 8080

---

## Contact / Credits

* **Lab author:** `mrh4sh`
* **Author link:** https://app.hackthebox.com/users/2570
