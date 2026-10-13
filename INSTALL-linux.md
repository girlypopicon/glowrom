# glowrom for Linux (beta)

glowrom is a media center you run from a terminal: browse your own video library and play it on a TV. It skips intros and
credits for you, looks up descriptions, keeps your files tidy, and has a phone remote. This is the Linux build; it is a beta
and suits people who are comfortable with a terminal. (There is a Windows app too.)

## Quick install (one line)
```sh
curl -fsSL https://github.com/girlypopicon/glowrom/releases/latest/download/install.sh | sh
```
It downloads the newest build, checks it against its published checksum, unpacks it into `~/glowrom`, puts a `glowrom` command in
`~/.local/bin`, and runs `glowrom setup`, which checks mpv and ffmpeg and sets up the background services. No sudo is needed, and
nothing outside your home folder changes. If mpv or ffmpeg are missing, setup shows the install command and asks first; add
`--install-deps` (after `sh -s --`) to let it install them on Debian, Ubuntu and Arch. Run the same line again later to update, or use
`glowrom update`. To see what it would do without changing anything: `curl -fsSL … | sh -s -- --dry-run`.

The manual steps below are for people who prefer to do it by hand.

## What you need
- Linux (x86-64 or arm64) with a terminal and `systemd` (for the background parts)
- **mpv** 0.37 or newer, **ffmpeg** (with chromaprint, the Ubuntu/Debian one has it), and `ffprobe`
- your videos in a folder (default `~/media`, with `movies/` and `tv/` inside; or set `MEDIA_ROOT`)
- a free **TMDB API key** from themoviedb.org/settings/api for descriptions and episode names (optional but nice)

## Install
```sh
tar xzf glowrom-linux-amd64.tar.gz -C ~            # makes ~/glowrom
sudo ln -sf ~/glowrom/glowrom /usr/local/bin/glowrom   # optional: so you can just type  glowrom
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
Then run `glowrom`. Press `,` for Settings (put your TMDB key under Accounts), `?` for info, `m` for a menu on anything.

## Useful
- `glowrom version`, `glowrom logs`, `glowrom report`, and `glowrom update` (checks for a newer version and installs it; Settings → Accounts does the same) (a bug-report file with your keys removed: read it before you share it)
- Settings → System shows what is running and warns about duplicate copies
- The downloader is optional and needs Docker: see the `torrent/` folder, and `VPN-SETUP.md` for setting up your VPN (Mullvad, Proton VPN, NordVPN or any WireGuard VPN). It is the most advanced part; skip it at first.

## About the files in this download
`glowrom` is the whole program (`tvui` is the same program under its old name, kept so older instructions still work) (the neon font, the idle screen and the phone remote page are inside it). It stores its data
in `~/.config/tvplayer`, `~/.local/share/tvplayer` and `~/.local/state/tvplayer`.
