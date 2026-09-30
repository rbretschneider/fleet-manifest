# fleet-manifest

Machine-readable service manifest consumed by Nectar Mobile applications.

```
https://raw.githubusercontent.com/rbretschneider/fleet-manifest/main/manifest.json
```

Shape: `{"status": "ok|outage|maintenance", "message", "updatedAt"}`.
Apps display `message` in their sync banner when `status != ok`.

Updated automatically by the workflow in this repository.
