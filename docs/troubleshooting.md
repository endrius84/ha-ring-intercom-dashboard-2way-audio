# Troubleshooting

Errors and symptoms the author actually hit, and what fixed them.

| Symptom / message | Cause | Fix |
|---|---|---|
| go2rtc: `ring: wrong query` | One of `camera_id`, `device_id`, `refresh_token` is missing | The Ring source needs **all three** in the URL |
| go2rtc / Ring: `401` / `invalid_grant` | Refresh token already used or revoked | Generate a **fresh** token with `ring-auth-cli` and put it straight in the config (don't paste it into go2rtc's web page first) |
| go2rtc add-on: YAML error such as `did not find expected '-' indicator` | Quotes around the Ring URL or token | Remove the quotes; write a trailing `==` as `%3D%3D` |
| Dashboard shows the phone template as raw text (`[[[ const ...`) | Looking at it in **edit mode**, or the template got mangled by the editor | View the dashboard normally (click *Done*) and hard refresh (Ctrl+Shift+R) |
| Phone box and tiles are empty / collapsed | `justify-items: start` on the layout grid | Remove that line from the layout block |
| Phone box is much smaller than the camera | The grid column shrinks to content | Use fixed pixel columns (`504px 240px`) as in the provided view |
| Tiles show `unavailable` / `n/a` | Entity ids differ on your system | Search Developer Tools → States for `volume`, `battery`, `ding` and replace the ids |
| Tapping the lock icon does nothing | Tile template overrides the tap action | Set the unlock in `icon_tap_action` (`lock.unlock`); `lock-unlock` is not a valid action |
| Browser keeps showing the microphone in use after hang-up | A stream with `background: true` is still alive | Remove `background: true`, hard refresh the tab, make sure the helper is off |
| Intercom LED stays on after a call | A connection to Ring is still open: go2rtc probe from its web page, `preload`, ring-mqtt live-stream switches, Ring app, or another integration | Close go2rtc web tabs; remove `preload`; turn off ring-mqtt live/event stream switches; restart go2rtc; bisect by stopping one thing at a time |
| LED stuck on and the intercom doesn't ring in the Ring app | Battery flat or an open live session | Charge it, close everything holding a session, wait a minute, test again |
| Ding sensor never turns `on` | Handset off the hook, intercom offline, or a flat battery | Check the Ring app rings first; use a second trigger (official integration's ding event) |
| Tablet doesn't switch to the Intercom view | The `select` option text doesn't match your dashboard path | Read the entity's `options` attribute in Developer Tools → States and use the exact string; check the automation's Traces tab |
| Squealing / feedback during a call | Tablet speaker is picked up by the intercom mic | Lower the tablet volume and the intercom's voice volume |
| Microphone permission never asked | Browser needs HTTPS for the mic | Use your HTTPS address, or a kiosk app that grants the mic |

## Diagnosing the stream

- The plain `/api/streams` page of go2rtc (open it through the add-on's web UI) lists producers and
  consumers **without** waking the intercom. Do **not** open `?src=ring-intercom` - that probes the stream.
- A healthy idle system shows **no consumers** and no Ring producer connected.
- If you need to share a dump in an issue, **delete the `url` line** (it contains your token) first.
