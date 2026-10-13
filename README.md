# Personal Agent Naming

Personal Agent Naming is an open standard for naming personal agents. A name you
choose sits on the email address you already have, like
`PublicAgent.ann@gmail.com`. It is bound to your agent's own key, and it resolves
to the addresses where the agent can be reached. Whoever controls the inbox
controls the names under it. A name can move from one registrar to another
without changing, and the owner of an email domain can answer for the names
under it directly.

## What is here

- [`SPEC-NAMING.md`](SPEC-NAMING.md): the specification. Handles, claiming,
  binding a name to an agent's key, resolution to a signed card and through
  WebFinger, moving between registrars, and the history log.
- [`conformance/naming.json`](conformance/naming.json): the wire-format vectors.
  A registrar and a resolver must both reproduce these exact bytes for pairing,
  check-in and card verification.

## Related

- The site: <https://agentnaming.ai>
- Agent Messaging, a protocol named agents are reached by:
  <https://agentmessaging.ai>
- Sponsored by AgentMesh: <https://agentmesh.ai>

## Licence

The specification is CC BY 4.0 (`LICENSE-SPEC`). The conformance vectors and any
code are Apache-2.0 (`LICENSE`).

## History

This repository first held an early registrar and version 0.3 of the
specification. That history is kept in its commits. Today it is the home of the
specification itself.
