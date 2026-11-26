## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 4b. Explain Simply (default when asked "what is X / how does X work")

**Default to plain, everyday explanations — don't dump jargon.**

- Open with the **real-world problem** being solved, written conversationally — not with a technical definition.
- Use **one concrete, everyday analogy** (burglar alarm, lock, key...) to anchor the idea. Put it in a `>` blockquote so it stands out.
- Frame it as **"before / after"** or **"today / with this solution"** to make clear what changes.
- Keep **sentences short**. Keep technical terms / identifiers only when needed, and explain them immediately in plain words.
- Always include a **"limits / be honest"** section: what this does NOT do — brief, no sugarcoating.
- Close with the **practical reality / next step** (how far along, how costly), then ask whether they want to go deeper.
- Only go deep technically (files, lines, offsets, contracts) **when asked**; the first pass always favors the easy-to-understand version.

Measure of success: a non-expert finishes reading and still grasps "what it solves, what it changes, where it falls short".

## 5. Clean Architecture

### 5.1 Decision rules

- Source dependencies point inward. Domain and use cases must not import frameworks, databases, web, UI, queues, service clients, device, vendor, or infrastructure details.
- Entities guard enterprise invariants; focused use cases orchestrate application actions with plain input and output models.
- Frameworks, databases, web delivery, messaging, filesystems, clocks, networks, services, and hardware sit behind policy-owned ports and outer-layer adapters.
- Controllers, presenters, gateways, service listeners, mappers, and hardware adapters translate; they do not own business rules.
- Organize by use case, feature, or business capability. Avoid generic technical buckets, god services, shared utility escape hatches, and sideways coupling.
- Choose the lightest enforceable boundary that preserves likely change independence; a service, package, diagram, or folder name is not enough.
- Test policy through entities, use cases, and boundary contracts without real frameworks, databases, networks, services, or hardware.

### 5.2 Trigger rules

- When framework, ORM, request, response, schema, transport, config, vendor, or hardware types enter core policy, move translation outward.
- When controllers, jobs, handlers, gateways, repositories, SQL, presenters, service listeners, or `*Service` classes grow business rules, move policy inward and split by use case.
- When core code constructs or calls volatile details directly, define an inward-owned port and wire the concrete implementation at the edge.
- When a shortcut bypasses a use case, crosses layers, creates a cycle, or hides coupling in `common` or `utils`, restore dependency direction and ownership.
- When constraints force a compromise, keep it outermost, name the violation, and preserve a future path to separation.

### 5.3 Final checklist

- Policy independent of details?
- Dependencies inward?
- Use cases visible?
- Adapters humble?
- Boundaries enforced?
- Core tests detail-free?
