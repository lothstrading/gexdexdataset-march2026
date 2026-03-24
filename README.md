# SPX GEX/DEX By-Strike Snapshots — March 2026

Intraday snapshots of SPX options **Gamma Exposure (GEX)** and **Delta Exposure (DEX)** by strike price, captured during March 11–24, 2026.

Data source: [OptionsCharts](https://optionscharts.io), SPX nearest-expiry contract.

---

## Contents

| Folder | Files | Description |
|--------|-------|-------------|
| `gamma/` | 822 snapshots | GEX (Gamma Exposure) by strike |
| `delta/` | 830 snapshots | DEX (Delta Exposure) by strike |

Snapshots are taken at irregular intraday intervals throughout the trading session, roughly every 4–5 minutes during market hours.

---

## File Naming

```
gamma/gex_exposure_by_strike_$SPX_<TIMESTAMP>Z.csv
delta/dex_exposure_by_strike_$SPX_<TIMESTAMP>Z.csv
```

`<TIMESTAMP>` is UTC in `YYYYMMDDTHHMMSSz` format. Example:

```
gex_exposure_by_strike_$SPX_20260311T134228Z.csv  →  2026-03-11 13:42:28 UTC
```

---

## Schema

### `gamma/` — GEX by Strike

| Column | Type | Description |
|--------|------|-------------|
| `strike` | float | SPX option strike price |
| `call_gex` | float | Gamma exposure from call options at this strike ($ millions) |
| `put_gex` | float | Gamma exposure from put options at this strike ($ millions) |
| `net_gex` | float | Net GEX = call_gex + put_gex |
| `gex_profile` | string | Optional profile label (may be empty) |

### `delta/` — DEX by Strike

| Column | Type | Description |
|--------|------|-------------|
| `strike` | float | SPX option strike price |
| `call_dex` | float | Delta exposure from call options at this strike |
| `put_dex` | float | Delta exposure from put options at this strike |
| `net_dex` | float | Net DEX = call_dex + put_dex |

Each file contains ~224 rows covering strikes across the full SPX options chain.

---

## What Is GEX / DEX?

**Gamma Exposure (GEX)** measures how much market makers must delta-hedge as spot price moves through each strike. Large positive GEX at a strike acts as a magnet/pin (dealers sell into rallies, buy dips). Large negative GEX amplifies moves (dealers add fuel to directional moves).

**Delta Exposure (DEX)** measures the total directional exposure (in underlying notional) held by market makers at each strike. It indicates where dealer hedging flows are concentrated.

These are commonly used to identify:
- **GEX walls** — strikes where price tends to stall or pin
- **Negative GEX zones** — strikes where volatility tends to expand
- **DEX clusters** — areas of concentrated dealer hedging activity

---

## Date Range

- **Start:** 2026-03-11
- **End:** 2026-03-24
- **Coverage:** US market hours (approximately 09:30–17:00 ET)

---

## Usage Example (Python)

```python
import pandas as pd
import glob

# Load all GEX snapshots
files = sorted(glob.glob("gamma/gex_exposure_by_strike_*.csv"))
snapshots = {}
for f in files:
    ts = f.split("$SPX_")[1].replace("Z.csv", "")
    snapshots[ts] = pd.read_csv(f)

# Get net GEX at a specific timestamp
df = snapshots["20260311T134228"]
print(df.sort_values("net_gex", ascending=False).head(10))
```

---

## License

Data sourced from OptionsCharts. Shared for research and educational purposes.
