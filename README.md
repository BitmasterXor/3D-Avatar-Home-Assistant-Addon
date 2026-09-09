# Aria — Home Assistant add-on

A full-body 3D talking avatar for your Home Assistant, powered by the Assist
pipeline you already have: your speech recognition, your language model, your
voice, your house. Runs as a single static binary with the web app embedded —
installing compiles nothing, even on a Raspberry Pi.

**Nothing is fetched from the internet.** Three.js, TalkingHead, the Draco
decoder and the stand-in avatar are all inside the binary. The browser talks to
exactly two things — this add-on and your Home Assistant — so Aria works on an
isolated VLAN with the WAN unplugged, and keeps working when someone else's CDN
has an outage. The wake word is the same story: audio goes to your own
speech-to-text and nowhere else.

## Install

1. In Home Assistant open **Settings → Apps** (called *Add-ons* before
   HA 2026.2) **→ App Store**.
2. Menu **⋮ → Repositories**, paste this repository's URL, **Add**.
3. Refresh the page, find **Aria** under *Aria Add-ons*, click **Install**,
   then **Start**.

## Open it

Aria supports **Ingress**: it appears in the Home Assistant sidebar and opens
inside HA's own HTTPS page — no port, no certificate warning, nothing to
configure. Hide or show the sidebar entry with the standard *Show in sidebar*
toggle on the app's page.

On first open the setup wizard asks for:

- **Home Assistant address** — `http://homeassistant:8123` works from inside
  any add-on; your LAN address (`http://192.168.x.x:8123`) works too.
- **A long-lived access token** — in HA click your name (bottom left) →
  **Security** tab → **Long-lived access tokens** → create one and paste the
  whole string. It is stored on the server, never in the browser.

Drop your own avatar or environment `.glb` files in through the app's
settings panel; they persist in the add-on's `/data` across updates.

## Talking to her

Tap the microphone button once and speak. When you stop, the turn is sent —
there is nothing to hold down. Tap again to send immediately, or to stop her
mid-answer. **Settings → Voice** tunes how long a pause counts as "finished",
and can turn on *Keep listening after she answers* for a back-and-forth that
costs one tap rather than one per turn.

### Wake word

**Settings → Voice → Wake word.** Type whatever phrase you like — it is not
limited to a fixed list, because it is not using a pretrained wake word model.

Detection runs entirely on your own hardware. The browser gates the microphone
locally on loudness, and only short candidate clips are sent — to the same
Home Assistant speech-to-text engine that already transcribes your commands.
Silence is never transmitted and nothing reaches a third party. The browser's
own speech API is deliberately not used: in Chrome it streams the microphone to
Google continuously.

Two syllables or more works best; a single short word gets triggered by ordinary
conversation. If it never fires, loosen *Match strictness* — speech recognition
rarely returns exactly what was said. If it fires on its own, raise the
*Trigger threshold*, which is the setting that matters in a room with a
television in it.

While the wake word is armed a permanent indicator sits at the top of the
screen. It has no dismiss button and it is not hidden by wall display mode.

### History

The last 100 messages are kept on the device and survive a reload — the
**History** button in the dock opens them, with search and a copy button.
They are stored per device, in the browser, and never sent to the server.

## Placing an avatar or an environment

Anything spatial — size, rotation, position, camera framing — has an **Adjust
on the scene** button beside it. That lifts the single control out of the
settings dialog into a small draggable bar at the bottom of the screen and gets
the dialog out of the way, so you can see what you are adjusting while you
adjust it. The scene still orbits underneath. Arrow keys nudge finely, the
arrows at the top step between related controls, and **Done** puts the dialog
back exactly where it was.

For everything else, hold the eye button in the settings header to see straight
through the dialog.

## Direct access (optional)

Besides Ingress, the add-on also listens on two host ports:

| Port | What |
|---|---|
| `8420` | HTTP — typing works, the microphone does not (browser rule) |
| `8421` | HTTPS — microphone works after accepting the certificate warning |

The `ssl` option only affects port 8421: set it to `true` with `certfile` /
`keyfile` to use a real certificate from HA's `/ssl` folder instead of the
self-signed one. Behind your own reverse proxy, point it at port 8420 with
`X-Forwarded-Proto: https` and a WebSocket timeout of at least 600 s.

## Architectures

`amd64` (Intel/AMD), `aarch64` (Raspberry Pi 4/5 and most boards), `armv7`
(older 32-bit installs).
