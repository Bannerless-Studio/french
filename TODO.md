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
