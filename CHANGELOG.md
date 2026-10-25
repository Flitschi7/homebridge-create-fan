# Changelog

## 2.3.0

- Support Node.js 24
- Fix: frequent `Disconnected` -> reconnect cycles every few minutes. tuyapi dropped the connection whenever a single heartbeat reply was more than 2s late, which Wi-Fi fan modules in power-save mode often are. The plugin now drops the connection only when the fan has sent nothing at all for 35s
- Fix: the TCP connect timeout is raised from 5s to 10s, so slow-to-answer fans fail with `connection timed out` less often
- Fix: a fan that accepts the connection but never finishes the protocol 3.4/3.5 handshake no longer leaves the accessory stuck offline. The connect attempt is aborted after 20s and retried
- Errors that happen on a working connection no longer force a reconnect. A failed connect attempt is now logged once instead of twice
- Fix: `Timeout waiting for status response` after every command. The fan carries out commands but often sends no status reply, so the plugin no longer waits for one. Commands also no longer queue behind each other for 5s each
- Debug mode now logs the raw data points (dps) the fan reports
- Commands sent while the connection was silently dead are re-sent after the reconnect instead of being lost
- Reconnect backoff now tops out at 60s instead of 120s, so the fan comes back sooner after a Wi-Fi dropout

## 2.2.2

- Fix: a fan whose config is missing a required field (`ip`, `id`, or `key`) is now skipped at startup with a clear error message naming the field, instead of failing with obscure connection timeouts. This helps users who upgraded from 2.0.x without adding the static `ip` that became mandatory in 2.1.0
- Fix: a single malformed/partial Tuya packet on a healthy socket is now ignored (logged at debug) instead of triggering a full reconnect, so one bad packet no longer looks like the plugin "restarting"

## 2.2.1

- Fix: a failed connect attempt (connect timeout or socket error) no longer leaves the accessory wedged offline permanently — the reconnect backoff is now always re-armed, so queued writes get flushed once the fan comes back

## 2.2.0

- Add `light` config (default: on) to hide the Lightbulb tile and its toggle for fan models without a light

## 2.1.1

- Fewer `ECONNRESET` reconnect storms when another local client (a second Homebridge instance, Home Assistant, the Tuya cloud) competes for the fan's single TCP connection
- Writes issued while the fan is offline are now queued and flushed on reconnect, instead of forcing an out-of-backoff reconnect that caused `Timeout waiting for status response`
- Reconnect backoff now has jitter and a longer ceiling so competing clients desynchronise

## 2.1.0

- **Breaking:** each device now requires a static `ip` field in the config
- Removed UDP broadcast discovery (`find()`) — the plugin connects directly to the fan's IP, fixing frequent connection timeouts on stable networks
- Fixed config changes (`version`, `toggles`, `speedButtons`) not taking effect on cached accessories without clearing the Homebridge cache

## 2.0.25

- Fix: momentary toggle tiles now trigger on every tap

## 2.0.24

- Add speed up/down button tiles (gated by `speedButtons` config, default: on)
