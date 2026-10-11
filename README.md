# D0WNL04D M3 Releases

This repository distributes official release assets for **D0WNL04D M3**, a local-first desktop utility for inspecting and downloading media.

Paste a public media link, inspect its title and source, choose Audio or Video and the desired format and quality, then download it to a folder you control. D0WNL04D M3 combines single-link downloads, batch review, selectable playlists, a real download queue, persistent history, and local engine diagnostics in one focused macOS workspace.

> This is a release-assets repository. It does not contain the D0WNL04D M3 source code.

## Current platform

The current release supports **macOS 14 or later on Apple Silicon** only (M-series Macs).

Intel Macs, Windows, and Linux are not supported by the current release.

## Install D0WNL04D M3

1. Open [this repository's releases](https://github.com/wlknbrch/d0wnl04d-m3-releases/releases) and download the latest Apple Silicon DMG asset, named `D0WNL04D M3_<version>_aarch64.dmg`.
2. Open the downloaded DMG.
3. Drag **D0WNL04D M3** to the **Applications** folder.
4. Eject the DMG and open the app from Applications.

If no release is listed yet, no installer has been published to this repository.

The release app includes its local media runtime. Normal use does not require Homebrew, Python, Node.js, pnpm, Rust, or manually installed yt-dlp/FFmpeg/Deno. The optional local-signing workaround below may prompt macOS to install its free Command Line Tools.

The first start may take a moment while macOS verifies the app and D0WNL04D M3 initializes and checks its local runtime.

### macOS security notice and local signing

These releases are currently **not notarized by Apple and do not have a Developer ID distribution signature**. macOS Gatekeeper may therefore block the first launch with a message such as “Apple cannot check this app for malicious software”, “the developer cannot be verified”, or “the app is damaged and can't be opened”. The dialog may offer **Move to Trash** without an **Open** option.

#### 1. Try the macOS approval option first

1. Download the DMG only from [this repository's official releases](https://github.com/wlknbrch/d0wnl04d-m3-releases/releases). If a checksum is provided, verify it as described below.
2. Copy **D0WNL04D M3.app** to **Applications** and eject the DMG. Work with the installed copy, not the app inside the disk image.
3. Try opening the installed app once. Dismiss the warning without moving the app to Trash.
4. Open **System Settings → Privacy & Security**, scroll to **Security**, and select **Open Anyway** for D0WNL04D M3, if available. In German macOS this is **Systemeinstellungen → Datenschutz & Sicherheit → Dennoch öffnen**.
5. Confirm **Open** and authenticate if prompted.

Depending on the macOS version, Control-clicking the app and choosing **Open** may also work. If no approval option is available, or the installed copy remains blocked, continue below.

#### 2. Locally ad-hoc sign the installed app

Close D0WNL04D M3 first, then open **Terminal** (Applications → Utilities). Copy the entire block below and press Return:

```bash
(
  APP="/Applications/D0WNL04D M3.app"

  if [ ! -d "$APP" ]; then
    printf 'App not found: %s\nCopy the app to Applications first.\n' "$APP" >&2
    exit 1
  fi

  /usr/bin/xattr -dr com.apple.quarantine "$APP" &&
  /usr/bin/codesign --force --deep --sign - --preserve-metadata=identifier,entitlements,flags "$APP" &&
  /usr/bin/codesign --verify --deep --strict --verbose=2 "$APP" &&
  /usr/bin/open "$APP"
)
```

This removes the download quarantine attribute **only from this app bundle**, replaces its local code signatures with ad-hoc signatures, verifies them, and opens the app only if those steps succeed. The existing identifier, entitlements, and signing flags are preserved when available. The `-` after `--sign` means **ad-hoc signing**: no Apple Developer subscription, Apple account, or signing certificate is required.

**Only do this for a download you trust.** Removing quarantine bypasses the normal downloaded-app Gatekeeper check for this copy; local signing does not notarize the app, verify its publisher, or prove that it is safe. Do not use these steps to override an explicit “will damage your computer” malware warning. Do not disable Gatekeeper globally.

#### Troubleshooting

- **Different installation location:** Change the `APP="..."` line to the actual path of the installed `.app`. Keep the quotation marks, especially if the path contains spaces.
- **Permission denied / Operation not permitted:** Make sure the app is closed and you are modifying the copy in Applications, not the read-only DMG. If the copy belongs to an administrator, an administrator can run the same `xattr` and `codesign --force ...` commands with `sudo` before each command, using the same quoted app path. Terminal does not display characters while an administrator password is entered.
- **Developer tools requested:** If macOS requests Command Line Tools when running `codesign`, install the free tools with `xcode-select --install`, finish the installation, and rerun the block. A paid developer membership is not required.
- **Signing or verification fails:** Stop and retain the exact Terminal error. Download a fresh copy from the official release and try again. If it still fails, [open an issue](https://github.com/wlknbrch/d0wnl04d-m3-releases/issues) with your macOS version, app version, and error. Recursive `--deep` signing is a local repair fallback; some bundled components may require a corrected release. A valid local signature does not guarantee that every runtime component will start.
- **After an update or reinstall:** The replacement app may need these steps again. They affect the installed app bundle, not your downloaded media or files in Application Support.

See [Apple's instructions for opening an unnotarized app](https://support.apple.com/en-us/102445).

## Local runtime setup

D0WNL04D M3 bundles **yt-dlp, FFmpeg, ffprobe, and Deno** as a known-good media runtime.

On first launch, the app copies the bundled runtime into a writable folder in Application Support and checks that the components can run. You do **not** need to download or unpack a separate runtime archive.

The managed runtime is installed here:

```text
~/Library/Application Support/D0WNL04D M3/runtime/
```

Engine updates are manual, not silently installed. In **Settings → Runtime & recovery**, use **Check** to inspect the engine, **Update yt-dlp** to update the selected release channel, or **Restore bundled** to restore the packaged runtime. The stable update channel is recommended.

The standalone yt-dlp executable can take more than ten seconds to start cold. Its version check allows up to 60 seconds; wait for the initial check to finish before treating the engine as unavailable. If it still needs attention, use **Copy diagnostics** and **Open diagnostic logs** in Settings when reporting the problem.

## Local-first by design

D0WNL04D M3 has no application accounts, telemetry, analytics, or cloud backend. Downloading and media processing run locally; the app connects to media providers to inspect and download the links you choose, and to engine release sources when you explicitly request an update.

Settings, download history, presets, runtime files, and diagnostic logs stay on your Mac. Downloads are saved to the folder you choose, with **Downloads** as the initial default.

## First steps

1. Open D0WNL04D M3 and wait for the runtime check to finish.
2. Paste a public media URL into the Download workspace.
3. Inspect the source and review its title, artwork, provider, and available media types.
4. Choose **Audio** or **Video**, then the output format and quality.
5. Review the destination folder and start the download. For a playlist, select the tracks you want and queue the selection.
6. Follow real progress in the activity tray, then reveal the completed file in Finder or open it from History.

Supported output choices:

- **Audio:** MP3, PCM WAV, or Original / Best Source.
- **Video:** MP4, MOV, or Original / Best Source, when video is available.

SoundCloud playlists contain audio only, so Video is disabled. Higher MP3 bitrates and WAV conversion do not create additional quality that was missing from the original source.

Native completion notifications are optional and can be enabled in Settings. Use the installed app rather than a terminal development build for normal macOS notification registration.

## Included asset

| Asset | Purpose |
| --- | --- |
| `D0WNL04D M3_<version>_aarch64.dmg` | The desktop application installer for Apple Silicon Macs, including the bundled media runtime. |

The exact version and asset filename are listed on each release page.

If a SHA-256 checksum file is attached to a release, verify the downloaded asset before opening it. For example, after replacing the version with the actual filename:

```bash
shasum -a 256 "D0WNL04D M3_<version>_aarch64.dmg"
```

Compare the printed hash with the value published for that asset in the release's checksum file.

## Your local data

On macOS, D0WNL04D M3 stores its managed data in:

```text
~/Library/Application Support/D0WNL04D M3/
```

That location can contain settings, history, presets, runtime binaries, recovery records, temporary download work, and diagnostic logs. Completed media is saved separately in your chosen download folder.

Removing the app from Applications does not automatically remove those files or your completed downloads. Back up any data you want to keep before deleting local files.

## Responsible use and release limitations

Only download media you own or have permission to download. D0WNL04D M3 does not circumvent DRM, paywalls, or access restrictions. Optional browser-session or cookies-file authentication is available for sources you are authorized to access; it does not remove DRM.

Some SoundCloud tracks expose only DRM-protected streams even though their title and artwork are visible and they play on the provider's website. Such tracks cannot be downloaded by this app and are marked as non-downloadable after stream verification. Changing the output format or repeating the queue request does not remove that restriction.

Provider support depends on yt-dlp and can change when a website changes its extraction behavior. Playlist inspection currently shows up to the first 100 entries, and available formats, qualities, and subtitles depend on the source.

D0WNL04D M3 is still evolving. Keep backups of important media and [report problems here](https://github.com/wlknbrch/d0wnl04d-m3-releases/issues), including your macOS version, app version, and relevant diagnostics. Remove personal paths, private URLs, or other sensitive information before posting diagnostics publicly.