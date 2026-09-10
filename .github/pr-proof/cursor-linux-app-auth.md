# Cursor Linux app-auth PR draft

## Summary

Restore Linux Cursor usage through the signed-in Cursor app token, including the Grok Bot allowance, while preserving
manual-cookie precedence and keeping explicit web mode independent of app credentials.

Reference: [upstream PR #1908](https://github.com/steipete/CodexBar/pull/1908), merged at `7807679535394caa78b75526caf433057377648f`.

Thanks @DonnieFi for the original contribution.

## Maintainer improvements

- Rebases the Linux app-auth change onto the current upstream `main`.
- Keeps manual cookies and cached/stored sessions ahead of the app token, and makes explicit Linux `web` mode manual-cookie only.
- Validates absolute-only `XDG_CONFIG_HOME` handling, including a literal `~/custom` regression case.
- Covers cached-session precedence, explicit-web isolation, app-token expiry, read-only database access, and best-effort Grok Bot behavior.
- Clarifies Linux paths, fallback order, CLI behavior, and contributor credit in the docs and changelog.

## Behavior

- Linux reads Cursor's XDG state database read-only and uses the existing Cursor usage endpoints.
- Resolution order is manual cookie, cached/stored session, then the app token.
- Explicit `--source web` requires a manual cookie and never reads app credentials.
- Grok Bot failure does not discard valid Cursor usage.

## Proof

- Focused Linux suites: 15 tests in 2 suites passed.
- Full suite: `make test` passed 94 tests in 9 suites (69 selections, 6 groups).
- Release CLI build passed and is installed at `/home/red/.local/bin/codexbar`.
- Live CLI smoke passed for Cursor and Grok Bot usage; output was redacted before capture.

![Cursor Linux usage proof](https://raw.githubusercontent.com/DonnieFi/CodexBar/fix/linux-cursor-app-auth/cursor_fix.png)

The screenshot is the supplied redacted CLI card capture and shows Cursor, Third Party, and Grok Bot windows.

`make check` reaches the repository checks but cannot complete on Debian because its installer regression requires macOS
AppleScript. The Linux lint lane reaches portable checks but SwiftLint cannot load Debian SourceKit.

Exact reviewed source head: `ca649a5850f14d39cc90b262b7b2749ea5836035` (rebased onto upstream `main` at `7fdc176`).
