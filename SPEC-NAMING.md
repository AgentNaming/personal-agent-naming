# Personal Agent Naming (PAN)

**An open standard for naming personal agents**, sponsored by AgentMesh, and a companion to the Agent Messaging specification, `SPEC.md`.

**Version:** 0.10 · **Date:** 2026-10-13
**Authors:** Jeff R Schneider <jeffrschneider@gmail.com>

**Editorial, 2026-10-13:** PAN is published as an open standard sponsored by AgentMesh. The
position paragraph in the introduction and §11, now "Implementations", say so. No normative change.

**Changes from 0.9 (2026-10-05):** One addition, new §5.8, the rename
within one registrar. §5.6 covers a name that moves to another registrar;
nothing covered an agent given a new name at the same registrar with the
same key, so the old name could only be released, and
every address written down for the agent then answered "no agent by that
name". §5.8 lets the old name keep resolving to the same card for a bounded
window, with a notice beside the card naming the new handle and the day the
old one stops, and afterwards answer resolution with `moved_to` for the
cooling-off period: §5's optional tombstone signal, given a destination. It
is not a referral. §5 and §7 gain one line each. No change to claiming,
binding, the card, or re-homing.

**Changes from 0.8:** One addition, new §4.4, making a rebind visible to the
party it can deceive. §4.1's pairing ceremony never required a handle to be
unbound, so one ceremony re-points a bound name to a brand new key. The act
is legitimate (re-pairing after a key loss is exactly what the ceremony is
for), but the party it can mislead is not the person who ran it, who
authorized it twice over; it is the counterparty who reads the name.
Reputation and audit history are keyed by the public key, so when the key
changes, the record behind an unchanged name silently starts over at zero.
A confirmation prompt at the owner therefore fixes nothing, and §5.3's
pinning protects only a resolver that saw the handle before the change.
§4.4 has a registrar record every change of a handle's bound agent key
(previous key, new key, when, method) in a public, append-only record, and
§5.1's card now serves that record as an identity field, `key_history`,
inside the signed bytes, scoped to the handle's current claim. A resolver
seeing the handle for the first time can now see that the name moved to its
current key and when, and a registrar's human-readable page states it in
plain words. §7 gains the matching obligation. No changes to handles,
claiming, the pairing ceremony, or resolution; the card gains one field.

**Changes from 0.7:** One addition, new §6.2, closing a gap §6's chaining
never covered. A hash chain detects a rewrite; it does not detect a
deletion, because a chain geneses at its first entry, so a registrar that
discarded its log and started appending again would hold a chain that
verifies perfectly, and nothing in the entries says how many there ought to
be. The gap is worse for the party §6 serves: an owner receives only their
own entries, whose `prev_hash` and `entry_hash` point at entries they may
not read, so they can check that each entry hashes to what it claims and
can never walk the chain it belongs to. "Append-only" was an assertion the
registrar made about itself. §6.2 has a registrar publish, periodically and
signed, where its chain ends and **how many entries it holds**, over a
canonical form carrying the issuing registrar's own URL. The count is the
part that matters: a hash says an entry is intact, a count says entries are
present, and a chain later reporting fewer than a published commitment
contradicts a signature the registrar cannot disown. Commitments MUST be
publicly readable, since they are only worth anything to someone who kept an
earlier one, and they disclose nothing, being a hash, a count and a
sequence number, so §6.1's erasure is untouched. A registrar SHOULD commit
on a cadence even when idle, so that having stopped is distinguishable from
having nothing to report. No wire changes to handles, claiming, binding, the
card, or resolution.

**Changes from 0.6:** One addition, new §6.1, resolving a conflict inside §6
that made erasure impossible: entries are never deleted and every entry
named its anchor in plaintext, so an owner's email address sat permanently
on a record nothing could remove, and twice over at that, since an email-tier handle
contains the address in its own name. §6.1 lets a registrar record the
owner as a stable opaque reference whose mapping to the anchor is
separately deletable, so deleting the mapping severs the link without
touching a hashed byte and the chain still verifies. It requires that such
a reference be described as a pseudonym rather than as anonymisation, that
a returning owner be issued a new one, that already-written entries be
neither rewritten nor re-chained (both forms are one chain, and a verifier
picks the rule from the entry), and that the resulting plaintext residue be
disclosed rather than glossed. No wire changes to handles, claiming,
binding, the card, or resolution; the owner-scoped read of §6 returns the
same shape it always did.

Also in 0.7: §3.1 gains one permission and gives up one guarantee. A
delegated session **MAY** now release a handle its own anchor holds, and
**MUST** be refused for any other anchor's. The case is a partner closing
a user's account, where the only route available was a registrar's
moderation credential, putting an authority meant for taking names down
for cause behind an action the user initiated, and writing the operator
into the §6 log as having taken a name its owner gave up. §9 already
required operator-initiated releases to be logged as such, which is only
meaningful if other releases are not, so §9 now also forbids recording an
owner's or delegate's release as an operator action. What the permission
costs is stated where it is granted: a stolen §3.1 credential can name any
anchor, so the guarantee that such a credential cannot take existing names
away from their owners is given up by any registrar that accepts delegated
release, which is why the permission is MAY, and why §3.1 tells a
registrar that wants the guarantee to decline. Unbinding, reversing a
move, and releasing another anchor's handle stay refused. §4.3 and §9
follow the change. No wire changes.

§9 also says what *separate* credentials means, since §3.1's cost depends
entirely on it: the moderation credential's bytes are not the witnessing
credential's bytes, two settings holding one value are not two credentials,
and a registrar whose moderation authority still answers to the witnessing
credential MUST say so to its own operator, the only party who can see that
failure or fix it.

**Changes from 0.5:** Hardening, written after a security review of the
reference implementation. No new capabilities and no wire changes to
claiming, binding, or the card's existing fields. §5.6's fifth check is
rewritten: corroboration now requires a *signed* referral from a
*configured* peer, because a check satisfied by a source the caller
nominates lets anyone mint any handle under anybody's email address.
§5.6 also defines the referral document itself and bounds the outbound
fetch a corroborating registrar may make. New §5.7 gives the anchor
owner the standing a key-signed move does not: a bounded window in which
to reverse it. §5.3 puts the issuing registrar and a validity window
inside the signed bytes, and replaces the single published signing key
with a key SET carrying key ids, so rotation is additive instead of a
flag day; §5.1 states the TTL a registrar actually serves. §3.1 and §4.3
name unbinding as destruction, so a delegated session cannot do it. §5.4
covers reverse resolution. §7 gains two obligations (refuse resolution
rather than serve an unsigned card; corroborate a migration in), and §9
gains the ceilings, mail-sending, and outbound-fetch rules that make the
rest of the document safe to implement literally.

