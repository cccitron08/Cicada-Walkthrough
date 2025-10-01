<img width="260" height="121" alt="icon" src="https://github.com/user-attachments/assets/3bccf137-4638-4165-9c13-2927e7ca061f" />


Machine Info

* **Name:** `Cicada`
* **OS:** `Windows` 
* **Category:** `AD / SMB / Network`
* **Difficulty:** `Easy`
* **Source:** https://app.hackthebox.com/machines/627
* **Date solved:** `2025-09-28`
* **Personal Rating:** `8/10`
---

## Summary

This box is an Active Directory challenge that centers on SMB-based enumeration and lateral movement. The path to root relies on classic AD attacks: SMB enumeration, Pass-the-Hash (PtH), SeBackupPrivilege abuse, and password-spraying. Many credentials are exposed in cleartext — often hidden in file metadata and within shares or AD objects — which accelerates the compromise chain.

The objective is to escalate privileges across five accounts, beginning from a guest-level foothold and ending at a domain administrator. Overall it’s straightforward for anyone familiar with AD and common SMB/Windows attack techniques.

---

## Highlights

Heavy focus on SMB (shares, SYSVOL/NETLOGON, and metadata leaks).

Kerberos/NTLM vectors are relevant — PtH and password-spraying are practical here.

SeBackupPrivilege abuse is a key privilege-escalation mechanism on this box.

Multiple cleartext passwords are available in share files and object metadata, making enumeration very productive.

The box rewards standard AD knowledge and tooling rather than obscure exploits.

---

## Opinion / Rating

8/10 — Fun and well-constructed. The privilege-escalation chain is clear and relies on expected AD concepts, which makes it accessible. I’d have preferred more emphasis on cracking or dealing with hashes (e.g., include hashes to crack instead of plain cleartext) to increase difficulty and learning value.

## Open services (important ones)

88 - Kerberos
135 - MSRPC
139 - NetBIOS-SSN
389 - LDAP
445 - Microsoft-DS (SMB)

---

## Contact / Credits

* **Lab author:** `theblxckcicada`
* **Author link:** https://app.hackthebox.com/users/796798
