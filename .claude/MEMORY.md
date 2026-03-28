# MEMORY.md — Lessons Learned

This file captures corrections and patterns discovered across sessions. Claude reads it at session startup (per CLAUDE.md) so mistakes don't repeat.

Add entries using the format: `[LEARN:category] wrong → right`

---

[LEARN:ggplot] Do not use theme_meridian() — use theme_bw() for all ggplot2 figures in this project.

[LEARN:quarto] Quarto RevealJS, not Beamer. Never generate .tex slide files.

[LEARN:estimation] Do not comment on whether coefficient estimates are "good" or "bad." Focus on whether the specification is correct.

[LEARN:paths] Use relative paths in all code. Absolute paths break reproducibility across machines.

[LEARN:data] Never modify files in data/raw/. All cleaning outputs go to data/clean/.

[LEARN:referee2] Referee 2 never modifies author code. It only reads, runs, and creates replication scripts in code/replication/.
