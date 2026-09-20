# YuE2 Remote

A phone client for a [YuE2](https://github.com/multimodal-art-projection/YuE) music server running on
your own machine. One static HTML file, no build step, no backend of its own — it is hosted on GitHub
Pages and talks to *your* PC over your Wi-Fi.

**Live:** https://xuezh2008.github.io/yue2-remote/

Create a song, watch the stages tick by, play the result, browse and delete the library. The heavy
lifting stays on the GPU at home; GitHub only ever serves this one file.

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

Browsers refuse to let an `https://` page call a plain `http://` address, which is the whole reason
the server speaks TLS. The way around it is to let the server hand out the app itself:

```
http://192.168.68.125:7860/m
```

Exactly the same file, served from the PC instead of from GitHub — no certificate, no warning. The
GitHub copy detects the block and offers this as a one-tap button.

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
