## Alternatives considered

**New vocabulary.** Describing claiming would need *canonical* or *primary*, and
a framing that separates controlling a row from being the subject it was
evaluated as. The document defines neither, and inventing them here would
settle on the page what belongs on a call. Rejected; the change records only
what the node does and what is missing.

**Stating a lockout.** `verifyUserSig`'s identifier-derived fallback applies
only when no row exists, so a BrightID first seen as an evaluation subject
cannot use it afterwards. That is a claim about the operation path, not about
the row primitive, so §3 states the fallback's scope and stops short of calling
it a lockout.
