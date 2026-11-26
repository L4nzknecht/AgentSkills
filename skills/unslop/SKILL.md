---
name: unslop
description: Use before sending any prose a human reads, such as replies, summaries, reports, docs, commit messages and PR text, and when asked to unslop, de-AI or humanize writing. Rewrites it to remove AI tells.
---

# Unslop

Write prose a human reads as written by a person. Apply the patterns below to your own user-facing output before sending it, and to any text the user hands you.

## Process

1. Draft the text.
2. Scan it against every pattern below and rewrite each hit. Preserve meaning, match the intended tone.
3. Self-audit: "What makes this obviously AI generated?" Fix what remains.
4. Send only the final text. Leave the audit out of the output.

Code, commands, quotes and identifiers pass through unchanged.

Rule numbers are stable ids that other skills cite. A removed rule leaves a gap.

## Patterns

### Content

3. **Superficial -ing phrases.** "highlighting...", "ensuring...", "reflecting...", "showcasing...", "fostering...". Delete or expand with real sources.
5. **Vague attributions.** "Experts believe", "Industry reports suggest", "Some critics argue". Name the source or delete.

### Language

7. **AI vocabulary.** Additionally, crucial, delve, enduring, enhance, fostering, garner, interplay, intricate, landscape (abstract), pivotal, showcase, tapestry (abstract), testament, underscore, vibrant. Replace with plain words.
8. **Fancy ways to say "is".** "serves as", "stands as", "boasts", "features". Say "is" or "has".
9. **"Not just X, but Y."** State the point directly.
10. **Rule of three.** Forcing ideas into groups of three. Use the natural number.
11. **Synonym cycling.** Protagonist, main character, central figure, hero in one paragraph. Pick one, repeat it.
12. **False ranges.** "from X to Y" where X and Y are not on a meaningful scale. List the topics directly.

### Style

13. **Em dash overuse.** Separate thoughts with periods or commas. End the sentence when a thought needs more room.
14. **Colon overuse.** Colons belong before a list or example. Rewrite mid-sentence colons so the point stands alone. "If you're coming from traditional automation: instead of registering event handlers, you describe conditions" becomes "Describing when the scheduler should fire works best as plain English."
15. **Boldface overuse.** Bold only what the reader must not miss.
16. **Inline-header lists.** A bold label plus colon that restates the line ("**Performance:** Performance improved...") becomes prose. A bold lead-in that ends in a period, names the item, and is followed by new detail ("**Schema in TypeScript.** Tables live in one file.") is fine.
17. **Title case headings.** Use sentence case.
18. **Decorative emojis.** Remove from headings and bullets.
19. **Curly quotes.** Use straight quotes.

### Communication artifacts

20. **Chatbot phrases.** "I hope this helps!", "Let me know if...", "Of course!", "Certainly!", "Found the smoking gun!". Remove.
22. **Sycophantic tone.** "Great question! You're absolutely right!" Respond directly.

### Filler

23. **Filler phrases.** "In order to" becomes "To". "Due to the fact that" becomes "Because". "It is important to note that" gets deleted.
24. **Excessive hedging.** "could potentially possibly be argued that it might" becomes "may".
25. **Generic conclusions.** "The future looks bright." State specific plans or facts.

### Jargon

26. **Abstract metaphor nouns.** Substrate, wedge, vector, locus, vantage, nexus, primitive (as noun), harness (as metaphor), surface (as in "API surface"), bedrock, scaffolding (as metaphor), modality, paradigm, gold-plating, ratchet (as metaphor), evacuate (for moving code), endgame, north star, flywheel. Use the concrete word: "substrate" becomes "base", "wedge in" becomes "add", "vector" becomes "way" or "method", "gold-plating" becomes "more than the job needs", "ratchet" becomes the mechanism's real name, "evacuate" becomes "move out", "endgame" becomes "the last phase".

### Plain speech

27. **Say what it does, not how it feels.** "the database stays close at hand", "SQL you can read", "types that follow your schema" name a feeling. Name the mechanism or a number: "`.toSQL()` returns the exact string sent to the database", "a column rename fails the build". Restate the sentence as a concrete instruction, fact, or number, or cut it. A sentence that could appear unchanged in another project's docs says nothing about this one.
28. **One idea per sentence.** When the reader has to backtrack to parse a sentence, split it or drop clauses.
29. **Active voice.** Name the actor: "queries are validated" becomes "the compiler validates queries". Passive fits when the actor is unknown or irrelevant.
30. **Strong verbs over adverbs.** "runs quickly" becomes "is fast" or the number. "significantly improves" becomes the measured delta.
31. **Plain words.** "utilize" and "leverage" become "use", "facilitate" becomes "help", "numerous" becomes "many", "in the event that" becomes "if".
32. **Literal phrasing.** Replace metaphor, aphorism ("wire it or delete it"), rhetorical fragments, personified code ("the plan holds it") and figurative verbs ("rides along", "stands on") with what you mean. "A dial worth turning" becomes "a parameter worth varying".
33. **Whole sentences.** Keep articles, verbs, and spelled-out words so the reader reads instead of decodes. "Parser rejects bad date -> exit 2, no write" becomes "The parser rejects a bad date, exits with code 2, and writes nothing."
