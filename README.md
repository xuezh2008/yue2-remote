# YuE2 Remote

A phone client for a [YuE2](https://github.com/multimodal-art-projection/YuE) music server running on
your own machine. One static HTML file, no build step, no backend of its own — it is hosted on GitHub
Pages and talks to *your* PC over your Wi-Fi.

**Live:** https://xuezh2008.github.io/yue2-remote/

The heavy lifting stays on the GPU at home; GitHub only ever serves this one file.

It covers what the desktop UI does, rearranged for a thumb:

| Tab | What it covers |
|---|---|
| Create | style + lyrics, planning mode (`full`/`melody`/`off`), seed, variations, name, plan-only output, guidance (`cfg_scale`), your own ABC score, and per-stage planner/song sampling overrides |
| Score | paste or load a score (from an example or any library item), inspect it, strip chords, compare melody/meter invariants against what you loaded, send it to Create as a melody cover; transcription from audio when SheetSage2 is set up |
| Library | play, filter, reuse settings, edit the score, full details (decoder, timings, token counts), render a saved plan again with a new seed or guidance, re-decode cached latents with the other decoder, blind A/B two songs, build the offline listening page, download, delete |
| Settings | server address and token, decoder / backend / quantization / memory budget / flow-matching steps / AR offload (sent with every job you start), GPU and model status, free GPU memory |

The Cover tab's audio transcription needs SheetSage2 on the server; without it the Score tab says so and
the paste-an-ABC route still works. The desktop UI remains the better place for long score editing.

## Using it

1. Start the server on the PC with the GPU:

   ```
   webui\start-remote.bat
   ```

   The banner prints the address to use, e.g. `https://192.168.68.125:7860/`.

2. On the phone, open the page above → **Settings** → type that address → **Connect**.
   The address is remembered on the device. You can also link straight to it:
   `https://xuezh2008.github.io/yue2-remote/?server=192.168.68.125:7860`

3. The first time, the phone shows a certificate warning — the server signs its own certificate,
   because nobody can issue a trusted one for a private `192.168.x.x` address. Open
   `https://192.168.68.125:7860/` once, tap **Advanced → Proceed**, then go back to the app.

### If the certificate is more trouble than it is worth

Some phones will not be talked through that warning. Safari on iOS in particular may accept the
certificate for a direct visit and still refuse a call made to it *from* this page.

So `--tls` also serves plain http on the next port up, with no certificate anywhere in the way:

```
http://192.168.68.125:7861/m
```

Exactly the same file, served from the PC instead of from GitHub. This page offers it as a one-tap
button whenever it cannot reach the server over https. Both ports need a firewall rule.

## Publishing changes

This folder *is* the repository behind the Pages site, and the YuE2 server serves the same
`index.html` at `/m`. So there is only ever one copy to edit:

```
git -C webui/remote commit -am "…" && git -C webui/remote push
```

GitHub Pages redeploys in under a minute.

## Security

The server has no password unless you start it with `--token`, and `start-remote.bat` lets this page's
origin call it. Anyone else on the same Wi-Fi can therefore generate songs and delete results. On a
network you do not control, start it with a shared secret instead:

```
python webui/server.py --lan --tls --cors https://xuezh2008.github.io --token SECRET
```

and paste the same secret into the app's **Token** box.
