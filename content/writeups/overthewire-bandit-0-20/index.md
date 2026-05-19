# OverTheWire: Bandit Levels 0 to 20 Walkthrough

Bandit is an essential capture-the-flag (CTF) wargame hosted by OverTheWire. It is designed to build a practical understanding of the Linux command line, file manipulation, pipeline text processing, and basic administrative security concepts. 

This comprehensive writeup breaks down the methodologies used to solve Levels 0 through 20.

---

## Host Connection Details
* **Host:** `bandit.labs.overthewire.org`
* **Port:** `2220`

---

## Level 0
**Objective:** Log into the game server using SSH.
* **Username:** `bandit0`
* **Password:** `bandit0`

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

---

## Level 0 → 1
**Objective:** Find the password for the next level stored in a file named `README` in the home directory.
* **Methodology:** Use `ls` to locate the target and `cat` to read its contents.

```bash
ls
cat README
```
* **Flag discovered:** `boJ9jbbUNNfktd78OOpsqOltutMc3MY1`

---

## Level 1 → 2
**Objective:** Find the password inside a file named `-` located in the home directory.
* **Methodology:** A single dash `-` represents standard input/output in many Linux utilities. To force `cat` to read it as a specific filename, pass an explicit relative path `./-`.

```bash
cat ./-
```

---

## Level 2 → 3
**Objective:** Find the password inside a file named `spaces in this filename`.
* **Methodology:** Use double quotes or backslash escaping to pass filenames containing whitespace characters as a single operational parameter.

```bash
cat "spaces in this filename"
```

---

## Level 3 → 4
**Objective:** Access a hidden file stored inside the `inhere` directory.
* **Methodology:** Move into the folder using `cd` and pass the `-la` flags to `ls` to view hidden files prefixed with a dot (`.`).

```bash
cd inhere
ls -la
cat .hidden
```

---

## Level 4 → 5
**Objective:** Locate the only human-readable file inside the `inhere` directory among multiple corrupted or binary assets.
* **Methodology:** Examine files to locate standard ASCII strings. Checking text types identifies `./-file07` as the destination.

```bash
cd inhere
ls
cat ./-file07
```

---

## Level 5 → 6
**Objective:** Find a file under the `inhere` directory that matches three specific properties: human-readable, 1033 bytes in size, and not executable.
* **Methodology:** Use the `find` utility with criteria constraints for size (`1033c`) and permissions (`! -executable`).

```bash
cd inhere
find . -size 1033c ! -executable
cat ./maybehere07/.file2
```

---

## Level 6 → 7
**Objective:** Find a file hidden somewhere on the entire server belonging to user `bandit7`, group `bandit6`, and exactly 33 bytes in size.
* **Methodology:** Execute a root-level `find` query. Redirect permission warning noise (`Permission denied`) using `2>/dev/null` to keep the console clean.

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
cat /var/lib/dpkg/info/bandit7.password
```

---

## Level 7 → 8
**Objective:** Retrieve the password stored within `data.txt` next to the word "millionth".
* **Methodology:** Pipe the data block directly into `grep` to extract the corresponding target string.

```bash
cat data.txt | grep "millionth"
```

---

## Level 8 → 9
**Objective:** Find the single line of text in `data.txt` that occurs exactly once.
* **Methodology:** The `uniq` command requires sorted adjacent lines to filter effectively. Sort the document stream and isolate unique entities with `uniq -u`.

```bash
cat data.txt | sort | uniq -u
```

---

## Level 9 → 10
**Objective:** Extract human-readable strings from `data.txt` preceded by several `=` characters.
* **Methodology:** Search for explicit string formatting patterns using a simple syntax filter match.

```bash
cat data.txt | grep "="
```

---

## Level 10 → 11
**Objective:** Decode the file `data.txt`, which contains base64 encoded data.
* **Methodology:** Use the system binary tool with the decode flag to process the content.

```bash
base64 -d data.txt
```

---

## Level 11 → 12
**Objective:** Decode `data.txt`, which has been obfuscated using a ROT13 cipher (rotated by 13 positions).
* **Methodology:** Map both uppercase and lowercase character sets using the string translation utility `tr`.

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

---

## Level 12 → 13
**Objective:** Decompress a heavily nested, multi-compressed hex dump file.
* **Methodology:** Work in a workspace subfolder under `/tmp`. Reverse the hex dump with `xxd -r`, determine file signatures iteratively with `file`, and recursively strip layers using `gunzip`, `bzip2`, and `tar`.

```bash
mkdir /tmp/workspace
cp data.txt /tmp/workspace
cd /tmp/workspace

xxd -r data.txt > bandit
file bandit
mv bandit bandit.gz
gunzip bandit.gz

# Repeat decompression layers based on 'file' output:
bzip2 -d bandit
tar xvf bandit.out
# (Continue process until flat ASCII flag appears)
cat data8
```

---

## Level 13 → 14
**Objective:** Log into the next account using an SSH private key stored in `sshkey.private`.
* **Methodology:** Use the identity flag input `-i` on SSH. To fix permission warning errors, restrict key read properties using `chmod`.

```bash
ssh bandit14@localhost -p 2220 -i sshkey.private
```

---

## Level 14 → 15
**Objective:** Submit the current level's password to port 30000 on localhost to unlock the next flag.
* **Methodology:** Retrieve the raw baseline password from `/etc/bandit_pass/bandit14` and establish a direct network link using `nc` (Netcat).

```bash
cat /etc/bandit_pass/bandit14 | nc localhost 30000
```

---

## Level 15 → 16
**Objective:** Submit the current password to port 30001 on localhost using secure SSL/TLS encryption.
* **Methodology:** Use the built-in OpenSSL connection engine helper instead of basic text Netcat.

```bash
openssl s_client -connect localhost:30001 -quiet < /etc/bandit_pass/bandit15
```

---

## Level 16 → 17
**Objective:** Scan ports between 31000 and 32000 on localhost to discover active listeners, identify the SSL service, and submit the password to extract a private key.
* **Methodology:** Run an explicit network reconnaissance trace with `nmap`. Establish a secure OpenSSL stream to the credential engine port to download the private key asset.

```bash
nmap -p 31000-32000 localhost
cat /etc/bandit_pass/bandit16 | openssl s_client -connect localhost:31790 -quiet > /tmp/key/akey
chmod 600 /tmp/key/akey
ssh -i /tmp/key/akey bandit17@localhost
```

---

## Level 17 → 18
**Objective:** Find the line that changed between `passwords.old` and `passwords.new`.
* **Methodology:** Use the structural comparison tool `diff` to pinpoint differences between the old and new files.

```bash
diff passwords.old passwords.new
```

---

## Level 18 → 19
**Objective:** Log into `bandit18`. The account is hard-configured to force a prompt exit and log out immediately upon shell initialization.
* **Methodology:** Bypass default profile script blocks by passing the file command string directly to the tail of your terminal connection invocation.

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
```

---

## Level 19 → 20
**Objective:** Leverage a local binary called `bandit20-do` that runs with elevated SetUID privileges.
* **Methodology:** Running the program executes tasks with the permissions of the `bandit20` user owner, bypassing standard group isolation barriers.

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```
