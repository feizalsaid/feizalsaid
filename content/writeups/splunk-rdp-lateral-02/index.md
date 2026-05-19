Scenario: finance subnet shows a burst of authentication failures followed by a single successful RDP session that fans out to three internal workstations within twelve minutes. Goal — separate helpdesk noise from staged lateral movement and produce an escalation-ready timeline.

Add dashboard screenshots as `![Splunk timeline](./timeline.png)` in this folder.

## Phase 1 — anchor the user session

Indexed Windows Security events (4688/4624/4672) and Splunk CIM fields for `src_ip`, `user`, and `Logon_Type`. First pivot: isolate `Logon_Type=10` (RDP) successes where workstation name changes per event but the credential stays constant.

```spl
index=wineventlog EventCode=4624 Logon_Type=10 user="FIN\\svc_deploy"
| stats earliest(_time) as first_seen latest(_time) as last_seen values(Workstation_Name) as hops count by src_ip
| where mvcount(hops) > 2
```

## Phase 2 — parent/child integrity

4688 chains showed `explorer.exe` spawning `powershell.exe` with `-enc` — uncommon for the svc_deploy persona. Cross-indexed proxy logs: no matching update traffic; this was not patch tooling.

```spl
index=wineventlog EventCode=4688 user="FIN\\svc_deploy" process_name="powershell.exe"
| rex field=process "(?i)-enc\s+(?<b64>[A-Za-z0-9+/=]{32,})"
| eval dec_len=len(b64)
| where dec_len > 400
| table _time host parent_process_name process dec_len
```

## Escalation package

- **Timeline**: first RDP success → PS encoded execution → secondary RDP to two new hosts.
- **Hypothesis**: compromised service account or leaked cred material; not VPN lockout noise.
- **Containment asks**: disable svc_deploy interactive logon, revoke sessions, snapshot triage hosts, preserve Security.evtx + Sysmon if enabled.

This mirrors the discipline from simulated enterprise investigations — narrative clarity beats raw event dumps.
