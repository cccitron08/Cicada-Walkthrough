<img width="260" height="121" alt="icon" src="https://github.com/user-attachments/assets/c178e8aa-e4aa-4e82-84f9-8ca0cb8db61e" />

---

Walkthrough

We start with an nmap scan. From the output we can tell the IP is an Active Directory domain controller and that port `445 (SMB)` is open among other interesting ports.

<img width="998" height="856" alt="nmap" src="https://github.com/user-attachments/assets/1b8daacc-8413-4071-a70e-059aee6f968c" />

---

Next, we enumerate SMB shares using `smbclient` or `crackmapexec` with the `--shares` flag. We find some interesting shares and an unusual one called `HR` that we can access by guessing the username guest with an empty password. (That was a random guess I tried, but it’s also possible to enumerate guest and Administrator with kerbrute.)

<img width="1270" height="468" alt="shares1" src="https://github.com/user-attachments/assets/0fc1264c-7e0f-48a7-b2a6-8e79ebb4febd" />

---

Inside `HR` we find a file called `Notice from HR.txt`. We download it with get and find credentials — a default password. Now we perform a `password‑spraying attack`, but we need user names first. To gather names we use `lookupsid.py`.

<img width="935" height="216" alt="smbclient1" src="https://github.com/user-attachments/assets/9d1570cb-c706-4d26-bd2c-b6c97a12eb0b" />
<img width="1500" height="387" alt="notice" src="https://github.com/user-attachments/assets/d125518c-d5ab-425e-bfc9-b738696169e9" />

---

lookupsid.py asks a Windows server to translate `SID`s into readable `user`/`group`/`machine` names and shows the `domain SID` so you can derive common RIDs. We found many users and groups, filtered them, and wrote them to `names.txt`.

<img width="999" height="876" alt="names" src="https://github.com/user-attachments/assets/0570a464-3e2b-4232-b81a-61d39545219d" />

--

We run password spraying with `crackmapexec` (I won’t explain the tool here — see the project page: https://github.com/byt3bl33d3r/CrackMapExec)

<img width="1266" height="174" alt="spray" src="https://github.com/user-attachments/assets/674cde78-e5ea-46f5-9504-4d0e9a9e12d2" />

--

Password spraying succeeds: the password belongs to `michael.wrightson`. From there we continue enumerating users for `badPwdCount` and `description`. In description we find another cleartext password for `david.orelious`.

<img width="1434" height="216" alt="creds1" src="https://github.com/user-attachments/assets/79a45019-600e-4b6d-82c4-cb8f70edef65" />

---

Using that password we again use `crackmapexec` to check share access for the new user. We have `READ` access to the `DEV` share. Using smbclient we access a `.ps1` file in the share, cat it, and find a cleartext password for `emily.oscars`.

<img width="1273" height="726" alt="shares2" src="https://github.com/user-attachments/assets/26cbd241-a58c-4050-8032-49f33d2d5039" />

---

We immediately use `evil-winrm` (which creates a remote session over WinRM on `port 5985`). After connecting, `whoami /priv` shows we have `SeBackupPrivilege` and we obtain the `user flag`. With SeBackupPrivilege we can back up hive files — specifically `SYSTEM` and `SAM` — which are useful for a `Pass‑the‑Hash attack` if we can extract the `Administrator NTLM hash`.

<img width="1055" height="793" alt="flag_user" src="https://github.com/user-attachments/assets/c9e422f1-248f-439c-b8e2-457d9a556b8d" />

---

We save the hives locally using:

`reg save hklm\sam sam`
`reg save hklm\system system`

(You should read a doc about what these are to understand them better.) We download both files to our attack box.

---

Because `SAM` does not contain plaintext hashes and is encrypted with information in `SYSTEM`, we need a tool to decrypt and format the hashes. We use `secretsdump.py`, which automates extracting the boot key from `SYSTEM`, decrypting `SAM`, and printing the hashes in a usable format. (Don’t forget the `local` flag when running it against local hive files.)

<img width="864" height="290" alt="hash" src="https://github.com/user-attachments/assets/f89ca897-91e0-42a8-a760-d2c657b3c711" />

---

We obtain the `Administrator NTLM` hash and use it for a `Pass‑the‑Hash` attack with `evil-winrm` again, this time for user administrator. You should learn more about the tool output, how the hash is constructed, and why we used the NT hash rather than the LM hash. We pass the hash with the `-H` flag, successfully log in as administrator, and capture the `root` flag.

<img width="1055" height="210" alt="root_flag" src="https://github.com/user-attachments/assets/c29f8c15-72cc-474d-9673-482454283bfc" />

---

Thank you :3 - cccitron

<img width="690" height="622" alt="pwn" src="https://github.com/user-attachments/assets/3371b914-8727-4eb4-9dc7-c772a03a9477" />







