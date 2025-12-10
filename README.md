# auto-trim

Trimmy-style helper script for enabling and checking TRIM on Intel Macs running macOS 15 — no Swift 6 or Xcode 16 required. It wraps Apple’s built-in `trimforce` and `system_profiler` so you can see TRIM support and toggle it from the shell.

## What it does
- Detects attached NVMe/SATA SSDs and reports their `TRIM Support` status.
- Offers `enable`/`disable` wrappers around `sudo trimforce` (interactive; macOS will still prompt and reboot after enabling/disabling).
- Works with the stock Bash and the Command Line Tools; no extra dependencies.

## Quick start
```bash
# From this repo
./auto-trim status          # show TRIM support per SSD
./auto-trim enable          # run trimforce enable (interactive, requires sudo)
./auto-trim enable --dry-run# print what would run
./auto-trim disable         # undo trimforce
```

Make it available globally:
```bash
sudo cp auto-trim /usr/local/bin/auto-trim
auto-trim status
```

## Requirements & scope
- Intel Mac (`uname -m` == `x86_64`).
- macOS 15 is the target; earlier versions should work but aren’t the focus.
- `status` only needs the stock `system_profiler`/`sw_vers` (no CLT needed).
- `enable`/`disable` require `/usr/sbin/trimforce`; install Xcode Command Line Tools if missing: `xcode-select --install`.
- Script-level `--yes` skips the helper’s prompt, but `trimforce` will still prompt twice and then require a reboot.

## Commands
- `status` (default): print detected SSDs with TRIM support state.
- `enable`: wraps `sudo trimforce enable`. Use `--dry-run` to see the command before running it.
- `disable`: wraps `sudo trimforce disable`.
- Flags: `--dry-run`, `--yes`, `-v/--verbose`, `-h/--help`.

## Notes and safety
- TRIM is usually on by default for Apple SSDs; third-party drives may need `trimforce enable`.
- The helper is intentionally conservative: it doesn’t try to bypass Apple’s prompts or force non-interactive usage.
- If you have Apple Silicon, you likely don’t need this tool; the script will exit when it detects a non-Intel machine.

## Testing
- `./auto-trim status` to validate parsing on your machine.
- `./auto-trim enable --dry-run` to verify the call flow without changing system state.
- Real enable/disable paths depend on `sudo trimforce` and will reboot; run only if you intend to toggle TRIM.
