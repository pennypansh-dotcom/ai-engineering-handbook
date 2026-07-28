---
slug: i-told-my-reviewer-to-ignore-it
title: "I told my reviewer to ignore the one thing that was wrong"
authors: [nikolai]
tags: [making-of, ai-sdlc, verification, translation]
date: 2026-07-28
---

The Russian edition of the handbook shipped a lesson about defects that slip past every check, and the word it
used for those defects meant *breakthrough*.

{/* truncate */}

The lesson is [the escape ledger](/ai-sdlc/part-3-verification/escape-ledger). In QA an *escape* is a defect
that passed every gate and reached production. The lesson's argument is that each one is evidence about which
gate is blind, not just a bug to fix. The Russian translation of *escape* was «прорыв». That word's dominant
sense in Russian is the good kind of breakthrough: «научный прорыв» is a scientific one. Its second sense is a
forceful breach: «прорыв обороны», a hole punched in a defensive line. Neither of those is a bug that crept
through quietly while everything stayed green, and neither is what Russian QA writing actually calls one. The
term for that is «пропущенный дефект», a defect that was let through.

I found the wrong term by reading the shipped page. Nothing in the automated chain had said a word.

## The right term was already in the file

Before the lesson existed, the Russian page sat in the repo as a placeholder: a title, a heading, two sentences
of "here's what this will cover." The placeholder said «Журнал пропущенных дефектов», the ledger of let-through
defects. The correct term was sitting in the exact file the translation pass opened. That pass overwrote it
with a word of its own invention.

That reframed the whole thing for me. This wasn't a pipeline staring at an empty vocabulary and guessing badly,
which is a problem I know how to fix. The answer was already in the corpus and got overwritten. The Slovak
translation, meanwhile, used «únik», which means a leak or an escape. It is idiomatic and correct, and nobody
touched it.

The correction went in as [PR #232](https://github.com/NikolaiSachok/ai-engineering-handbook/pull/232), commit
`b2b698b`. It was deliberately dull: the title, the H1, every place the term showed up in the body, the
glossary section and its headwords, the new-terms line. Twenty-four lines out, twenty-four lines in. Links,
numbers, the embedded video and the page anchor came out byte-identical, so the review could be about one term
and nothing else.

## The check was told not to look

The translation pipeline has an independent check in it, run on a different model. That check's entire job is
to read the finished translation and report anything that doesn't read like native writing. It read this page.
It reported nothing.

It reported nothing because the pass that coined «прорыв» had written the word into its own instructions as
settled house vocabulary, and then handed it to the check under "do not flag, this is deliberate canon." The
party under review wrote its own exemption, and the exemption covered the one word that was wrong.

The handbook has a lesson arguing that [the actor making a change must not be the actor certifying
it](/ai-sdlc/part-3-verification/detection-vs-mutation). So the inversion happened inside the pipeline built to
teach against it, which is the part I'd rather not have to write down.

The inversion travels, though. Any suppression list can take this shape: a `# noqa`, a scanner allowlist, an
eval config maintained by the team the eval is grading. The newest entry on such a list is the one nobody
outside has looked at yet. It still reads as settled, because someone had to make a call in order to add the
line. Being new should be the reason it gets argued with. If you keep an exemption list, the cheap defence is
an entry format: who added it, what it silences, when its continued need gets rechecked. And the actor whose
output an entry silences must not be the actor who added it.

## A naturalness check cannot see a wrong term

«Прорыв», the breakthrough word, reads beautifully. A Russian speaker parses it instantly, and it sits in its
sentence correctly, denoting something in the general neighbourhood of an escape. Its connotation and its
domain register point somewhere else entirely.

Fluency and domain correctness are separate axes. A reviewer briefed to judge whether text reads native cannot
report on whether the term is the right one. That's not laziness: nothing in what it was given made the second
axis visible. It ran the check it had. A sterner naturalness prompt would only have produced a more rigorous
version of the same blindness. What was missing was that second axis, checked on a pass of its own.

The fix came with three rules I now hold the pipeline to. Nothing gets coined until someone has gone looking
for a translation that already exists, in a placeholder, a sibling page or the glossary; a word already in the
corpus outranks a fresh one. Wrong connotation and wrong register are a named failure mode now, with the
signature written out. A word that is decodable and roughly right on literal meaning but wrong in its dominant
sense is exactly what a naturalness reader waves through. And no coinage reaches the independent check as
settled: new terms go in on probation, unprotected, so the check can argue with them. One more rule landed
after those: external evidence, a domain-tagged dictionary or a usage corpus rather than the model's ear, with
the source recorded.

The conclusion I want to avoid is "review everything more carefully." That's the lesson that feels right and
doesn't scale, and it would have changed nothing here, because the reviewer was already reviewing carefully
along the axis it had. The useful question when a check misses something is whether the miss was even visible
on the axis the check was looking at. If it wasn't, a harsher version of that check misses the same thing
again.

The rules are in place now. What caught this one was still a person reading the output, and I don't know what
the new checks do against the next plausible, fluent, domain-wrong word, because that word hasn't shown up yet.
