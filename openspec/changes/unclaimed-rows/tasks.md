## 1. Edit the document

- [ ] 1.1 §3 — add the **▸ In the code** and **▸ Not yet built** markers at the end of *Subjects need an identifier, not an identity*.
- [ ] 1.2 §14 — append questions 15 and 16, and revise question 13 in place (old → new, per `design.md`).

## 2. Check it still reads

- [ ] 2.1 §14's revised question 13 does not contradict the *Non-person subjects — Not built* row in §13.
- [ ] 2.2 The **▸ In the code** claims are verifiable on `Meta-Node/BrightID-Aura-Node` `dev` at the pinned commit, from **both** files named — `web_services/foxx/v6/db.js` (row creation, empty `signingKeys`) and `web_services/foxx/v6/operations.js` (`verifyUserSig`'s identifier-derived fallback) — **and** `scorer/verifications/aura.py` for the scoring claim (it never references the `users` collection). Re-run `git -C aura-node rev-parse origin/dev` and confirm it still matches the pinned commit before treating the line numbers as current.
- [ ] 2.3 No new vocabulary: grep the diff for *canonical*, *primary*, *withers*, *cascade*, *conferral*, *predicate*.
- [ ] 2.4 The open questions are questions. Nothing in the diff answers them.
- [ ] 2.5 No sentence claims non-person subjects (restaurants, books, agents) are supported today, or that the `users/` row is "all that exists" for them — §13 marks that not built.

## 3. Human review before it leaves

- [ ] 3.1 Philip reads every changed word.
- [ ] 3.2 Open the PR against `BrightID/foundations`.
- [ ] 3.3 Adam and Ali review as humans.

## 4. Follow-on, not in this change

- [ ] 4.1 Claiming — the mechanism, once questions 15 and 16 are answered.
- [ ] 4.2 Deduplication and re-pointing, with revised open question 13.
- [ ] 4.3 Withdrawing an evaluation.
