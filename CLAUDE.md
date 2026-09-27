# French trainer — agent notes

```
kaikki (Wiktionary) + hermitdave FrequencyWords + wordfreq + Tatoeba (sentences,
translations, audio)
        |
        v
tools/build_pack.py (shim) -> engine/tools/packbuilder (langs/fr.py)
        |
        v
pack/*.json (words, sentences, attribution, passages, pack config)
        |
        v
engine/tools/jsonify_pack.py -> pack/*.js (generated)
        |
        v
build.sh -> engine/build.sh pack index.html
        |
        v
index.html + sw.js  --(GitHub Pages)-->  https://bannerless-studio.github.io/french/
                                          progress: localStorage key vocab_fr
```
Why it is built this way: single-file site + service worker for offline use;
engine is a git submodule so every language ships the same drills and QA
tooling; word ids are frozen (`tools/id_map_v1.json`) so a rebuild never
invalidates a learner's saved progress.

## Commands (pinned)
- Rebuild pack: `python3 tools/build_pack.py` (venv with `tools/requirements.txt`;
  set `PACKBUILDER_PATH=../vocab-engine/tools` to build against a non-submodule checkout)
- Regenerate JS from JSON: `python3 engine/tools/jsonify_pack.py pack`
- Build site: `./build.sh`
- Check (must pass before every commit of index.html): `./check.sh`
- Passages: `PYTHONPATH=engine/tools python3 -m packbuilder passages .`
- QA helpers: `PYTHONPATH=engine/tools python3 -m packbuilder {check,scan,sample} --lang fr --repo .`
- Engine tests live in vocab-engine (see its CLAUDE.md)

## Always
- Commit index.html and sw.js together; check.sh's stale-build guard runs post-commit.
- Bump the engine submodule only to a vocab-engine main sha; rebuild after every bump.
- Keep ids append-only; never renumber (tools/id_map_v1.json).
- Path-limited commits: engine, index.html, sw.js, pack/, tools/, README.md, TODO.md; never .venv or .cache.
- Regenerate pack/*.js (jsonify_pack.py) any time pack/*.json changes, including passages.json.
- Use fr-FR for TTS/STT.

## Never
- Edit pack/*.json by hand; change tools/gloss_overrides.json, tools/forced_a1.txt,
  tools/h_aspire.txt or tools/passages_src.json and rebuild instead.
- Edit pack/*.js, index.html or sw.js by hand (generated).
- Delete sw.js (use engine/engine/sw.disable.js).
- Add comments that say what the code does; only why, or an external reference.
- Push to main without `git merge-base --is-ancestor origin/main HEAD`.

## Generated files
pack/*.js, index.html, sw.js, tools/REPORT.md, tools/REPORT_passages.md,
tools/id_map_v1.json (frozen, hand-edit never), pack/passages.json (from
tools/passages_src.json via `packbuilder passages`).

## Where things are
README.md (end users), tools/README.md (builder inputs, file by file),
TODO.md (residuals + STATUS history), engine/ (submodule, read-only here).
