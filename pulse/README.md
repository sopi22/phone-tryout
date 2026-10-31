# pulse: the phone-tryout runner

Runs the two tryouts (`probe_storage_write`, `probe_settings_persist`) on one phone connected
over adb, and saves each result to a local history file so the next run can say whether it
is **NEW**, **UNCHANGED**, **CHANGED** or has **NO_COMPARISON** yet.

See [../README.md](../README.md) for what the project is and why, and
[../RESEARCH.md](../RESEARCH.md) for the full notes. This file only covers running it.

## Needs

- Python 3.10+ (standard library only; `pytest` for tests)
- The `adb` command on your PATH, and a phone already connected (`adb devices` shows it).
  This tool doesn't pair or connect phones itself.

## Setup

```bash
cd pulse
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

## Run the tryouts

```bash
pulse-run --serial <serial-as-shown-by-adb-devices>

# example:
pulse-run --serial 192.168.1.50:5555
```

Each run prints one JSON line per result and adds it to `pulse_history.json` (in the current
folder; change with `--history <path>`).

## Check again later

To see whether anything changed after, say, an Android update, run the same two tryouts
again whenever you like:

```bash
pulse-recheck --serial <serial-as-shown-by-adb-devices>
```

It does everything `pulse-run` does and also adds a dated entry to the re-check log in
`../RESEARCH.md` (change the file with `--research-txt`). Nothing is scheduled or left running.

## Tests

```bash
pytest
```

`test_store.py` needs no phone. `test_probes_live.py` runs the real tryouts on a connected
phone (including one deliberate failure) and is skipped automatically if no phone is connected.
