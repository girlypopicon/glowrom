# glowrom for Linux (beta)

glowrom is a media center you run from a terminal: browse your own video library and play it on a TV. It skips intros and
credits for you, looks up descriptions, keeps your files tidy, and has a phone remote. This is the Linux build; it is a beta
and suits people who are comfortable with a terminal. (There is a Windows app too.)

## What you need
- Linux (x86-64 or arm64) with a terminal and `systemd` (for the background parts)
- **mpv** 0.37 or newer, **ffmpeg** (with chromaprint, the Ubuntu/Debian one has it), and `ffprobe`
- your videos in a folder (default `~/media`, with `movies/` and `tv/` inside; or set `MEDIA_ROOT`)
- a free **TMDB API key** from themoviedb.org/settings/api for descriptions and episode names (optional but nice)

## Install
```sh
tar xzf glowrom-linux-amd64.tar.gz -C ~            # makes ~/glowrom
sudo ln -sf ~/glowrom/tvui /usr/local/bin/tvui      # optional: so you can type  tvui
mkdir -p ~/.config/systemd/user
cp ~/glowrom/systemd/tv-skipd.service ~/glowrom/systemd/glowrom.service ~/.config/systemd/user/
```

### The player (mpv), pick one
**On a desktop** (you are logged in and the TV is a screen of this computer): start mpv in your session and leave it running,
for example from your desktop's startup programs:
```sh
mpv --idle=yes --force-window=yes --fullscreen --screen=1 --no-terminal --osc=no --input-ipc-server=/run/user/$(id -u)/mpv.sock
```
(`--screen=1` is the second screen; leave it out to use your main one.) Make sure the audio goes to the TV in your sound settings.

**On a headless box plugged into a TV** (no desktop): use `~/glowrom/systemd/tv-mpv.service.drm-example`. Copy it to
`~/.config/systemd/user/tv-mpv.service`, change the three marked lines for your machine, and run
`loginctl enable-linger $USER` so it starts without you logged in.

### Start the background parts
```sh
systemctl --user daemon-reload
systemctl --user enable --now tv-skipd glowrom        # plus tv-mpv if you made that one
```
Then run `tvui`. Press `,` for Settings (put your TMDB key under Accounts), `?` for info, `m` for a menu on anything.

## Useful
- `tvui version`, `tvui logs`, and `tvui report` (a bug-report file with your keys removed: read it before you share it)
- Settings → System shows what is running and warns about duplicate copies
- The downloader is optional and needs Docker: see the `torrent/` folder, and `VPN-SETUP.md` for setting up your VPN (Mullvad, Proton VPN, NordVPN or any WireGuard VPN). It is the most advanced part; skip it at first.

## About the files in this download
`tvui` is the whole program (the neon font, the idle screen and the phone remote page are inside it). It stores its data
in `~/.config/tvplayer`, `~/.local/share/tvplayer` and `~/.local/state/tvplayer`.