**Changes from 0.4:** Federation-readiness (companion to `SPEC.md` §1.5's
layering invariants). §5.2 WebFinger upgraded SHOULD → MUST and now
defines domain sovereignty: the anchor domain, where it speaks, outranks
every registrar. §5.1 splits the card into identity fields and
attachment fields (different lifetimes, different TTLs) and adds the
serving mesh to the agentmesh endpoint. New §5.5 (resolution authority:
where authority flows from, and how a registrar of record is found) and
§5.6 (re-homing: moving attachment or custodian with no identity event;
pins deliberately do not alarm). §7 gains two obligations: WebFinger
(MUST) and migration-out (MUST: a registrar executes a key-signed
departure; a registrar that won't let a name leave is not conforming).
§8 extends the trust model to multiple registrars (per-registrar
pinning, conflict precedence, surfaced-never-merged). §10's federation
exclusion narrowed accordingly. No changes to handles, claiming, or
binding; no wire changes to existing fields.

**Changes from 0.3:** PAN is now AgentMesh's naming service, not an
independent standard (repositories merged; positioning rewritten). §5.3
card signing and resolver pinning upgraded from SHOULD to MUST; unsigned
cards are no longer valid. Card signing added to registrar obligations
(§7). §8 states the shared-operator trust posture plainly. No wire
changes: handles, canonical strings, the card, and the resolution API are
unchanged. §4.3 (delegated binding) added: a §3.1 partner that verified
agent-key control first-hand may bind without a second pairing ceremony,
disclosed via `binding: agent-key-delegated:<partner>`. §5.4
(discoverability) added: an anchor is findable by its email only after
its owner opts in; default stays no-enumeration.

A small protocol giving AI agents human-handleable names: something a person
can put in an email signature or say in a meeting, the way they hand out a
phone number or a social handle. PAN targets the largest and least-served
agent population: **personal agents**, owned by people who have an email
address and nothing else. No domain, no PKI, no ops team.

PAN deliberately does one thing. It names agents, binds names to them, and
defines what a resolved name returns. It does not transport messages (that
layer belongs to protocols like A2A and Agent Messaging), does not verify
capability claims, does not host agents, and does not do discovery (that
belongs to catalog/search layers like ARD).

**Position beside Agent Messaging.** PAN is an open standard, sponsored by
AgentMesh. An Agent Messaging deployment that offers naming (a
deployment-defined part, SPEC.md §1.6) resolves handles against a PAN
registrar for the features that need a named, accountable operator: sender
provenance, admission tiers, and durable and sealed rooms. The spec is
self-contained as a layering discipline: claiming, binding, and resolution
are defined without reference to mesh internals, and pairing requires
nothing but an Ed25519 key. Where this document describes a deployment, it
describes the one AgentMesh runs.

**Relationship to ANS.** The Agent Name Service (ANS) covers
domain-anchored, PKI-backed agent identity for organizations that own a
domain and can run a certificate authority. PAN does not compete there:
it names agents whose owner has an inbox, not a PKI team. Domain
anchoring is out of scope by design (§9).

---

## 1. Terminology

- **Handle**: one globally unique string naming an agent, of the form
  `<name>.<email>`.
- **Anchor**: the email address whose control authorizes claims under it.
- **Registrar**: a service that accepts claims, enforces uniqueness,
  answers resolution queries, and maintains the history log.
- **Agent record**: the registrar's minimal record of the agent a handle
  points at: its public key and, optionally, endpoints. It is created by
  binding, not harvested.
- **Binding**: the attachment of a handle to an agent record.
- **Card**: what resolution returns (§5).

## 2. Handles

A handle has one form:

```
<name>.<email>        PublicAgent.ann@gmail.com
```

- `<name>` is any non-empty string up to 64 characters containing no
  whitespace, no `@`, and no control characters. **Dots are allowed.** There
  is no further grammar.
- `<email>` is lowercased.

**Rule 1: nobody parses handles.** A handle is an opaque key. Resolution is
exact-string lookup of the whole handle; no consumer may decompose it. This
is what lets the grammar stay this simple: the string belongs to whoever
claimed it first, and the registrar knows the anchor because it witnessed
the claim.

**Rule 2: uniqueness is full-string, first come, first served.** The
registrar enforces uniqueness on the case-folded complete handle at claim
time. The second claimant is refused with "taken." No exceptions, no
adjudication.

Handles are case-insensitive for matching and case-preserving for display.

## 3. Claiming

Control of the mailbox authorizes claims under it.

1. Claimant submits the anchor email to the registrar.
2. Registrar delivers a short verification code (6 digits, ≤15-minute
   expiry) to that mailbox, and accepts a bounded number of attempts.
3. A correct code yields a bounded session under which the claimant may
   claim handles, bind, release, and list their handles. Registrars choose
   the lifetime; the reference registrar uses 8 hours (long enough to manage
   a roster, short enough that the emailed code stays the real credential).

Registrars MUST rate-limit code issuance per anchor.

**The operator record.** Every anchor has one REQUIRED public display name,
the operator's chosen label (e.g. "Ana Lima"). It MUST be set no later
than the anchor's first claim, under the verified session; it MAY be updated
at any time under a verified session, and every change MUST be recorded in
the history log. Naming yourself and claiming a handle are one act to the
person doing them but two to the registrar: a claim that fails (taken,
cooling off, moved away) MUST leave the operator record as it was, since
that name is served on every card of every handle the anchor already holds.
Registrars MUST show the operator name on every card of that anchor's
handles (§5.1). The rationale: PAN is *personal* agent naming,
and the handle already publishes the anchor email, so the human behind an
agent is the meaningful unit of trust; a white pages has names. Consumers
MUST treat the name as a chosen label anchored to the proven email, not as
verified identity (§8).

**Lifetime = anchor lifetime.** A handle lives as long as its owner can
re-prove the anchor when required. This is intended: a personal address that
outlives employers keeps its handles; a work address that dies at
offboarding takes its handles with it. That is the governance boundary
working, not a defect.

**Release and cooling-off.** Releasing a handle tombstones it. A released
handle MUST NOT be claimable by anyone for a cooling-off period (REQUIRED
minimum 90 days), so that a handle written down last year does not silently
start pointing at a stranger.

### 3.1 Delegated witnessing

A registrar MAY accept a partner service's attestation that it has already
verified an email, and mint a session without a second code round trip: the
partner (in the reference deployment, the AgentMesh control plane, whose
accounts are themselves email-verified) authenticates to the registrar
with a pre-shared credential and names the email. This trades one email ceremony for a trust link, and
the spec constrains that trade three ways:

1. **Scope.** A delegated session may *establish*: claim handles, set the
   operator name, start pairing, bind a handle to an agent record. It MUST
   NOT *move*: reversing a move (§5.7) and any future transfer MUST be
   refused with an instruction to sign in directly. It MUST NOT
   **unbind** (detach a handle from the agent record it points at),
   because unbinding destroys rather than establishes: a handle with
   nothing bound to it resolves as "reserved" (§5), so detaching an
   anchor's handles takes that operator's agents off the mesh as surely as
   releasing the names would, and it does it without tombstoning anything.
   Release is the one destructive act it may perform, bounded and
   qualified immediately below. A stolen delegate credential can squat new
   names under emails it names, which is detectable and reversible, but
   cannot silence the agents already bound to an owner's names, and cannot
   move a name to another registrar or reverse a move.
2. **Disclosure.** Sessions carry a provenance, `email` or
   `delegated:<partner>`. Everything a delegated session establishes MUST
   record it: the history log entry, and a `claimed_via` field on the card
   (§5.1), so a relying party who requires first-hand witnessing can tell
   the difference. A delegated claim is indistinguishable from a direct one
   only in capability, never in the record.
3. **Accountability.** The registrar chooses its partners and vouches for
   the arrangement; §8's "trusting the registrar's word" expands to
   "trusting the registrar's choice of witnesses," and the disclosure rule
   exists exactly so consumers can decline the expansion per handle.

**Releasing the session's own anchor.** A registrar **MAY** accept a
release from a delegated session, and only for a handle that the session's
own anchor holds; it **MUST** refuse a release of any other anchor's
handle. That refusal is the whole of the permission. A delegated session
speaks for one anchor, so what it may give up is what that anchor holds,
and nothing else.

The case this exists for is a partner closing a user's account: the names
anchored to that account have to go with it. The alternative, a
registrar's own operator credential releasing them on the user's behalf,
is worse in two ways. It puts an authority meant for moderation (taking a
name down for cause) behind an action the user initiated, widening that
authority's blast radius to cover ordinary use. And it writes the operator
into the §6 log as having taken a name whose owner in fact gave it up. §9
requires operator-initiated releases to be logged as such, and that
requirement means nothing unless releases that were *not*
operator-initiated are recorded as what they were.

A release under a delegated session **is** an ordinary owner release and
**MUST** be logged as one, with the disclosure rule above applied to it:
the entry records the same action a direct release records, and discloses
the delegated provenance within that entry. An owner reading their own
history (§6) therefore sees that the name was given up on their behalf and
by which partner, and a registrar's audit of its own operator action stays
a list of operator action.

**What this costs, stated rather than implied.** A delegated session's
anchor is named by the partner that mints it, so a party holding a stolen
§3.1 credential can name any anchor and release its handles. Earlier
versions of this section guaranteed that such a credential could not take
existing names away from their owners; a registrar that permits delegated
release gives that guarantee up, and gives it up for good, because a
release tombstones and cools off (§3) and §5.6 does not let a registrar
re-issue a name to make it right. The permission is **MAY** for exactly
that reason: a registrar that would rather keep the guarantee **MUST NOT**
accept delegated release, and any registrar that does accept it **SHOULD**
hold a §3.1 witnessing credential that is not also a moderation credential
(§9), so that the two authorities can fail separately. A directly verified
session remains the only way to unbind, to reverse a move, or to release a
handle of an anchor other than the session's own.

## 4. Binding

Claiming a name and proving you operate an agent are different proofs. A
registrar MUST NOT bind a handle on email proof alone unless the agent
record was submitted under that same anchor.

### 4.1 Agent-key pairing (the primary path)

Most personal-agent runtimes hold an Ed25519 key, and the agent's public key
is its identity. Binding proves control of that key:

1. The handle owner, in an authenticated session, requests a **pairing
   code**: short, single-use, ≤10-minute expiry (e.g. `KX4-92F`).
2. The software that holds the agent's private key (*any* software: a
   gateway, a daemon, a five-line script) signs the UTF-8 bytes of the
   canonical string:

   ```
   pan-pair-v1:<code>:<agent-id>
   ```

