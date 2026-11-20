# Architecture, decisions and tradeoffs

## Source and scope

This explanation is based on the private `content-engine` repository's `agent-studio-v1-phase1-working` snapshot, dated 26 October 2026. That snapshot contains a WordPress interface, WordPress PHP proxy, frontend JavaScript, and an n8n Creative Director workflow. The private source repository also contains a separate Notion content-capture service; that is not part of this public example.

The public demonstration is an independent synthetic implementation, not a redacted export of the original workflow.

## Private system: observed Phase 1 sequence

1. A person prepares and approves an Idea Brief in a WordPress interface.
2. A WordPress REST proxy checks permissions and a REST nonce before forwarding the brief to n8n using a server-held authorization token.
3. n8n validates and normalizes the brief, handles duplicate IDs, initializes article state and checks Phase 1 eligibility.
4. A Creative Director agent establishes structured editorial direction, with bounded Researcher assistance available.
5. Deterministic validators check the proposal contract before storing the direction and marking the article as awaiting human editorial approval.
6. The WordPress interface polls an authenticated status endpoint and presents the structured proposal for editorial review.

The source snapshot explicitly states that Phase 2 approval was disabled, Writer and Editor were not used in Phase 1, and publication was not permitted. Treating the entire workflow as autonomous or fully deployed would be inaccurate.

## Why these boundaries matter

**Input validation before AI.** A malformed or duplicate brief should fail predictably rather than be interpreted creatively by a model.

**Structured output instead of only prose.** Editorial direction has identifiable fields, making it possible to review a proposal and reject incomplete output before continuing.

**Human authority is a state transition.** A descriptive model response cannot itself authorize drafting or publication. Approval requires a deliberate transition enforced outside the language model.

**Narrow roles.** The Creative Director has editorial responsibility. Researcher provides supporting material. Drafting and reviewing are separately scoped work, not permissions automatically granted to the Director.

**Private integration boundary.** The browser calls WordPress; WordPress holds the upstream secret. A webhook token should never be shipped to the frontend.

## Tradeoffs and known limitations

- The historical frontend is a large, self-contained JavaScript snippet. That made WordPress integration practical, but it reduces modularity and testability.
- Local browser storage holds working article state in the prototype. That is convenient for experimentation, but not a robust multi-device or confidential-content datastore.
- The Phase 1 review UI is not equivalent to a complete approval and publication system.
- A successful recorded Phase 1 execution establishes that one path worked, not that all failure cases or later phases were tested.
- Real model outputs require independent validation. Citations, claims and editorial judgments are not automatically trustworthy.

## The public example

The importable demonstration uses four nodes: Manual Trigger, Synthetic Brief, Validate and Propose, Stop at Human Review. It runs only on local invented data and creates an example of the `awaiting_human_editorial_approval` state. It makes no model calls and contains no working approval-to-publication path.

This simplification is intentional. It isolates the most important product decision: **who is allowed to move the workflow forward, and under what conditions**.

## Not included

Production hostnames and webhooks, tokens, account IDs, WordPress page IDs, internal data-table identifiers, client materials, unpublished editorial content, real prompt chains and private repository history.
