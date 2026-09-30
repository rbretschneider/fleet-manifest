# nectarmobile-status

Off-infrastructure status feed for Nectar Mobile apps. Deliberately hosted
on GitHub so it stays reachable when the home infrastructure is not.

Apps read (open CORS, ~5-minute edge cache):

```
https://raw.githubusercontent.com/rbretschneider/nectarmobile-status/main/status.json
```

Shape: `{"status": "ok|outage|maintenance", "message", "cause", "updatedAt"}`.
Clients show `message` in their sync-unavailable banner when `status != ok`.

## How it updates

- **Cron backstop:** every ~5 minutes a workflow probes
  `https://sync.nectarmobile.dev/health` from GitHub's infrastructure
  (3 tries over ~50s to ignore blips) and flips the status on change.
- **Power self-report:** hawaii's UPS watcher calls, within the on-battery
  grace window:

  ```bash
  curl -s -X POST \
    -H "Authorization: Bearer $STATUS_PAT" \
    -H "Accept: application/vnd.github+json" \
    https://api.github.com/repos/rbretschneider/nectarmobile-status/dispatches \
    -d '{"event_type":"power","client_payload":{"state":"onbatt"}}'
  ```

  and the same with `"state":"online"` when power returns (which triggers a
  health probe rather than blind trust). `$STATUS_PAT` is a fine-grained PAT
  scoped to ONLY this repository with Contents: read/write permission.
- **Manual maintenance:** Actions → Status monitor → Run workflow →
  `mode: maintenance` (+ message). The cron will not override it; clear with
  `mode: auto`.