3. It submits `{code, agent_id, signature}` to the registrar. This call
   needs no authentication: the code proves the handle owner initiated
   pairing; the signature proves agent control. **The binding is the
   intersection of the two proofs.**
4. The registrar verifies the signature against `agent_id`, records the
   agent (its key, and any endpoints supplied), binds the handle, and logs
   the binding with its method.

The agent record is created by this step. There is no prior directory to
look the agent up in: the signature is the record's authorization, and the
key is the agent's address.

For the v0.3 profile: keys are Ed25519 expressed as nkey public strings
(`U…`), and signatures are base64-encoded raw Ed25519 signatures. Other key
profiles may be added; the canonical string is versioned for this reason.

Replay is prevented by construction: codes are single-use and expiring, and
the signed string includes both the code and the agent ID.

**Host neutrality is normative.** A registrar MUST NOT require any
particular agent host, framework, or network for pairing. Whoever holds the
key can pair, from any runtime. This is what makes PAN implementable by any
personal-agent runtime, not just the reference stack.

### 4.2 Submitter-match

For an agent that does not hold a key (for example, an A2A card reachable
only by URL), the owner may submit a minimal agent record under a verified
anchor. A handle anchored to the same email may then bind to it directly.
The proof is that the same email both submitted the record and owns the
name.

### 4.3 Delegated binding

