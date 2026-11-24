# LeoPrevent for Claude Code

Security review built into Claude Code. When the agent finishes a turn, LeoPrevent reviews what it
just wrote, and if there's a security issue it sends the agent back to fix it before you see "done."

This is the Claude plugin directory build. It carries the plugin's hooks, commands and a small
launcher, but not the client binary itself: the directory accepts no file over 5 MiB, and the
client is about 16 MiB.

## What happens on first run

The first time a hook runs, the launcher (`bin/leoprevent-plugin`, a shell script you can read)
downloads the client for your platform from the
[LeoPrevent GitHub release](https://github.com/leotrace-hq/leoprevent-plugin/releases) named in
`bin/checksums.txt`. It checks the download's SHA-256 against the pin in that file and runs it only
if they match. Anything else (no network, a mismatch, no SHA-256 tool) deletes the download and lets
your turn finish unreviewed, with one notice saying so.

The download goes to github.com only, over HTTPS. If it takes longer than 20 seconds it carries on
in the background and that turn goes unreviewed.

## What leaves your machine

LeoPrevent sends data to one service of ours, `api.leotrace.io`, over HTTPS. What it sends depends
on the turn:

- **Every turn:** usage metadata. That is your git name and email, the repository's remote (for
  example `github.com/org/repo`), the agent and model, token counts, timing, your platform and the
  plugin version. No code and no prompt.
- **A turn that changed code:** the changed lines and the full text of the files they are in, the
  local helper files that code imports (trimmed to what it calls), and your prompt. Never the whole
  repository.
- **After LeoPrevent asks the agent to fix something:** the code before and after the fix, and the
  agent's reply.

Secret files (`.env` files, private keys, credential stores) are never sent or reviewed. We keep
review records to show them on your dashboard. Details:
[leotrace.io/privacy](https://leotrace.io/privacy/).

## Sign in

After installing, run `/leoprevent:login` in Claude Code and approve the browser sign-in, or save a
license key with `/leoprevent:set-license <key>`.

## Verify the binary

The client is open source (Apache-2.0). Its complete Go source is in
[leotrace-hq/leoprevent-plugin](https://github.com/leotrace-hq/leoprevent-plugin): clone it at the
release tag, run `./build.sh`, and the hashes match the ones pinned in `bin/checksums.txt`.

## Platforms

macOS (Apple Silicon and Intel), Linux (x86-64 and ARM64), and Windows through Git Bash.

Support and security reports: info@leotrace.io.
