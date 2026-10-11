# Setting up a VPN for the downloader (Mullvad, Proton VPN, NordVPN, or any WireGuard VPN)

glowrom's downloader is **optional**, and it only ever goes online through a VPN. This page shows how to set up the VPN you
already pay for. You only need to do this once.

**How it protects you:** the downloader lives in its own little container whose only way to the internet is the VPN tunnel.
If the tunnel drops, its traffic **stops** rather than leaking out on your normal connection (a "kill switch"). You can check
at any time: in the app press `,` for Settings, open **VPN for torrents**, and press `c`. It tells you what address the internet
sees for the downloader, and warns you loudly if that is your own.

**Please only download things you have the right to.** Check that your VPN allows torrenting (most paid ones do on some or
all servers).

## Where to start in the app
Press `,` (Settings), then **VPN for torrents**. Pick your VPN from the list:

| You use | Choose | What you need |
|---|---|---|
| Mullvad | 1 or 2 | your 16-digit account number, or a file from Mullvad |
| Proton VPN | 3 | a WireGuard file from your Proton account |
| NordVPN | 4 | an access token from your Nord account |
| Anyone else | 5 | a WireGuard file from your VPN |

The app shows these same steps on screen as you go.

---

## Proton VPN
Torrenting needs a **paid** Proton plan and a **P2P** server (the free plan does not allow it).

1. Sign in at **https://account.protonvpn.com/downloads#wireguard-configuration** (or go to *Downloads*, then *WireGuard configuration*).
2. Give the file a name (anything, like "glowrom"), choose the platform **GNU/Linux**.
3. Pick a server marked **P2P**. Choose a country, then a specific server.
4. Click **Create**, then **Download** (or copy the text on screen).
5. In glowrom choose **3. Proton VPN: paste a WireGuard file**, paste the whole file, press **enter**.
6. Do steps 2 to 5 again for **two or three more servers**. Each file is one server, and glowrom tries the next if one stops working.
7. Press `s` on the VPN screen to start it, then `c` to check it.

*Tip:* if Proton says you've made too many files, delete old ones on the same page.

## NordVPN
Nord does not offer WireGuard files to download. glowrom asks Nord for the same information Nord's own apps use, with an
**access token** you make in your Nord account. Your plan needs to include file sharing (**P2P**) servers.

1. Sign in at **https://my.nordaccount.com/**, open **NordVPN**, then **Set up NordVPN manually**. (The wording on Nord's site changes now and then.)
2. Under **Access token**, choose **Generate new token** and copy it.
3. In glowrom choose **4. NordVPN: use my access token** and paste it. The characters aren't shown.
4. glowrom fetches your WireGuard key and five servers and saves them. **The token itself is used once and never saved.**
5. Press `s` to start it, then `c` to check it.

## Any other VPN that gives you a WireGuard file (IVPN, Windscribe, AirVPN and more)
1. In your VPN's account area look for **WireGuard** (sometimes "manual setup" or "config generator").
2. Download a file for a server that **allows torrenting**.
3. In glowrom choose **5. Another VPN** and paste the whole file. Add a few servers for spares.

## Mullvad
Choose **1** and type your account number: glowrom makes the key and registers this machine as one of your 5 devices (the number
is used once and never saved). Or choose **2** and paste a file from **https://mullvad.net/en/account/wireguard-config**.

---

## If it won't connect
- Press `c` on the VPN screen: it says what it found, and shows the last messages from the VPN.
- Add another server (press `p`, choose your VPN again). A server that is busy or blocked is skipped for the next one.
- Make sure the server you picked allows **P2P / file sharing**: some providers block it on other servers.
- On a slow or unusual connection, a smaller packet size can help. That is an advanced setting (`WG_MTU` in `torrent/.env`).
- To see what the VPN container is doing: `docker logs tv-vpn`.
- `tvui report` writes a bug-report file with your keys removed, to attach to an issue.

## What is saved, and where
Your key and the server files are stored on your own computer, in `torrent/vpn-confs/` (readable only by you). Nothing is sent
to us. To remove them: on the VPN screen press `r`.

## How well is each tested?
- **Mullvad:** used every day by the author.
- **Any provider's WireGuard file (this includes Proton VPN):** tested end to end against a stand-in VPN server: it connects
  from the file, nothing gets out except through the tunnel, the kill switch holds when the tunnel drops, and it reconnects by
  itself. It hasn't yet been run with a real Proton account.
- **NordVPN:** written from Nord's public documentation and tested against a stand-in, **not yet with a live Nord account.**
  If you try it, please tell us how it went.
