# Changelog

## 1.1 (2026-10-27)
- Renamed from MMA10ExitFix to MMA10Killer.
- Also ends the long **hang on exit**: from the moment the game starts closing (and not before), a crash in the
  game's own shutdown code that nothing else handles gets one line in the log and the process ends straight away,
  instead of waiting on Windows error reporting. Seen with some mod setups in `phMaterialMgrGta`'s table.

## 1.0 (2026-10-27)
- First version (as MMA10ExitFix): stops the **MMA10** error box when GTA IV 1.2.0.59 closes. When the game leaves
  its main loop, one extra heap is added to the end of the game's heap list; only a pointer that none of the game's
  own heaps owns reaches it, and it is ignored instead of raising MMA10. Nothing changes during play.
