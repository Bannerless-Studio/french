# TODO (v2 candidates)

Residuals from the v1 QA rounds. The rules already in place are described
in `README.md` and in `engine/tools/packbuilder/langs/fr.py`.

## Sentence links
- Noun/verb homographs with a noun subject are still read as nouns when the
  tagger says so: "Le Japon produit..." links le produit, "L'heure ...
  approche" links l'approche, "Maîtriser une langue demande..." links la
  demande. The subject-pronoun rule only covers je/tu/il/elle/on/ils/elles.
  "pour déjeuner" (to have lunch) links the noun le déjeuner.
- Object clitics la/les before a verb are sometimes read as articles
  ("je la porte" can link la porte). The verb-after-determiner rule skips a
  determiner that follows a subject or object pronoun, but a clitic after a
  noun subject ("Marie la voit") still follows the tagger.
- Fixed expressions whose noun has another sense are unlinked from a short
  list (bon marché, en général, à part, en conserve, au fait). Others
  still link word by word.
- Noun/verb homographs after a noun subject follow the tagger: "Votre vol
  part à 14 h" links la part.
- "fort au rugby" (good at rugby) links fort with its main gloss "strong".
- A feminine noun after an adjective can be read as the adjective:
  "Les mauvaises nouvelles" links nouveau, not la nouvelle.
- Adjectives used as interjections link the adjective: "Mince !" links
  mince (thin).
- Participle/adjective homographs after a noun follow the tagger:
  "du poisson cru" links croire.
- "être juste" (to be fair) links the adverb juste.
- Gender homographs have one entry per lemma (la poste "post office" has no
  le poste "job" beside it; le tour / la tour, le livre / la livre). A token
  whose determiner shows the other gender links nothing.
- au, aux, du, des link only to the preposition (à, de), not also to the
  article le.
- Fixed expressions not merged: n'importe (quoi), avoir l'air, faire mal,
  en avoir marre (marre is a headword glossed "fed up (en avoir marre)").
  Their parts link word by word. (il y a, est-ce que, excusez-moi, s'il te
  plaît, lors de, à travers, petit déjeuner are phrases since QA round 2.)

