Machine Info

* **Name:** `Jerry`
* **IP / Host:** `10.10.10.95'
* **OS:** `Windows` 
* **Category:** `Web`
* **Difficulty:** `Easy`
* **Author / Source:** `https://app.hackthebox.com/machines/Jerry`
* **Date solved:** `2025-09-27

---

## Summary

A Tomcat instance was discovered on port 8080. 
Valid credentials allowed access to the /manager application, where a .war upload feature was used to deploy a payload. 
The metasploit module multi/http/tomcat_mgr_upload was used to upload and execute a WAR, resulting in SYSTEM level access.
---

## Open services (important ones)

8080/tcp — Apache Tomcat (web manager)

---

## Credits

* **Lab author:** `mrh4sh`
* **Author link:** 'https://app.hackthebox.com/users/2570'
