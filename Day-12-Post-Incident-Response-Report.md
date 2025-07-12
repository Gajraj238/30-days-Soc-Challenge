# Post-Incident Activity – Malicious Cron Job (Linux IR Lab)

### When was the cron job added?

The cron job was added on:
**Friday, July 11, 2025 at 15:48:56**, as indicated in the `/var/spool/cron/crontabs/root` file.

### What was the script doing?

The script located at `/tmp/malicious.sh` contained the following command:

```bash
echo "Ping from attacker server" >> /tmp/.cron.log
```

### Behavior Observed:

* This script was executed **every minute** by the cron service.
* It continuously appended a fake "ping" message to a hidden log file (`.cron.log`) in `/tmp`.
* While harmless in this simulation, this pattern mimics real-world persistence or beaconing activity used by attackers.

### Any signs of lateral movement or download activity?

### Findings:

* **No evidence** of lateral movement, remote connections, or file downloads was detected in this lab scenario.
* No curl, wget, or outbound traffic was observed during investigation.
* Process listing and logs focused solely on the cron execution behavior.

---

### Recommendations:

* Restrict cron usage to authorized users
* Monitor for scripts or jobs executing from `/tmp`
* Set up alerts for new or modified cron jobs using tools like `auditd` or `inotify`
* Enforce `noexec` mount options on `/tmp` to prevent execution from temporary directories
* Regularly review user crontabs and `/etc/cron*` directories for unauthorized entries
