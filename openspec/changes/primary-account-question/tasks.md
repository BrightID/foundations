## 1. Edit the document

- [x] 1.1 §2 — replace the BrightID domain question with *"is this the account to verify
      for this person?"*
- [x] 1.2 §2 — after that sentence, add: *"You are not asked whether other accounts
      exist — you cannot know that, and Aura has never required anyone to account for every
      place they exist."*
- [x] 1.3 §2, calibration paragraph — add the asymmetry clause: a strong positive rests on
      knowing the person, a strong negative often does not.
- [x] 1.4 §4, tier table, Subject row — change *"is this a person's only account?"* to
      match §2, shortened to fit the column.
- [x] 1.5 Grep for remaining uses of "only account" and any wording that assumes the old
      question.
- [x] 1.6 §10, decay — the illustration reads *"near zero for 'is this a unique human'"*.
      Under the old question the answer was near-static. Under the new one it changes
      whenever a person changes which account should be verified, so near-zero decay is no
      longer obviously right for the BrightID domain. Either change the illustration or
      state the consequence. Do not leave it as-is.
- [x] 1.7 §12 — *"Verification comes from people who already know the person being
      verified"* becomes *"Verification that carries weight comes from…"*, so the privacy
      claim stays true now that strangers may answer.

## 2. Check it still reads

- [x] 2.1 Read §2 and §4 straight through. Content may grow; complexity may not.
- [x] 2.2 Confirm no **▸ In the code** claim was made about anything not in the code.
- [x] 2.3 Confirm net new prose is about two sentences.

## 3. Human review before it leaves

- [x] 3.1 Philip reads every changed word.
- [x] 3.2 Open the PR against `BrightID/foundations` — https://github.com/BrightID/foundations/pull/1
- [ ] 3.3 Adam and Ali review as humans, not by dispatching an agent.

## 4. Follow-on, not in this change

- [ ] 4.1 File the client-copy update for the Player and front-end apps.
- [ ] 4.2 Note in the canonical-identifier change that this one unblocks it.
- [ ] 4.3 Consider whether *"anyone may answer; the protocol decides who to listen to"*
      belongs in §3 as a stated principle.
