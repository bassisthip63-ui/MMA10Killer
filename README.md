# MMA10Killer

**Hate this pointless GTA IV error on exit? Yeah, me too. Let's murder it in its sleep.**

An ASI plugin for **GTA IV Complete Edition 1.2.0.59** that fixes two annoyances when you quit the game:

- the **MMA10** error box (and the Rockstar launcher error that can follow it), and
- the game **hanging on a black screen** for a long time before it finally closes.

It only acts **while the game is closing**. During play it does nothing at all.

## Download

- [`MMA10Killer_1.1.zip`](MMA10Killer_1.1.zip): the plugin (`MMA10Killer.asi`), README and licence.
- [`MMA10Killer_source_1.1.zip`](MMA10Killer_source_1.1.zip): the complete source code and build scripts.

## Install

Back up your game folder (or at least note what you add) before installing any mod.

1. Copy `MMA10Killer.asi` into the folder with your other `.asi` plugins (the game folder, or `plugins` if your ASI
   loader uses one). An ASI loader is required; Fusion Fix and most mod setups already have one.
2. Play and quit as usual. `MMA10Killer.log` next to the plugin says what it did each session.

To uninstall, delete `MMA10Killer.asi` (and `MMA10Killer.log`).

**GTAIV.exe 1.2.0.59 only.** On any other version it does nothing and says so in its log. Downgraded 1.0.8.0 /
1.0.7.0 installs don't need it.

## How it works

**MMA10.** When the game frees memory, it asks each of its memory heaps "is this yours?". MMA10 means none of them
said yes. With some mod setups a stale or garbage pointer is left behind (the logs show values like `0xCDCDCDCD`),
and the game only tries to free it while shutting down. The moment the game leaves its main loop to close,
MMA10Killer adds one extra heap to the **end** of that list. The game's own heaps are still asked first, so all real
memory is freed exactly as before. Only a pointer that none of them owns reaches the extra one, which accepts it and
does nothing with it.

**Exit hang.** Sometimes the game's own shutdown code crashes on such a stale pointer, and Windows then spends a
long time collecting crash data before the process goes away. From the moment the game starts closing, MMA10Killer
catches a crash that nothing else handled, writes one line to its log and ends the process right away.

**During play it does nothing.** If MMA10 or a crash happens *while you are playing*, you still see it, and you
should: that means something really is wrong, and hiding it could lead to worse problems later.

## The log

A session where it had something to do looks like:

    shutdown: the game is closing - exit-time safety net in place (heap slot 4)
    exit: the game tried to free 0xCDCDCDCD, which none of its heaps owns (this is what showed MMA10). Ignored.
    closing: the game's own shutdown code crashed (...). Ending the process now instead of waiting on Windows error reporting.

If you only see the "Ready" and "safety net in place" lines, your setup had nothing to fix that time.

## Safety and antivirus

- No threads, no network access, no files touched except its own log.
- It changes one 5-byte call instruction in the game's code (after checking the exe version and the exact bytes)
  and, only at exit, one entry in the game's heap list and the process's crash handler.
- Not packed or obfuscated; the full source is included and builds with the free LLVM tools (`build_win.cmd` on
  Windows, `build.sh` on Linux).
- Not code-signed (that needs a paid certificate). If a scanner flags it, submit it to the vendor as a false
  positive, or build it yourself from the source.

## Licence and credits

MIT (see [LICENSE](LICENSE)). By bassisthip63-ui, with Claude (Anthropic).
Not affiliated with Rockstar Games or Take-Two Interactive. Use it on your own legally owned copy of the game.
