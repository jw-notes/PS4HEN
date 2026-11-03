<div align="center">
  <img src="goldhen.png" alt="GoldHEN" width="200">
  <h1>PS4HEN</h1>
</div>

Kernel exploit for the PlayStation 4 on firmware **13.52** and **14.00**,
run from the console's own browser. Loads **GoldHEN** into the kernel,
leaving it readable and writable, `uid 0`, `mmap` handing out RWX and
`syscall 11` as a kexec gate — so the usual payloads work as they do after
any other kernel exploit.

> Based on [afterglow](https://github.com/well0nez/afterglow-web) by well0nez/m0zz.
> Modified by **jw-notes** — see `LICENSE` for a full list of changes.

## Requirements

* PS4 on firmware **13.52** or **14.00**, on your network
* A machine on the same network with `python3`

Nothing is installed on the console. The pages are static files.

## Running it

```sh
./serve.sh                      # port 8000, or PS4HEN_PORT=8080 ./serve.sh
```

Open `http://<host>:8000/` in the console's browser and press **start**.
Tick *autostart* and it goes by itself after five seconds.

Each run must begin on the landing page. The run page arms the chain only
when it was reached from there, so a browser restoring its last session
cannot re-trigger it unattended.

## What is shipped

| File | What it is |
|---|---|
| `goldhen.bin` | GoldHEN, included with the author's permission |
| `patchset-1400.bin`, `patchset-1352.bin` | the kernel patches GoldHEN expects to find, one set per firmware |
| `afterglow.js` | original exploit work by well0nez/m0zz |
| `jb.js`, `ps4_offsets.js` | the raw13g host, trimmed |
| `core.js`, `mem.js`, `int64.js` | the in-process primitive |
| `index.html`, `jb.html`, `launch.js`, `style.css`, `goldhen.png` | landing page and run page |
| `serve.sh` | the local server, with the headers this needs |
| `cache.appcache`, `make-manifest.sh` | the offline cache and the script that rebuilds it |

## If it stops

Expect to repeat one now and then. Ten hand-run rounds on 14.00 landed nine
times; a failure in the WebKit stage costs nothing, so going again is cheap.

The page tells you which of these happened.

| What it says | What it cost | What to do |
|---|---|---|
| `exploit failed - please retry (reopen browser)` | nothing — no kernel byte written | reopen the browser and go again |
| nothing at all for 45 s | same | cold start |
| `the handles are NOT defused` | the chain got far and could not clean up | **do not close the browser** — cold start |

That last one matters. With the handles left armed, the next process exit runs
`fo_close` into `apmclose` and never completes — closing the browser *is* that
exit, so no browser restart will help. Cold start only.

## Credits

* **well0nez / m0zz** — original [afterglow](https://github.com/well0nez/afterglow-web) exploit
* **raw13g** — [raw13g.github.io](https://github.com/raw13g/raw13g.github.io),
  the host this page grew out of (`jb.js`, `ps4_offsets.js`, the page
  scaffolding), MIT, see `LICENSE-raw13g`
* **aminegold0** — [silverps4hen](https://github.com/aminegold0/silverps4hen),
  the rebuilt version of that host this one descends from
* **jordyidk** — [slopkit](https://github.com/jordyidk/slopkit), the PS5
  jailbreak the in-process primitive (`core.js`, `mem.js`, `int64.js`) descends from
* **al-azif** — <https://github.com/al-azif/>
* **SiSTRo** — GoldHEN
* **Pharaoh2k**
* **TheFlow, ChendoChap, abc, AlAzif, LightningMods, @egycnq** — the PS4 kernel
  patch and sandbox escape lineage these payloads build on
* **jw-notes** — UI modifications, GoldHEN-only mode, PS4HEN branding

## Legal

Security research on hardware we own. No Sony code is redistributed.

See `LICENSE` for the full custom notice covering original authorship,
third-party components, and modifications.
