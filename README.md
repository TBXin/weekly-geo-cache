**English** | [Русский](README.ru.md)

# geo-mirror

A weekly cache of geo files for Xray / Happ.

The upstream repositories rebuild their `.dat` files every day, which makes clients re-download them daily. This repository takes a snapshot of upstream once a week and publishes it as a release — the links below are stable and change only once per week.

## Direct links to the current files

Use these in your client instead of the upstream URLs:

| File | URL |
|------|-----|
| `geoip.dat` | `https://github.com/TBXin/geo-mirror/releases/latest/download/geoip.dat` |
| `geosite.dat` | `https://github.com/TBXin/geo-mirror/releases/latest/download/geosite.dat` |
| `checksums.txt` | `https://github.com/TBXin/geo-mirror/releases/latest/download/checksums.txt` |

`releases/latest/download/` always points at the most recent published release and returns a `302` to GitHub's CDN, so the client must follow redirects (Xray and Happ do).

## Sources

The files are mirrored verbatim from:

- **geoip.dat** — [runetfreedom/russia-blocked-geoip](https://github.com/runetfreedom/russia-blocked-geoip)  
  `https://raw.githubusercontent.com/runetfreedom/russia-blocked-geoip/release/geoip.dat`
- **geosite.dat** — [runetfreedom/russia-blocked-geosite](https://github.com/runetfreedom/russia-blocked-geosite)  
  `https://raw.githubusercontent.com/runetfreedom/russia-blocked-geosite/release/geosite.dat`

Nothing is modified — each release is a byte-for-byte copy of upstream at the time the snapshot was taken.

## How it works

A GitHub Actions workflow ([`.github/workflows/mirror.yml`](.github/workflows/mirror.yml)):

1. Runs every Monday at 03:00 UTC.
2. Downloads both files from the upstream repositories.
3. Verifies they aren't empty or an HTML error page, then computes SHA-256 sums.
4. Publishes a release tagged by date (e.g. `2026.08.31`) with `geoip.dat`, `geosite.dat` and `checksums.txt` attached.
5. Updates `last-sync.txt` so the repository doesn't go stale.

To refresh manually: **Actions → Weekly geo mirror → Run workflow**.

## Verifying integrity

```bash
curl -fsSLO https://github.com/TBXin/geo-mirror/releases/latest/download/geoip.dat
curl -fsSLO https://github.com/TBXin/geo-mirror/releases/latest/download/geosite.dat
curl -fsSL  https://github.com/TBXin/geo-mirror/releases/latest/download/checksums.txt | sha256sum -c -
```

## Notes

- GitHub Actions schedules run in UTC and may be delayed by 10–60 minutes; under heavy load a run can be skipped entirely. That's fine for a weekly snapshot.
- If no workflow runs for 60 days, GitHub disables the schedule automatically. The `last-sync.txt` commit on every run prevents that.
- Releases are never marked as pre-release — `latest/download/` ignores pre-releases and the link would get stuck on an older one.
- Every snapshot is kept, so if upstream ever ships a broken file you can pin an earlier release from [Releases](../../releases).

## License

This repository contains only the automation. The `.dat` files remain the property of their upstream authors and are distributed under their respective licenses.