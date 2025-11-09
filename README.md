# NerdQAxe / BitAxe Mining Dashboard — Adapted for your configuration

This repository contains an adapted Home Assistant configuration for monitoring BitAxe compatible miners. The files and structure match your local setup:

- `configuration.yaml` — Template sensors (total hashrate, power, uptime, acceptance/rejection rates, etc.).
- `miner.yaml` — REST sensors for each miner (NerdQAxe, NerdAxe, Supra, Gamma) and pool REST endpoints (CKPool, Satoshiradio).
- `secrets.yaml` — IP addresses and pool/wallet secrets (referenced with `!secret` in `miner.yaml`).

This README explains installation, customization and common troubleshooting steps targeted to these files.

## Quickstart — Installation

1. Backup: Make a backup copy of your Home Assistant `config` folder.
2. Copy files: Copy `configuration.yaml`, `miner.yaml` and `secrets.yaml` into your Home Assistant config directory. If you already have `configuration.yaml`, include only the appropriate sections or use `!include`.
3. Secrets: Edit `secrets.yaml` and set your miner IPs and wallet address. Example:

```yaml
nerdqaxe_ip: "http://192.168.178.58/api/system/info"
wallet_address: "bc1q..."
ckpool_resource: "https://eusolo.ckpool.org/users/bc1q..."
```

4. Check: In Home Assistant → Settings → System → Server Controls → Check Configuration.
5. Restart: If the check succeeds, restart Home Assistant.

## File overview

- `miner.yaml`
  - Contains `rest:` blocks per miner. Each block queries the miner API (`/api/system/info`) and defines multiple `sensor:` entries.
  - Each sensor has a `unique_id` (e.g. `nerdqaxe_hashrate`) — important for stable Entity Registry entries.
  - Pool sections: CKPool user stats and pool stats are present. CKPool `pool.status` returns three JSON lines — templates parse this using `value.splitlines()` + `from_json`.

- `configuration.yaml` (Template sensors)
  - Aggregates miner REST sensors into totals (Total Power, Total Hashrate, Efficiency W/TH, Uptime, Overheat count).
  - Includes Acceptance/Reject Rate sensors for NerdQAxe, NerdAxe, Supra, Gamma, CKPool and Satoshiradio.
  - All template sensors now include `unique_id` and human-friendly `name` fields.

## Customization

- Removing a miner: If you remove a miner, also remove the corresponding `rest` block in `miner.yaml` and adjust or comment out the template sensors in `configuration.yaml`.
- Units: Pool hashrates are reported in different units (GH/s, TH/s, PH/s). Templates convert where necessary — adjust if you prefer different display units.

## Troubleshooting

- `Check Configuration` errors:
  - Template errors: Copy the failing template into Developer Tools → Template and iterate until it produces the expected result.
  - REST timeouts: Verify the miner IPs and firewall settings, and test the URL (`/api/system/info`) from the Home Assistant host.

- Missing sensors:
  - Verify `secrets.yaml` has correct IPs and pool URLs
  - Check Home Assistant logs for REST or template error messages

## Recommendations

- Naming convention: The files use `unique_id` prefixes like `bitaxe_`, `nerdqaxe_`, and `ckpool_`. Adjust these if you want a different naming scheme.

---
Short: main files are `configuration.yaml`, `miner.yaml` and `secrets.yaml`. Put your miner IPs and wallet in `secrets.yaml`, check configuration in HA and restart.

<img width="992" height="992" alt="AxeBTC" src="https://github.com/user-attachments/assets/7760dab0-b33f-4529-aa00-bc3112e1964a" />
