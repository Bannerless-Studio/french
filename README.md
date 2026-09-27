# French A1-B1 vocab pack

Static data pack for a language-agnostic vocab trainer (`key: "fr"`). 2000
words spanning A1-B1, each with a short English gloss, plus example
sentences with translations and, where the licence permits, native audio.
The Read tab adds 60 short reading passages with comprehension questions
(see "Reading passages" below).

**Live:** https://bannerless-studio.github.io/french/

Open the link, pick a level (or take the placement test), and start a Today
session: short rounds of flashcard-style review mixed with new words, plus a
Read tab with short passages and comprehension questions, and typing practice
for spelling. Progress (what you've seen, what's due for review) is saved in
your browser only, and can be exported/imported as a file to move between
devices. The site works offline once loaded (it registers a service worker).

**Scope:** this app is a vocabulary base for B1; the DELF B1 also needs
grammar, writing and speaking practice, which this app does not teach.

**Data quality:** after four QA rounds, the final hand-checked samples
are as follows. Every A1 and A2 gloss (1,300) was read by hand in round 4,
and 293 were corrected through `tools/gloss_overrides.json`. A stratified
sample of 90 words (30 per level, seed 909) had 89/90 correct primary
senses. The top 300 words by rank have no wrong part of speech. A
90-sentence sample (seed 1010) had 3 wrong word links out of 540 (99.4%),
and 87/90 sentences were fully correct. No elision fragment, clitic
compound, feminine-form duplicate, proper noun or English loan is a word.
Every closed set is complete at A1, every noun shows an article except
days, months and madame/monsieur/mademoiselle, and `alt[0]` is the bare
lemma. Word ids are frozen in `tools/id_map_v1.json`. Known residuals are
in `TODO.md`; per-round notes are in `tools/REPORT.md`.

**Content policy:** sentences on sexual content, threats, violence, death
wishes or weapons are kept out of A1/A2. A word with no clean sense left at
that level moves to B1 entirely (tuer, mourir, mort, meurtre, arme, sexe,
sexuel, sang, drogue). Sentences about rape, sexual/child abuse, suicide or
self-harm are removed at every level. See `TODO.md` for the exact rule
history and counts.

## Reading passages (Read tab)

60 short reading texts, 20 each at A1, A2 and B1, with comprehension
questions each. A level's 20 passages unlock once you've learned 70% of
that level's words. Tapping any word in a passage shows its gloss, including
inflected forms. Comprehension questions feed missed words back into the
review queue as weak words. The passages and questions are machine-written,
checked by an automated QA pass rather than a native speaker.

A passage's spaced re-read on Today (after 7 days) becomes a listening pass
when audio is available: the text stays hidden behind numbered play rows,
and about half the questions are audio-only.

Typing practice stays accent-lenient (`ou` = `où`), but a fold-only match is
rejected when it would spell another pack word: la/là, sur/sûr, où/ou,
côté/côte, marché/marche, élève/élevé and âge/âgé are each distinguished, in
both directions.

## What's in this repo

This repo holds the French data pack (`pack/`) and the data files its build
reads (`tools/`), plus [`vocab-engine`](https://github.com/Bannerless-Studio/vocab-engine)
as a git submodule at `engine/`, which holds the shared UI, drill logic and
pack builder used by every language in this trainer. See `tools/README.md`
for a file-by-file breakdown of `tools/`, and `CLAUDE.md` for the full
architecture and build commands.

## Rebuild and publish (maintainers)

```
git clone --recurse-submodules <this repo>   # or: git submodule update --init
cd french && python3 -m venv .venv && source .venv/bin/activate
pip install -r tools/requirements.txt
python3 tools/build_pack.py && python3 engine/tools/jsonify_pack.py pack
./build.sh && ./check.sh
```

See `tools/README.md` for what each rebuild step reads/writes and how words,
senses and sentence links are chosen, and `CLAUDE.md` for the pinned
commands, submodule-update flow and forbidden patterns.

## Sources and licences

| Data | Source | Licence | Used for |
|---|---|---|---|
| Spoken/subtitle frequency | [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (`fr_full.txt`, 2018 OpenSubtitles, 834,768 rows) | CC-BY-SA 4.0 | word ranking |
| Written/general frequency | [`wordfreq`](https://github.com/rspeer/wordfreq) 3.1.1 (French) | CC-BY-SA 4.0 | word ranking |
| Glosses, part of speech, gender | [kaikki.org](https://kaikki.org) French Wiktionary extract | CC-BY-SA 3.0 / GFDL (Wiktionary) | English glosses, POS, noun gender, inflection map, aspirated h |
| POS tagging / lemmatisation (build time only) | [spaCy](https://spacy.io) 3.8 (MIT) with the `fr_core_news_lg` 3.8.0 model | model: LGPL-LR (trained on UD French Sequoia and WikiNER) | corpus POS, lemma and sense choice; sentence word links. The pack ships no model files. |
| Example sentences | [Tatoeba](https://tatoeba.org) `fra_sentences_detailed.tsv` (726,753 sentences; 377,203 with an English translation) | CC-BY 2.0 FR | sentence text (contributor usernames in `pack/attribution.json`) |
| Sentence translations | Tatoeba `eng_sentences.tsv` + `links.tar.bz2` | CC-BY 2.0 FR | English translations |
| Sentence audio | Tatoeba `sentences_with_audio.tar.bz2` | CC BY / CC BY-SA / CC0 (per clip; only permissive clips linked, keyed by audio id) | `sentences.json[].audio`; recorders per licence in `pack/attribution.json` |

Licence: code MIT, pack data CC BY-SA 4.0, see LICENSE.

No open CEFR word list for French was usable. The kotoshu "Kelly" `fr.json`
is wordfreq rebucketed into levels, not Kelly data, so no CEFR cross-check
is run.

## Level bands

Candidate (lemma, POS) pairs are ranked by the mean of log subtitle-rank and
log `wordfreq`-rank. Levels:

- **A1** (600 words): every forced item, then the highest-ranked remaining
  words. Forced items are days, months, seasons, numbers 0-20 plus the tens,
  cent and mille, eleven colours, six nationalities, oui / non / bonjour /
  bonsoir / merci / pardon / salut / s'il vous plaît / au revoir, et / ou /
  à / de / en, and the A1 core list in `tools/forced_a1.txt`.
- **A2**: the next 700 by rank.
- **B1**: the next 700 by rank.

This is a reproducible frequency proxy for CEFR level, not an official CEFR
classification.
