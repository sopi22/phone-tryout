# phone-tryout

**Tries things out on a real Android phone instead of trusting what the phone reports.**

Apps often break on some phones even when the phone's own system info (`getprop`,
`dumpsys`, `pm`) says everything should work. phone-tryout runs small, safe test actions on
a connected phone and records whether they **actually** worked, so you can compare phones,
and the same phone before and after an update.

v1 has two tryouts:

| Tryout | What it actually does |
|---|---|
| **Storage write** | Writes one small test file (`/sdcard/pulse_probe_test`) over adb, reads it back, deletes it |
| **Setting survives** | Saves one test value (`pulse_probe_test_key`) in Android's settings over adb and reads it back |

Each run is saved, so the next run can tell you whether a result is **NEW**, **UNCHANGED**
or **CHANGED** compared with last time.

## Why I built it

I wanted to know if "just try it" tells you anything that the phone's system info doesn't.
I wrote down beforehand what would count as yes or no, including deliberately making each
tryout fail to check that a failure really shows up as a failure.

**Result: yes, for these two tryouts.** Repeated over four sessions on three devices: a
Samsung Galaxy A53 (Android 16), a Google Pixel emulator (Android 14) and a Samsung Galaxy
A54 (Android 16), including the deliberate failure cases on all three.

**Still open, not urgent:** testing on a physical phone from a *different maker* (not
Samsung, not an emulator). The A54 helped but is the same brand as the A53.

## Run it

Requires Python 3.10+ and `adb` (Android platform tools) on your PATH, with a phone already
connected (`adb devices` lists it).

```bash
git clone https://github.com/sopi22/phone-tryout.git
cd phone-tryout/pulse
python3 -m venv .venv && source .venv/bin/activate
pip install -e ".[dev]"

pytest                                          # tests (phone tests skip if none connected)
pulse-run --serial <serial-from-adb-devices>    # run both tryouts once
pulse-recheck --serial <serial>                 # run again later and log the comparison
```

The internal package is still called `pulse` (the project's original name).

## Privacy

The tryouts only touch their own test file and their own test setting key. Nothing else on the phone is
read: no photos, contacts, messages or other apps' data, and nothing is sent anywhere.

## What it is not

Not an app store, not a permission manager, not a background service. Nothing runs unless
you start it.

The full lab notebook (every session, what I expected, what happened) is in
[RESEARCH.md](RESEARCH.md).

## Author

Jhoana Sophia Munar ([@sopi22](https://github.com/sopi22))

## License

MIT, see [LICENSE](LICENSE).