When the partner behind a delegated session (§3.1) has itself verified
control of an agent key first-hand (by an equivalent signed ceremony, a
single-use code signed by the agent's key), the registrar MAY accept the
partner's attestation and bind without a second pairing ceremony. The
rationale is the person's experience: proving key control twice in one
sitting, once to the partner and once to the registrar, proves nothing
the first proof did not.

The trade is disclosed the same way §3.1 discloses claiming:

1. **Scope.** Only a delegated session may bind this way, and only to
   handles its own email anchors. The §3.1 scope limits apply unchanged: a
   delegated session still cannot unbind, and may release only what its own
   anchor holds. This section authorizes *attaching* a handle to an agent
   record the partner witnessed; a request to bind a handle to nothing
   carries no attestation and MUST be refused for a delegated session like
   any other destructive act.
2. **Disclosure.** The binding method is recorded and served as
   `agent-key-delegated:<partner>` (in the history log entry and on the
   card's `binding` field), so a relying party that requires first-hand
   pairing (§4.1) can tell the difference and re-run the §4.1 ceremony if
   it cares.
3. **Accountability.** The registrar's word now covers the partner's
   verification of key control, not only the email. §8's expansion
   ("trusting the registrar's choice of witnesses") covers this, and §9
   notes what a stolen delegate credential can and cannot do.

### 4.4 Rebinding and key history

A pairing ceremony does not require the handle to be unbound. Re-pairing is
legitimate and expected: an owner replaces a machine, rotates a leaked key,
or moves an agent to new software, and §4.1's two proofs authorize the new
binding exactly as they authorized the first. What re-pairing also does is
re-point a trusted name at a record with no past. Reputation, audit chains,
and every other per-key record start over at zero behind a name that looks
unchanged, and the party that lands on is the counterparty who resolves the
name, not the owner who ran the ceremony. The owner cannot be prompted into
protecting somebody else, so the defense is the one §5.3 already uses for
key changes: make the change visible to readers.

1. **Recorded.** When a binding replaces or removes a handle's bound agent
   key, the registrar MUST record the change: the previous key, the new key
   (or none, for a detachment), when it happened, and the binding method
   that caused it. The first binding of a fresh handle is not a change and
   is not recorded; neither is re-pairing the same key. The record is
   append-only, like the §6 log. Unlike the §6 log it is public, because it
   exists for readers, and it discloses nothing resolution does not already
   serve: the handle, and keys the card itself would have carried.
2. **Served.** The card (§5.1) MUST carry the handle's recorded key changes
   as an identity field, `key_history`, inside the signed bytes: a list,
   oldest first, of `{changed_at, old_key, new_key, method, old_key_revoked}`,
   where a null `new_key` records a detachment and `old_key_revoked` is true
   when the binding that made the change said the replaced key is revoked
   (its owner reported it leaked; SPEC.md §4.10). A resolver SHOULD treat a
   message signed by a key recorded as revoked as not from the agent. Changes that predate the handle's current
   claim (an earlier owner's era, before a release and its cooling-off) MUST
   NOT be served; they describe a different claim of the name.
3. **Stated.** A registrar serving a human-readable page for a handle
   SHOULD state a key change in plain words with its date, so a person
   checking a name they were given does not need to read JSON to learn that
   the record behind the name is younger than the name.

What this complements rather than replaces: pinning (§5.3) alarms a
consumer who resolved the handle before the change. `key_history` informs
the consumer pinning cannot reach, the one resolving the handle for the
first time after the rebind. Neither makes the change wrong; a consumer's
policy toward a young key behind an old name is its own.

A registrar MAY additionally require a caller starting a pairing for an
already-bound handle to acknowledge the rebind in the request itself, as a
machine-readable flag rather than an interactive prompt, so that automation
cannot re-point a bound name by accident. This version does not require it.

## 5. Resolution

Resolution maps a handle to its **card**.

- Resolution MUST be exact-string: case-folded whole-handle lookup.
- Resolution MUST be publicly available without authentication for active
  handles.
- Released handles MUST NOT resolve; registrars SHOULD signal a tombstone
  distinctly from "never existed" in their history log, and MAY do so at
  resolution. For a handle renamed within the registrar, §5.8 says how the
  signal names the new handle.
- A handle claimed but not yet bound resolves to its claim record without an
  address ("reserved").
- A handle that has re-homed (§5.6) resolves to a signed **referral** rather
  than to a card, and MUST keep doing so for at least the cooling-off
  period even if the registrar's own record of it is later released. The
  referral is the only thing standing between "the name moved" and "the
  name vanished" for every consumer holding the old address, and releasing
  a record MUST NOT be a way for that obligation to lapse early.

### 5.1 The card

```json
{
  "handle":   "Coder.ana@example.org",
  "operator": { "name": "Ana Lima" },   // REQUIRED: the anchor's chosen public label (§3)
  "binding":  "agent-key",                 // "agent-key" | "agent-key-delegated:<partner>" | "email-submitter" | null
  "key_history": [],                       // §4.4: recorded changes of the bound agent key, oldest first
  "claimed_via": "email",                  // "email" | "delegated:<partner>" (§3.1) | "rehomed:<registrar>" (§5.6)
  "claimed_at":  "2026-07-18T…",
  "presence": { "state": "online", "last_seen_at": "…" },   // OPTIONAL, only if a source provides it
  "encryption_key": "<X25519 public key>",   // OPTIONAL, only if the agent declares one
  "endpoints": [
    { "protocol": "agentmesh", "agent_id": "UD653KLV…", "node": "UB2FF…", "mesh": "https://api.agentmesh.ai" },
    { "protocol": "a2a", "url": "https://…/.well-known/agent-card.json" }
  ],
  "registrar":  "https://naming.agentmesh.ai",  // REQUIRED: whose word this is (§5.3)
  "issued_at":  "2026-07-25T14:02:11.481Z",     // REQUIRED: signed, not a header
  "expires_at": "2026-07-25T15:02:11.481Z"      // REQUIRED: signed, not a header
}
```

**Identity fields and attachment fields.** The card carries two kinds of
fact with different lifetimes. *Identity fields* (`handle`, `operator`,
`binding`, `key_history`, `claimed_via`, `claimed_at`, `encryption_key`)
say who the agent is; they change rarely, and only a key change alarms
(§5.3), which is also the change `key_history` (§4.4) keeps visible to a
resolver arriving after it happened.
*Attachment fields* (`endpoints`, `presence`) say where the agent is
served right now; they are expected to change, and consumers MUST NOT
cache them beyond the registrar's stated TTL. A third group,
`registrar`, `issued_at`, `expires_at`, describes the *answer* rather
than the agent: which registrar spoke, and for how long its word stands
(§5.3). This split is what makes the serving mesh an implementation
detail: *where* may move freely because *who* never rides on it
(SPEC.md §1.5, invariants 1 and 2).

**The stated TTL has to be stated.** A registrar MUST say, where a
consumer will see it, how long its answer may be reused: the resolve
response carries a `Cache-Control` header, and the card's own signed
`expires_at` bounds how long the signature may be relied on at all
(§5.3). Attachment data SHOULD carry a TTL an order of magnitude shorter
than the signed card's validity; the reference registrar signs a card for
one hour and serves `Cache-Control: public, max-age=360`. A consumer MUST
NOT cache attachment fields beyond the stated TTL, and MUST NOT act on a
card whose `expires_at` has passed.

The OPTIONAL `mesh` field on an agentmesh endpoint names the control
plane of the instance currently serving the agent, its **home mesh**.
It is how a resolver learns *where* alongside *who*, and it is an
attachment field: re-pointing it is routine (§5.6), never an identity
event.

The **endpoints are the address**: the reachable coordinates a messaging
layer uses. For a key-bearing agent, the key itself is the address (in
Agent Messaging the agent ID is its inbox); a node or an A2A card URL may
accompany it. Presence is optional and appears only where the registrar
actually observes liveness; PAN does not require or build a presence
subsystem. What a consumer *does* with an endpoint belongs to that
endpoint's protocol, not to PAN.

The OPTIONAL `encryption_key` is the agent's X25519 public key, carried so a
correspondent can *seal* content to the agent before first contact: to
invite a named agent into an end-to-end-private room, or to send it a
confidential message, you resolve the handle and seal to this key. It is a
capability the card advertises, not an address; PAN neither defines nor uses
it, and simply passes through what the agent declares (Agent Messaging, SPEC.md §4.3).
Absent means the agent participates only in cleartext.

### 5.2 WebFinger and domain sovereignty

A PAN handle is a valid `acct:` URI. Registrars MUST serve
**WebFinger (RFC 7033)**:

```
GET /.well-known/webfinger?resource=acct:Coder.ana@example.org

{
  "subject": "acct:Coder.ana@example.org",
  "properties": { "urn:pan:binding": "agent-key" },
  "links": [ { "rel": "urn:pan:card", "type": "application/json", "href": "…/api/resolve?handle=…" } ]
}
```

so that two decades of existing `acct:` tooling resolves handles with no
PAN-specific code.

WebFinger is also the sovereignty mechanism. The anchor's **domain** may
serve `/.well-known/webfinger` for its own accounts, and where it does,
its answer is final: a domain speaking for its own addresses outranks
every registrar (§5.5). An organization that serves its own WebFinger
needs no registrar at all for its agents' handles. The registrar is the
path for anchors whose mail provider will never serve PAN records (a
Gmail address cannot make google.com answer for it), not a toll booth on
the namespace.

### 5.3 Signed cards, key sets, and resolver pinning

A resolve response is the registrar's live word, and everything downstream
(sender provenance labels, endpoint routing, encryption keys) rides on it.
Registrars MUST therefore sign cards: an Ed25519 signature over the
canonical JSON of the card (compact, keys recursively sorted; the same
canonical form as the history log, §6), delivered beside it:

```json
{ "card": { … }, "registrar_key": "<Ed25519 public key>",
  "registrar_kid": "<key id>", "registrar_sig": "<base64>" }
```

**What the signed bytes must cover.** A signature over the agent's fields
alone says only "some registrar said this, once." It does not say which
registrar, and it does not say when: a document that never expires
verifies forever. Capture a card while a handle is bound to K1, wait for
its owner to re-pair to K2 after a key leak, and replay the captured
document at any resolver seeing that handle for the first time: pinning
(below) then makes the leaked key stick. The signed bytes MUST therefore
also carry

- `registrar`, the issuing registrar's own URL, and
- `issued_at` and `expires_at`, RFC 3339 timestamps bounding how long the
  document may be relied on.

A verifier MUST reject a card whose `expires_at` has passed (a small
clock-skew grace is permitted; RECOMMENDED no more than 60 seconds), and
SHOULD reject one whose `registrar` names an origin other than the one
that served it. Registrars SHOULD keep validity short enough that a
captured document stops being useful quickly and long enough to survive
ordinary caching; the reference registrar signs for one hour. Putting
these fields in a header instead would leave them editable by anything on
the path, which is the one thing signing is for.

A consumer MUST verify the signature and MUST discard a card whose
signature fails **or is absent**: an unsigned card is invalid, and a bad
signature is evidence of tampering, not noise. The registrar's half of
that bargain is §7.6: a registrar that cannot sign MUST refuse to answer
resolution at all (the reference registrar validates its key at startup
and answers HTTP 503) rather than serve bytes no conforming consumer may
act on. Serving an unsigned card is worse than serving none, because the
consumers that check will drop it and the ones that do not will act on
anything.

**The published key set.** The signing key MUST also be published out of
band at `GET /api/registrar-key`, so a verifier need not bootstrap trust
from the same response it is verifying. Publishing exactly one key,
though, makes rotation a flag day: pinning resolvers all alarm at once,
and a registrar recovering from a key compromise has no way to say "this
new key is also mine." The endpoint MUST therefore publish a key **set**:

```json
{ "registrar": "https://naming.agentmesh.ai",
  "keys": ["<current>", "<retired>"],
  "key_set": [
    { "kid": "3f9a1c04e7b2", "key": "<current>", "status": "current", "use": "pan-card-signing" },
    { "kid": "b81d55f0a63c", "key": "<retired>", "status": "retired", "use": "pan-card-signing" }
  ] }
```

- Every key carries a `kid`, an identifier stable for that key's whole
  life and never reused for another. Any derivation satisfies this; the
  reference registrar uses the first 12 hex digits of SHA-256 over the
  public key string, so anyone can check a `kid` against the key it names.
- The current signing key MUST be first in both forms. `keys` is the plain
  array of key strings, for verifiers that want nothing else; `key_set` is
  the same set with ids and status.
- Rotation MUST be additive: publish the new key alongside the outgoing
  one, then switch signing, then drop the old key after the overlap. A key
  published as `retired` is one a verifier MAY still accept; a key absent
  from the set is one it MUST NOT.
- A response's `registrar_kid` names which key of the set signed it, so a
  verifier can pick a key instead of trying all of them.

**Pinning.** Signatures authenticate the registrar; they do not make it
honest (§8). The complementary consumer-side defense is **pinning**, and
it is REQUIRED: resolvers MUST remember `handle → agent key` on first
sight and MUST surface a loud warning when a known handle resolves to a
different key. That is legitimate on re-pairing, and exactly the signal
that matters if a registrar is compromised or coerced. Resolvers MUST pin
registrar signing keys the same way and **per registrar**: §5.5 puts
several in play, and one global pin would cry tampering on every correct
answer from the second one. A pinned signing key that changes MUST NOT be
replaced silently: the resolver MUST refuse that resolution and keep the
pin it holds, because an unattended node that re-pins whatever answers has
no alarm at all. The remedy is the registrar's published key set, fetched
out of band and compared by `kid`. Because the history log is private
(§6), pinning is the only external cross-check a consumer has; a warning's
remedy is out-of-band re-verification with the owner, not silent
acceptance.

### 5.4 Discoverability (opt-in)

Handles embed their anchor email, so §6 keeps the roster log private and
no public endpoint enumerates an anchor's handles: someone knowing your
email must not learn your agents. (A registrar operator's own credential
is a separate question, and §9 says what it does and does not preserve.)
Friend discovery ("do any of my contacts have
agents?") is exactly that lookup, so it exists only as a **consented
exception**:

- Every anchor carries a `discoverable` flag, **default off**. It MAY be
  flipped under a verified session (delegated sessions included), and
  every change is logged (§6). Setting it requires an operator record: you
  become findable as someone, not as a blank.
- `GET /api/discover?email=…` is public. For an opted-in anchor it
  returns the operator name and the anchor's active handles (name and
  bound-or-not; full detail still comes from per-handle resolution). For
  everything else it answers `discoverable: false`. **A not-opted-in
  anchor and a nonexistent one answer identically**, so the endpoint
  confirms nothing about anyone who hasn't consented.
- Registrars SHOULD rate-limit discovery like verification-code issuance:
  consent makes the lookup legitimate, not free to farm.

**Reverse resolution.** A registrar MAY answer resolution by agent key
instead of by handle (`?agent_id=…`), returning the card of the handle
bound to that key. This is the same no-enumeration question asked
backwards, and it needs saying because it looks harmless: an agent key
travels on every signed message, so anyone who has received one message
can turn it into that agent's handle, and a handle contains the
operator's email address in plain text. Registrars MUST rate-limit reverse
resolution per caller address, and MAY gate it on the same `discoverable`
consent as forward discovery; where a registrar gates it, a
non-consenting anchor and an unknown key MUST answer identically. The
reference registrar rate-limits by default and ships the consent gate
switched off, because the platform's own operator surfaces turn observed
keys into handles for display; a deployment whose anchors are private
individuals is exactly the case the gate exists for.

### 5.5 Resolution authority

Until now this document has said "the registrar" as though there were
exactly one. There need not be. Authority over a handle's resolution
flows from the name's owner outward, in this order:

1. **The anchor domain.** If the handle's anchor domain serves WebFinger
   for the anchor (§5.2), its answer is authoritative and final. No
   registrar's record overrides a domain speaking for its own accounts.
2. **The registrar of record.** Otherwise, the handle resolves at its
   registrar of record: the registrar that witnessed the claim, or the
   one the owner has since re-homed to (§5.6). A resolver finds the
   registrar of record two ways: a DNS `TXT` record at
   `_pan.<anchor-domain>` with the content `pan-registrar=<https-URL>`
   (one record; if several are published, resolvers use the
   lexicographically first, which is deterministic if inelegant), where the
   domain owner has published one; failing that, the resolver's
   configured registrar list. (The reference resolver ships with the reference registrar the
   way browsers ship trust stores; the list is the resolver operator's
   policy, and adding to it requires no one's permission.)

**Conflicts are surfaced, never merged.** If two registrars both answer
for a handle, precedence is: the anchor domain's word, if any; else the
registrar whose history log (§6) carries the earliest claim. A resolver
that detects a conflict MUST surface it to its consumer as a warning.
Like a pin mismatch (§5.3), the remedy is out-of-band verification with
the owner, never silent selection.

A reversed move (§5.7) is one of the ways two registrars come to answer
for one handle: the destination may have taken custody before the anchor
owner reversed the departure. The precedence above is unchanged, and it
favors the owner, since the reversing registrar is the one whose log
carries the earliest claim. A resolver MUST still surface the conflict
rather than settle it silently, and this version specifies no way for one
registrar to tell another that a move it corroborated has been undone
(§10).

A registrar is a **custodian, never an owner**. Nothing in this section
gives any registrar (including the reference registrar) standing to
retain a handle whose owner has moved on (§5.6, §7).

**The anchor probe is visible to the anchor domain.** Consulting the
domain first means the domain learns that somebody is resolving a handle
under it, and roughly when. For a handle anchored at a large mail
provider this tells that provider a little about who is being looked up.
This is inherent in making the domain authoritative (the owner's chosen
authority has to be asked to answer), and it is the same exposure
WebFinger has always carried. Implementations MUST NOT try to hide it by
skipping the probe; they SHOULD keep it cheap and infrequent (a short
timeout and a negative cache, so a provider that serves no PAN WebFinger
is asked rarely rather than once per resolution), and a resolver SHOULD
document that the probe happens.

### 5.6 Re-homing

The agent's identity is its key, and a key change is the only identity
event (SPEC.md §1.5, invariant 2). Both of a handle's pointers can
therefore move without anything happening, identity-wise:

- **Moving the attachment** (a new home mesh): the owner updates the
  card's endpoints under a verified session, or the agent re-binds from
  its new node (§4.1). Routine; logged (§6); no ceremony.
- **Moving the custodian** (a new registrar of record): the software
  holding the agent's key signs the UTF-8 bytes of the canonical string

  ```
  pan-rehome-v1:<handle>:<new-registrar-url>:<ISO-8601 timestamp>
  ```

  and either party submits `{handle, new_registrar, timestamp,
  signature}` to both registrars. On receipt of a valid re-home
  statement the old registrar MUST append a redirect entry to its
  history log, MUST answer subsequent resolutions of that handle with a
  signed referral to the new registrar for at least the cooling-off
  period (§3), and MUST NOT re-issue the handle. The new registrar
  records the claim with provenance `rehomed:<old-registrar>` and
  continues the handle's history from there. The old registrar MUST
  record *when* it made the move (§5.7 bounds the anchor owner's
  reversal window from that instant) and MUST refuse a second departure
  for a handle it has already moved: the name is no longer its to
  re-point, and a custodian that keeps accepting fresh destinations keeps
  changing what other registrars corroborate against.

**A statement is not custody.** The paragraph above describes a bearer
authorization: it is self-signed by whoever holds a key, it names its own
destination, and it is presented by an unauthenticated caller. Taken at
face value it authorizes far more than a move: it lets anyone assert any
name at any registrar. A registrar accepting a migration IN therefore
MUST make all five of these checks, and a registrar that makes fewer is
not conforming:

1. **It must be addressed here.** The signed `<new-registrar-url>` MUST
   equal this registrar's own URL. Otherwise a statement written for one
   registrar is replayable at every other. A registrar that does not know
   its own public URL cannot make this check, and MUST NOT accept
   migrations in until it does.
2. **It must be fresh.** The signed timestamp MUST be within a short
   window (RECOMMENDED: 15 minutes). Otherwise a captured statement is
   good forever. A registrar MUST treat a timestamp it cannot parse as
   RFC 3339, or cannot compare against its own clock without overflowing,
   as stale. Arithmetic on a caller-supplied number is not a place to be
   optimistic.
3. **It must be unspent.** A registrar MUST record statements it has
   acted on and refuse a repeat. Freshness alone leaves a live window in
   which a replay still works. The ledger key MUST be computed over the
   same canonical form the handle lookup uses (case-folded, trimmed):
   hashing the caller's spelling instead let a spent statement be
   replayed by changing its capitalization, because the signature still
   verified against the stored display handle and the lookup was already
   case-folded. The record of a statement and the change it authorizes
   MUST be committed together, so that two concurrent submissions of one
   statement cannot both succeed.
4. **It must not take a name already held here by another key.** A
   signature proves control of the key in the statement and nothing else;
   it says nothing about a handle already bound to a different key. The
   same reasoning bars a name inside its cooling-off period here (§3):
   a move MUST NOT be a way around the wait.
5. **The losing registrar must agree, and must be somebody this
   registrar already trusts.** Before taking custody, the new registrar
   MUST confirm with the named old registrar that the handle has been
   released *to it*. The confirmation is the referral the old registrar
   already owes, so nothing new is asked of a conforming custodian on the
   way out. But the confirmation is evidence only when all four of the
   following hold; a registrar that asks for less is not corroborating,
   it is asking the applicant for a reference.

   1. **A configured peer, never a nominated one.** `old_registrar` MUST
      name a registrar this one is configured to accept migrations from.
      The value arrives on an unauthenticated request, so a registrar
      that will fetch from any URL is corroborating against a source the
      caller chose: standing up a service that answers "yes, released to
      you" is an afternoon's work, and it satisfies every other check on
      this list. That is not a weaker version of check 5, it is the
      whole namespace: such a registrar mints any handle, under
      anybody's email address, for anyone who asks. A registrar with no
      peers configured MUST refuse migration in entirely.
   2. **A bounded fetch.** The request MUST use `https`, MUST NOT follow
      redirects, and MUST NOT be made to an address outside global
      unicast: loopback, link-local (the cloud metadata service lives
      there), private ranges, carrier NAT, the unspecified address,
      IPv4-mapped IPv6 forms, and single-label or reserved-internal
      hostnames are all refused. Address literals MUST be parsed rather
      than string-matched: `169.254.169.254`, `http://2130706433`,
      `http://0177.0.0.1` and `[::ffff:127.0.0.1]` all read as ordinary
      hostnames to a prefix test, and all point inward. The registrar
      MUST bound how much of the answer it will read and how long it
      will wait.
   3. **A signed referral.** The referral MUST be signed, and the
      signature MUST verify against a key the peer publishes out of band
      (§5.3) or a key pinned for that peer in configuration, never
      against a key the response itself declares. Unsigned JSON off a
      socket is not a second party to the story; it is whatever
      answered.
   4. **Signed bytes that name the parties.** The signed document MUST
      name the destination (`registrar`), the issuing peer (`from`), and
      the handle. A referral that does not name its own issuer is
      replayable as though a different registrar had said it; one that
      does not name the handle is replayable for a different name; one
      that does not name the destination is replayable at every
      registrar.

   Without this check the statement is uncorroborated: the handle need
   never have existed at the old registrar, or anywhere.

**The referral.** A referral is a signed document in its own right,
served in place of a card (§5, §5.3):

```json
{ "referral": {
    "handle":     "Coder.ana@example.org",
    "registrar":  "https://naming.example.org",   // where the handle went
    "from":       "https://naming.agentmesh.ai",  // who is saying so
    "operator":   "Ana Lima",
    "issued_at":  "…", "expires_at": "…" },
  "registrar_key": "…", "registrar_kid": "…", "registrar_sig": "…" }
```

`operator` carries the anchor's public label across the move. A registrar
accepting a migration in MUST record an operator name for the arriving
handle (taken from the referral where it is given, otherwise a
placeholder) and MUST let the owner replace it under a verified session,
because §5.1 marks the operator record REQUIRED and a migration brings no
claim ceremony to set one. A verifier MUST reject an expired referral, and
MAY accept one carrying no `expires_at` at all: check 3 has already
excluded strangers, so a peer that predates this version is not the thing
to fail closed on.

Check 5 fixes the order: **out first, then in.** A registrar MAY reject a
migration IN that arrives before the corresponding migration OUT, and the
owner retries after the departure is recorded.

**Pinning is unaffected, by design.** Pins are `handle → agent key`
(§5.3), and re-homing moves custodians, not keys: a consumer's existing
pin verifies identically against the new registrar's card. A resolver
MUST NOT alarm on a change of registrar alone; it MUST alarm on a change
of key, exactly as before. (Registrar *signing keys* are pinned per
registrar, so the new custodian's key is pinned on first sight like any
other registrar's.)

### 5.7 Reversing a move: the anchor owner's window

§5.6 has the *agent key* sign a move, and §7.8 makes executing one an
obligation. Both are deliberate: the key is the agent's identity (SPEC.md
§1.5, invariant 2), and a custodian that can refuse to let a name leave is
not a custodian. But the party a name is anchored to is the mailbox owner
(§3), and a key-signed move does not consult them. Whoever holds the bound
agent key (the owner's own software, a hosted agent service, a stolen
laptop, anyone who ever ran the agent) moves the name with one request,
and §5.6's "MUST NOT re-issue" then bars even the owner from getting it
back. Left there, the two rules together make a key a stronger claim on a
name than the anchor it was minted under, which inverts §3.

The owner's standing is therefore expressed as a reversal, not as a second
signature on the way out. Requiring the owner's signature to leave would
contradict §7.8 and would strand an owner whose mailbox has gone away,
which is precisely the case §3's "lifetime = anchor lifetime" anticipates.

1. **The move is dated.** A registrar recording a migration out MUST
   record when it happened, and SHOULD show the anchor owner, in their own
   roster and history (§6), that the name moved and by when a reversal is
   still possible.
2. **The window.** A registrar MUST offer the anchor owner a bounded
   window in which to reverse a move it recorded (REQUIRED minimum: 30
   days). A reversal restores that registrar's own record of the handle,
   with the name, anchor, claim date, and binding it had before.
3. **Directly verified sessions only.** A reversal MUST be available only
   under a session whose provenance is `email` (§3.1), and only for
   handles the session's own anchor holds. A delegated partner's
   attestation is not the owner's word about where their name lives.
4. **Logged.** A reversal MUST append a history entry (§6) naming the
   destination the move had recorded.
5. **The hold.** After a reversal, a registrar MUST refuse further
   migrations out of that handle for a bounded hold period (RECOMMENDED:
   30 days), so the key that signed the move cannot immediately sign
   another and undo the owner's decision. The owner MAY lift the hold
   under a directly verified session, for the case where they did mean to
   move after all.
6. **Tell the owner.** A registrar SHOULD notify the anchor by mail when
   it records a move, stating the deadline: a window whose clock nobody
   knows has started is not recourse. A failed notification MUST NOT
   prevent or reverse the move itself (§7.8).

**A reversal is not a re-issue.** §5.6's prohibition is unchanged: a
registrar that let a name go MUST NOT hand it to a new claimant, and MUST
NOT let it become claimable again as a fresh claim, not even by its own
owner, whose route back is this reversal and nothing else. Restoring the
record of a departure the anchor owner never authorized is the opposite of
re-issuing. Nothing new is minted, no uniqueness decision is revisited,
and the handle keeps its original claim date and history; the registrar is
correcting its own record, not selling the name twice.

**What a reversal cannot do.** It cannot reach into the new custodian. A
destination that corroborated the move (§5.6) holds its own record, and
this version specifies no registrar-to-registrar revocation (§10), so a
reversal inside the window can leave two registrars answering for one
handle until the destination's record lapses or its operator acts. §5.5
governs that state and favors the owner (the reversing registrar carries
the earliest claim), and a resolver MUST surface the conflict rather than
choose silently. The window is bounded for exactly this reason: it is
recourse against a move the owner did not make, not a standing right to
contradict a custodian the owner did move to.

### 5.8 Renaming within a registrar

*Added 2026-10-05 (0.10).* §5.6 covers a name that moves to another
registrar and keeps its name. This section covers the other case: an agent
given a new name at the same registrar, keeping its key, usually under the
same anchor. The new handle is claimed (§3) and bound (§4) to the same
agent record as the old one. Releasing the old handle at once
(§3) makes every address written down for the agent fail with "no agent by
that name", which is true of the name and false of the agent. A registrar
MAY instead keep the old name working for a window, saying where it went.

1. **The old name's anchor, the same agent.** A rename window MUST be
   opened only on the authority of the anchor the old handle belongs to,
   and only when the new handle is active and bound to the same agent
   record as the old one. The new handle may sit under another anchor; what
   makes the notice true is that both names reach the same agent.
2. **The window.** For the window the old handle MUST resolve exactly as
   before (§5, to the same card) and the resolution SHOULD carry a notice
   beside the card, outside its signed bytes: the new handle and the time
   the old one stops resolving. The card describes the agent, and the
   agent did not change. A registrar MUST bound the window (RECOMMENDED:
   30 days by default, at most 90).
3. **Reverse resolution.** While both handles are bound, resolving the
   agent key (§5) MUST answer the new handle.
4. **The end.** When the window ends the old handle MUST stop resolving,
   whether or not its anchor has released it yet. The anchor MAY end the
   window early by releasing the old handle (§3).
5. **Moved to.** After the window, and for the cooling-off period after
   the old handle's release, a registrar SHOULD answer resolution of the
   old handle with "not found" carrying the new handle (`moved_to`) and
   when the old one stopped (`stopped_at`: the window's end, or the release
   if that came first), distinct from "no agent by that name". This is §5's tombstone signal at
   resolution, with a destination. It is not a referral (§5.6): the name
   did not move to another custodian, and a resolver MUST NOT treat
   `moved_to` as a card or follow it silently; it tells the sender where to
   send instead.
6. **Logged.** Opening a window MUST append a history entry (§6) naming the
   new handle and the end of the window.

The cooling-off period (§3) is unchanged and runs from the release: the old
name is held from everyone, its former owner included, for at least 90 days
after it stops resolving.

## 6. The history log

Every claim, binding (with its proof method), release, move (§5.6) and
reversal of a move (§5.7) appends an entry. Entries are never updated or
deleted. An entry MUST be logged against the handle's own anchor, so that
the one person entitled to read it (the owner, §6 being owner-scoped)
can see it: a move logged against no anchor is invisible to exactly the
party who needs to know their name left.

**The log is not public.** Handles embed the owner's address in the name, so
a world-readable log would let anyone enumerate an owner's entire roster by
filtering on their email. The log is therefore owner-scoped: an owner may
retrieve the full history of their own handles after proving control of the
anchor, and it is not otherwise readable.

**Hash chaining is REQUIRED.** Each entry carries the SHA-256 hash of the
previous entry, computed over a canonical serialization that includes that
previous hash, so the log is a chain and any rewrite of history is
detectable:

```json
{ "seq": 1041, "at": "…", "action": "bound", "handle": "…",
  "detail": { "method": "agent-key" },
  "prev_hash": "b64…", "entry_hash": "b64…" }
```

### 6.1 Erasure

The two requirements above pull against each other. An entry must be
attributable to its anchor, and entries are never deleted, so an entry
that names the anchor in plaintext puts the owner's address in a record
nothing can remove, and for an email-tier handle it does so twice, because
the handle IS `<name>.<email>` (§2). An owner who asks to be forgotten
cannot be. Recording the anchor was never what the chain guaranteed; it is
only how attribution happened to be implemented.

A registrar therefore **MAY** record the owner as a stable opaque
reference (a value derived from nothing, whose mapping to the anchor is
held separately and is ordinarily deletable) in place of the anchor and
the whole handle. Attribution is unchanged: the owner-scoped read resolves
the anchor to its reference and returns the same history, reassembling each
handle from the anchor the request has just proved control of, which is why
the entry never needed to store either. Deleting the mapping severs the
link without touching a hashed byte, so the chain still verifies and the
entries remain, belonging to nobody. A registrar that does this **MUST
NOT** reissue a reference after deleting its mapping: an owner who returns
must be given a new one, or their next claim silently re-links the history
they asked to have erased.

Such a reference is a **pseudonym, not anonymisation**, for as long as its
mapping exists, and a registrar **MUST NOT** describe it as more than that.
Stability is what keeps the log useful (it is what lets one owner's
history be gathered, and lets an owner show which entries were theirs), and
it is the same property that makes the reference a pseudonym. Erasure
happens when the mapping is deleted, not when the reference is issued.

**Both forms are one chain.** A registrar that adopts the opaque form **MUST
NOT** rewrite or re-chain the entries it already holds. Rewriting settled
history to improve privacy is the exact act the chain exists to make
detectable, and a registrar that does it once has taught every future
reader that its log is editable when the reason is good enough. Older
entries keep their plaintext form and cannot be erased; a registrar **MUST**
disclose that residue rather than imply the erasure was total. `prev_hash`
links every entry regardless of form, so the chain stays unbroken across the
change and a verifier walks it end to end; what differs is only the
per-entry canonical serialization, which carries whichever owner pair the
entry itself holds: the anchor and the whole handle, or the opaque
reference and the handle's name with its anchor removed. No entry may carry
both, so a verifier selects the rule from the entry and cannot be misled
about which one applies.

Erasing the log's linkage is one step in erasing an owner, not the whole of
it. A registrar **MUST NOT** report an anchor as forgotten while it still
holds that address elsewhere (in handle records, including released
tombstones, in operator records, in verification state) or while a live
handle spells the address out in its own name.

Public, privacy-preserving verifiability (letting a third party confirm the
registrar has not rewritten history without learning who owns what) is
future work; see §10.

### 6.2 Signed head commitments

Chaining detects a rewrite. It does not detect a **deletion**, and the two
are not the same attack. A chain geneses at its first entry, so a registrar
that discarded its log and began appending again would hold a chain that
verifies perfectly from its new beginning. Nothing in the entries says how
many there are supposed to be.

The gap is wider for the party §6 exists to serve. An owner-scoped read
returns only that owner's entries, and each one carries `prev_hash` and
`entry_hash` pointing at entries the owner is not entitled to read. The
owner can confirm that each entry hashes to what it claims, and cannot walk
the chain those hashes belong to. "The log is append-only" is therefore an
assertion the registrar makes about itself, which a reader has no means to
check.

A registrar **SHOULD** therefore publish, periodically and signed, a
statement of where its chain currently ends:

```json
{ "id": 42, "at": "…", "registrar": "https://…",
  "head_seq": 4207, "entries": 12000, "head_hash": "b64…",
  "kid": "93acdb980800", "signature": "b64…" }
```

The signature is over the newline-joined canonical form

```
pan-log-commitment-v1
<registrar>
<head_seq>
<entries>
<head_hash>
<issued_at>
```

verified with the key whose `kid` matches an entry from §5.3's published key
set. The issuing registrar's own URL is inside the signed bytes for the same
reason a §5.6 referral carries it: without it, a commitment published by one
registrar verifies as a statement about another's chain.

`entries` is the field doing the work. A hash establishes that an entry is
intact; a count establishes that entries are present. A chain that later
reports fewer entries than a commitment already published contradicts a
signature the registrar cannot disown.

Commitments **MUST** be publicly retrievable, and a registrar **MUST NOT**
require authentication to read them. This is not a disclosure: a commitment
is a hash, a count and a sequence number, and names neither a handle nor an
owner, so it coexists with the erasure in §6.1 rather than undoing it. The
requirement is structural, because a commitment is only worth something to a
party who kept an earlier one, and a reader who cannot fetch them cannot keep
any.

A registrar **SHOULD** issue a commitment on a fixed cadence even when the
chain has not changed. A registrar that stopped committing is otherwise
indistinguishable from one with nothing to report, and the cadence is what
makes silence itself legible.

What this does not do: it gives nobody the ability to read entries they are
not entitled to, and it cannot prevent a deletion. It makes a deletion
**detectable by anyone who archived a commitment**, which is the property
chaining alone does not provide.

## 7. Registrar obligations

A conforming registrar:

1. Enforces full-string uniqueness and the cooling-off window.
2. Never parses handles on behalf of consumers, and never exposes an API
   that requires consumers to parse them.
3. Maintains the append-only, hash-chained history log of §6, and serves
   each owner only their own entries.
4. Serves resolution without authentication, and labels every card with its
   binding method.
5. Requires binding proofs per §4: email proof alone never binds to an agent
   record the anchor did not submit.
6. Signs every card and every referral it serves, publishes its signing
   key set out of band (§5.3), and refuses resolution rather than serving
   an unsigned card. A registrar that cannot sign is not degraded, it is
   not conforming.
7. Serves WebFinger for every active handle (§5.2).
8. Executes migration out: on a valid `pan-rehome-v1` statement (§5.6),
   it publishes the redirect, serves the signed referral, and releases
   custody. A registrar that will not let a name leave is not a
   conforming registrar. This obligation is worded so that the reference
   registrar binds itself first: the credibility of the whole namespace
   rests on custodians being fireable. It dates the move, notifies the
   anchor owner where it can, honors the owner's reversal window (§5.7),
   and does not re-point a handle it has already moved.
9. Corroborates migration in: it takes custody only on a signed referral
   from a configured peer, verified against a key that peer publishes out
   of band or one pinned for it in configuration (§5.6, check 5). A
   registrar with no configured peers accepts no migrations in. Custody
   arriving on a self-signed statement alone is not custody, it is a
   stranger's assertion about somebody else's email address.
10. Bounds what an unauthenticated caller can cause: per anchor, per
    caller address, and for the service as a whole (§9).
11. Records every change of a handle's bound agent key and serves the
    history on the card (§4.4): a name that moved to a new key says so to
    the readers the move could otherwise mislead.
12. Where it offers renames within the registrar (§5.8), bounds the window,
    stops resolving the old handle when the window ends, and answers its
    resolution with `moved_to` rather than a card from then on.

## 8. Trust model: read this before trusting a handle

**The registrar is a notary, not an oracle.** An email verification is
witnessed once, by one party; no third party can re-run it. Everyone who
trusts a handle is trusting the registrar's word, and for a handle whose
card says `claimed_via: delegated:<partner>`, the registrar's choice of
witness (§3.1). The history log records
every action under a hash chain, so tampering is detectable, and an owner
can audit the full history of their own handles. But the log is private
(§6), so this version offers no public cross-check on the registrar: a
deliberate trade of external auditability for owner privacy.

**The registrar and the mesh share an operator.** In the AgentMesh
deployment, the party that runs the registrar also runs the transport.
Signed cards authenticate *which* registrar spoke, and the hash-chained
log makes tampering detectable to the owner, but nothing structural
prevents the operator from rebinding a handle. The defense is §5.3's
mandatory resolver pinning: clients *detect* rebinding rather than
trusting the registrar not to. Authority separation is now *specified*:
an anchor domain may answer for itself (§5.2), authority has a defined
order (§5.5), and owners can move custodians (§5.6). Until independent
registrars exist in practice, however, pinning remains the operative
defense, and this document does not pretend otherwise.

**Many registrars, one namespace.** Nothing above requires a single
registrar, and §5.5 defines where authority lies when there are several.
The consumer-side rules generalize cleanly: pin each registrar's signing
key on first sight, scoped to that registrar (§5.3); treat the anchor
domain's WebFinger, where it exists, as outranking any registrar (§5.2);
and treat a two-registrar conflict like a pin mismatch, a loud warning
whose remedy is out-of-band, never silent preference (§5.5). Trusting a
handle still means trusting a registrar's word; with §5.5 the consumer
at least chooses *whose* word, and the owner can fire a custodian who
loses their confidence (§5.6) and reverse a firing they did not order
(§5.7). Authority separation stops being a federation-era aspiration and
becomes a property the owner exercises.

Binding sharpens what a handle claims. An `agent-key` binding is
cryptographic: someone holding the agent's key cooperated with the handle
owner, and that proof does not depend on the registrar's honesty. An
`email-submitter` binding is notarized only.

**The operator name is a label, not an identity.** It is required, stable
across the anchor's handles, set only under a verified session, and
change-logged, which makes it a consistent, auditable claim rather than a
per-message assertion. It is still whatever the mailbox owner chose to
type. The verified fact remains the anchor email (which the handle itself
displays); the name rides on it. Renderers SHOULD source the name from
resolution, never from message contents, so a message sender cannot assert
an operator name at all.

What a handle does **not** prove: that the agent is competent, safe,
endorsed by anyone, or that its capability claims are true. A handle is an
address, not a badge. If you need domain-anchored, publicly re-verifiable,
CA-backed identity, that is what ANS is for; PAN does not reach that bar and
does not try to.

## 9. Security considerations and non-goals

- **Code guessing**: registrars MUST bound verification attempts and rate-
  limit issuance per anchor, per caller address, and for the service as a
  whole (reference: 5 attempts/code, 5 codes/hour/anchor, 15/hour/address,
  500/hour service-wide). Code comparison SHOULD be constant-time. A
  ceiling counted inside one process is not a ceiling for a service that
  runs several copies of itself: it MUST be shared across them, or its
  real value is the configured one multiplied by the instance count.
- **Mail the registrar sends**: verification codes and invitations go out
  from the registrar's own domain on a request that proves little: an
  unauthenticated address for a code, one throwaway mailbox for an
  invitation. The per-anchor ceiling bounds what a single victim receives;
  only the per-address and service-wide ceilings bound what the domain
  sends in total, which is what a rotating recipient list turns into.
  Caller-supplied text (an operator label is 80 characters of whatever its
  owner typed) MUST NOT be interpolated into a subject line.
- **Pairing codes are relayed by humans**: a pairing code gets read aloud
  and pasted, so anyone who sees one may try it. A registrar MUST NOT
  spend a pairing code on a request whose signature does not verify
  (otherwise one unsigned request invalidates the code and forces the owner
  to start over), and MUST bound pairing attempts per caller address.
- **A key is not an anchor**: whoever holds the bound agent key can move a
  name to another registrar (§5.6). That is by design, and §5.7 is what
  keeps it from being theft: the move is dated, the anchor owner is told,
  and they can reverse it inside a bounded window under a directly
  verified session. A registrar MUST NOT make a key-signed move permanent
  and unreviewable.
- **Fetching from a registrar the caller named** (server-side request
  forgery): corroborating a migration in means making an outbound request
  whose target arrived in the request. Restricted to configured peers,
  over https, to globally routable addresses, without redirects and with
  bounded reads (§5.6, check 5), it is a call to a party the registrar
  chose. Without those restrictions it is a probe of the registrar's own
  network on the caller's behalf, and distinct error messages turn it into
  a reachability scanner.
- **Cross-origin**: a registrar's unauthenticated state-changing endpoints
  (code issuance, pairing completion, migration in) SHOULD NOT be callable
  from arbitrary web pages. Nothing is stolen (authorization here is a
  bearer token, never an ambient cookie), but a page that drives those
  endpoints through its visitors' browsers spreads a per-address ceiling
  across a residential address space.
- **Squatting**: full-string uniqueness plus visible anchors makes
  impersonation self-labeling: `support.paypal.attacker@gmail.com` carries
  its own anchor in plain sight. Registrars MAY additionally police names
  but the protocol does not require taste.
- **Email-costume confusion**: handles look like email addresses; mail sent
  to one goes wherever the mail system says, which is unrelated to the
  agent. Registrars SHOULD present handles in contexts that discourage
  mailto interpretation.
- **Delegate credential compromise**: a stolen §3.1 credential could
  always squat new names under emails it names; with §4.3 it can
  additionally bind those names to attacker keys; and at a registrar that
  permits delegated release (§3.1), it can release the handles of any
  anchor it names. Squatting and binding are fully disclosed on cards and
  in the log, and resolver pinning (§5.3) alarms on any rebinding of a
  known handle, so both are detectable and both are reversible. Release is
  neither: the name tombstones and cools off (§3), and §5.6 bars re-issue
  without distinguishing a release the owner asked for from one they did
  not, so there is no route back inside the protocol. That is why §3.1
  makes the permission a MAY rather than a rule, and why a registrar
  granting it should weigh the account-closure case against a destructive
  failure mode it otherwise does not have. What the credential still
  cannot do is unbind (§3.1), so it cannot take an owner's working agents
  off the mesh by detaching their names while leaving the names standing,
  and it cannot move a name or reverse a move (§5.7). Each delegated
  session is bound to one anchor, so every release performed under one is
  attributable in the log to a named anchor and a named partner, never an
  unattributed sweep.
- **Operator credentials are not delegated sessions**: §3.1 bounds what a
  *session* a partner mints can do. A deployment may separately hold
  operator credentials for moderation (taking a name down for cause) or
  support, and those sit outside §3.1 by definition. §8's "trusting the
  registrar's word" already covers them. A deployment that uses one shared
  secret for both collapses the distinction, and collapses it in the record
  as well: one holder can then release a name either as the owner's
  delegate (§3.1) or as the operator, and nothing but its choice of route
  decides which the log says it was. Registrars SHOULD keep the credentials
  separate, MUST log every operator-initiated release in the history log
  naming the handle's owner at the time, and MUST NOT record a release that
  an owner or their delegate asked for as an operator action. An audit that
  cannot distinguish a name its owner gave up from a name the operator took
  is not an audit of operator power.

  *Separate* means the moderation credential's bytes are not the witnessing
  credential's bytes. A deployment that holds two settings and configures
  both to one value has not separated anything, and is in the worse
  position of the two, because it reads as finished. A registrar that
  authorizes moderation with the witnessing credential (whether because no
  moderation credential is configured or because both are set to the same
  value) **MUST** disclose that to its own operator, plainly and where an
  operator will see it, rather than presenting the two as separate. This is
  the one failure in this section that is invisible from outside: no
  partner, consumer, card or log entry can reveal it, so the only party who
  can act on it is the one running the registrar, and the obligation runs
  toward them.
- **Anchor compromise**: whoever controls the mailbox controls its handles.
  Anchor hygiene (2FA on the account) is inherited, which is also PAN's
  strength: it rides the most hardened credential most people already have.
- **Non-goal, domain anchoring**: PAN does not anchor names to domains or
  issue certificates. That is ANS's domain, and PAN defers to it rather than
  duplicating a lighter, weaker version. (Distinguish this from §5.2/§5.5
  domain *sovereignty over resolution*: a domain serving WebFinger decides
  where its anchors' handles resolve (routing authority), but the names
  stay email-anchored and the proofs stay §3/§4's; no certificate or
  DNS-proven identity is introduced.)

## 10. Out of scope

- **Domain-anchored identity**: names proven by DNS or well-known records,
  CA-backed certificates, DANE. Covered by ANS.
- **Discovery**: search and browse across agents. Covered by catalog/search
  layers like ARD. PAN resolves a name you already have.
- **Reachability and messaging**: contacting a resolved agent, including any
  registrar-hosted chat or relay surface, belongs to the messaging protocols
  named in the card's endpoints (A2A, Agent Messaging).
- **A registrar network**: §5.5 defines resolution authority and §5.6
  defines migration between registrars, but a live mesh of referring
  registrars (shared uniqueness enforcement, referral chains, gossip)
  is not specified. One namespace with well-defined precedence is
  claimed; registrar-to-registrar protocol is not, yet. In particular
  there is no way for one registrar to tell another that a move has been
  reversed (§5.7): the two-registrar state a reversal can create is
  surfaced to consumers by resolvers (§5.5), not settled between
  custodians. Peering is configuration, not discovery: a registrar
  accepts migrations only from peers its operator named (§5.6, check 5).
- **Additional anchor proofs**: e.g. OIDC sign-in as a mailbox proof.
- **Public, privacy-preserving verifiability**: letting outside parties
  confirm the registrar has not rewritten history without exposing who owns
  what (e.g. Merkle commitments / SCITT-style inclusion proofs over the §6
  chain).

## 11. Implementations

AgentMesh runs a registrar at `https://naming.agentmesh.ai`. It is the
reference registrar this document cites for its defaults.
<!-- OPEN-REGISTRAR: when the open-source registrar is released, name its public repository here. -->
Live and
verified end-to-end: email-tier claiming, §4.1 agent-key pairing (with the
agent record created from the signed pairing), §5 resolution (card +
WebFinger, signed per §5.3), and the §6 hash-chained, owner-scoped history
log. The AgentMesh Rust SDK carries `examples/pan_pair.rs`, a standalone
pairing signer demonstrating §4.1 without any particular agent host. The
mesh-adapter is the reference resolver: it verifies, pins, and warns per
§5.3.

Also live as of 0.6: the §5.5 authority chain (the reference resolver
probes the anchor domain's WebFinger before the registrar of record, with
a negative cache, and pins signing keys per registrar), the §5.1
home-mesh endpoint field, §5.6 migration out and in with peer-scoped,
signature-verified referrals, and the §5.7 reversal window and post-undo
hold.

Also live as of 0.7: §3.1 delegated release bounded to the session's own
anchor, §6.1 erasure by deleting the owner-reference mapping, and §9's
separation of the witnessing credential from the moderation credential:
two independent settings, with the moderation routes falling back to the
witnessing credential where a deployment has not configured its own and
saying so at every startup, as §9's disclosure rule requires.

Also live as of 0.9: §4.4 key history. The reference registrar records a
key change on re-pairing (direct and delegated) and on detachment, serves
the current claim's changes in the card's `key_history` field, and states
the latest change in plain words on its card page.

Built, not yet deployed (2026-09-27): a delegated
binding may carry `revoke_previous`, recorded on the key change as
`old_key_revoked` and served in `key_history`; the card page then says the
previous key leaked and is revoked. Existing changes read false.

Not yet implemented: the DNS `_pan.<anchor-domain>` route to a registrar
of record (§5.5; the reference resolver uses the anchor-domain probe and
its configured list, not the TXT record), and the consumer half of the
0.6 card fields: the reference resolver verifies the signature and pins,
but does not yet reject an expired card or select a key by `kid` (§5.3).
Implementation status will be updated here as each lands.