## Words and glosses
- The reflexive gate uses 2-7 linked sentences per verb, so it is noisy.
  Reverted verbs show a combined gloss ("occuper: to take up; s'occuper:
  to be busy"). When both halves agree the gloss can read oddly
  ("reposer = to rest; se reposer: to rest").
- A few glosses keep a wordy Wiktionary tail or a secondary sense first
  (concevoir "to design"). Add them to
  `tools/gloss_overrides.json` as they are found.
- Days and months are shown without an article.
- Ids are frozen in `tools/id_map_v1.json`. New words get fresh ids; never
  renumber existing ones.
- Appended se-senses for less common verbs come from Wiktionary's first
  reflexive sense (REFL_SENSE in fr.py holds hand ones for common verbs);
  B1 ones are unreviewed.
- A1/A2 glosses were hand-reviewed in QA round 2 (overrides in
  `tools/gloss_overrides.json`). B1 glosses are ranker output only.

## Reading passages
- A native-speaker pass over the 60 texts has not been done yet; only an
  automated QA pass plus one round of manual/external QA fixes (see
  `tools/REPORT_passages.md` for the full manual notes and per-passage
  coverage/link numbers).
- The only out-of-pack lemmas across all 60 passages are salade (x2) and
  cuillère (x1) in the fruit-salad recipe (p0025, unavoidable), la
  ferme "farm" in p0044 and p0054 (the pack's ferme is the A2 adjective
  "firm"), and le scientifique "scientist" in p0059 (the pack has only the
  adjective), so those tokens are unlinked and declared.
- Tap glosses are per word, not per span: "Chère Emma" shows the full
  cher entry "expensive, costly; dear", and the adverb ensemble ("nous
  rentrons ensemble") glosses under the noun headword "l'ensemble … (m)"
  with "together" as first sense. A per-span sense override would need an
  engine change (live check 2026-09-24).
- The passage linker rules for French (declared names never recased or
  linked; a capitalised word that is not a declared name reads as the
  common word; a noun reading of a finite verb in predicate position is
  read as the verb; été after en/l' is the season; a multiword expression
  spans all its parts; an unresolved form links its pack base; a noun with
  no pack entry does not fall back to a same-spelling word of another POS
  when English names the noun's own sense) live in vocab-engine's
  `packbuilder/langs/fr.py` passage hooks, not in this repo. None of them
  changes the word or sentence build (see `tools/REPORT_passages.md`).
- 15 gloss senses were added or corrected in `tools/gloss_overrides.json`
  while writing the passages: cher (dear), ensemble (together), gagner
  (earn), arriver à (manage to), s'occuper de (look after), le public
  (audience), feuille (sheet), marcher (work), compris (included), tenir
  (run / y tenir), direction (management), tout droit (straight on),
  maître (owner), amie, de (partitive).

## Verification
- Browser verification of `index.html` (every tab, fr-FR TTS voice, typing
  with accents at each level, gap items, audio) is still to do.

## Policy rebuild (2026-09-25, engine ff88f44)
- Word ceiling: sang (A1) and drogue (A2) moved to B1; band edges: salle
  A2->A1, grandir and inquiet B1->A2. Ranks, ids, glosses unchanged.
- Drop-everywhere (suicide/self-harm): s2071, s2568, s3161 removed; le suicide
  (w1843) refilled from `tools/generated_examples.tsv`. Sentences 3,277 ->
  3,277 (10 removed, 10 added, by text). passages.json unchanged.

## Republish 09e90bc (2026-09-29)
- Republish 09e90bc: sentence spans (20222/20239 linked words placed); inflected forms now cloze targets
- Republish ef44c6e: no word moves; deleted 10 gloss override keys that were never applied (listed unused since the 2026-09-29 build): multiword en fait|adv, en même temps|adv, entre-temps|adv, tout à l'heure|adv (not shipped), and bien|adj, comme|adv, comme|conj, que|pron, tout|det, tout|pron (those lemmas ship as bien|adv, comme|prep, que|conj, tout|adj, tout|adv); pack byte-identical; set-counter and no-voice planner fixes

## Republish on engine 439df3d (2026-10-08, port wave 1)
- Rollback hash: 00e81a9 (engine ef44c6e). Previous live md5: index.html c65b587d4006aed359554a098625863f, sw.js 367bb8251bb3abe1440483b97203e1c9. New: index.html 13e298d62224af9bee1ca9b74f05075f, sw.js 0d45f47a570e3cb95ae75331bb5d6a58.
- Port = `packbuilder enrich --lang fr --repo .` + `tools/eta.json` (calibrated: `eta_checks.js --pack pack --calibrate --sessions 600`, 85%, seeds 5/6/7). Words gained `ft` only; pack.json gained the generic flag set + `eta`; no text, id or order change. Tiers [ambient core peripheral]: A1 [100, 440, 60], A2 [0, 525, 175], B1 [0, 420, 280]. `check.sh` runs `enrich --check`.
- ETA: goal gains [0.004891, 0.003175, 0.002673], known 7.392; every goal and the gate within 30% on 3 of 3 seeds.
- Storage: new fields day/sn/t/u/f/p/pm/pv/pause/read.done s,ls/today.tw on first use; boot writes nothing; previous build ef44c6e/aa00571 carries them (migration [port] 9/9).
- French has no `pron`, so Progress Settings has no "Show reading" chip (the pack-gated wording never appears).
- Live proof 2026-10-08 (KEEP): seed = 12 sessions played on 00e81a9. On live (md5s above) the seed was byte-equal after boot, reload and on Progress with no backup keys. One Today session added sn/day/pm. That record booted on the previous build byte-equal after boot and reload, no backup keys, and a session ran there. Back on live it was byte-equal after boot and reload. 0 console errors, 0 failed requests.
- Migration proof on engine 8745de1 (placement early stop, placed level drives reading/patterns/estimates, gender gaps, session estimates recalibrated): rollback hash 73f3949faf2d9d29e4ddd39120ddfed83d4c246a (engine 439df3d). Live md5 before: index.html 13e298d62224af9bee1ca9b74f05075f, sw.js 0d45f47a570e3cb95ae75331bb5d6a58. New build: index.html f3f90db006e08dd01283237769ec1397, sw.js d8fe321b6ddc5d7232330fa6085cf05a. Pack diff: pack.json gains gapGender, placedKnown, placedRead, placementEarlyStop, placementWhole (+ eta). Storage: boot writes nothing; 12-session seed from the previous build boots byte-equal on the new build (boot, reload, Progress; only pv added on leaving Progress), after one session boots on the previous build byte-equal with no backup keys and runs a session, and back on the new build byte-equal (scratch 13/13, KEEP).
- Migration proof on engine d2876d8 (adaptive placement with placed-word probation, level exam and critical set, cloze and pattern option fixes, noun gender gaps, notranslate, session estimates recalibrated): rollback hash 00758ab5c9235b6e7a498498c4a641c84a350c5c (engine 8745de1). Previous live md5: index.html f3f90db006e08dd01283237769ec1397, sw.js d8fe321b6ddc5d7232330fa6085cf05a. New build: index.html 098880a4027d9e993280bda6bef513b6, sw.js 3a87c045f60fb6a2c0162e37a0c08053. Proof on the scratch build (live pending the push): a 12-session seed played on 00758ab, and a placement plus exam record, both byte-equal on the new and previous builds in both directions, no backup keys, 0 console errors. Verdict KEEP.
- Migration proof on engine d6fe859 (round d6fe859, 2026-10-14): PASS 30/30 KEEP vs b2b1726 both directions: 12-session seed boots/reloads/progress byte-equal on prev, session changes it, new build boots byte-equal and back; exam record (20 items, 3 word misses, prog.exam + crit x3) boots byte-equal on b2b1726, crit and exam kept through a session there. Browser: placement 17 items landing A2 set 36 = Learn set; pattern cloze in Today Sentences with the meaning tap before the answer and a two-line verdict note; Sentences test 8 of 20 pattern items; Progress patterns row; goal 2 of 3 about 96 sessions, unchanged after a miss; notranslate; 0 console errors; Tatoeba audio aborts only. Screenshots .cache/live/round-d6fe859.
