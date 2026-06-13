---
name: tb-flow-first-explainer
description: Use when tb asks to understand or explain a technical concept, code path, system component, architecture mechanism, distributed-system behavior, or framework internals.
---

# TB Flow-First Explainer

## Overview

Explain by making the object move through a concrete chain. Start from visible structure and working units, then discover roles, mappings, dependencies, anchors, and only then design purpose.

This is for tb's learning style. Avoid textbook-first, goal-first, or definition-heavy explanations unless tb explicitly asks for them.

## When To Use

Use for questions like:

- "X is what?"
- "Help me understand this module / component / mechanism."
- "Why does this thing exist?"
- "Walk me through this code path."

This is especially useful when the subject has layered abstractions, internal working units, ownership, state, dependency, control flow, data flow, or runtime behavior.

Do not use for tiny factual answers, commands, translations, or cases where tb asks for a formal definition, proof, checklist, or implementation only.

## Default Answer Shape

When the user asks for an explanation and gives no stricter format, use this shape:

1. **Concrete role**: "First treat X as..."
2. **Smallest useful unit**: what object, record, task, file, block, node, message, page, partition, token, or state does it directly touch?
3. **Outside vs inside**: what name or abstraction is exposed, and what actually works underneath?
4. **One live chain**: follow a request, record, task, event, address lookup, state change, or function call as it moves.
5. **Dependency / anchor**: in that movement, where does X get identity, address, state, ownership, membership, coordination, or recovery information?
6. **Break point**: without X, where would the chain break, become ambiguous, duplicate work, lose state, or concentrate pressure?
7. **Then name the purpose**: only now summarize the design goal and official terms.

Use headings only when they help. The important thing is the order of discovery, not the literal section names.

## Core Shape

Prefer this order, adapting freely to the object:

1. Give a concrete role: who is this thing, what does it directly do, what does it manage, receive, expose, translate, store, schedule, or execute?
2. Shrink the grain: identify the smallest useful working unit it directly touches.
3. Map outside to inside: what name or abstraction is visible externally, and what internal object actually works?
4. Start from a visible structure or observed fact, not a top-down design goal.
5. Walk one live chain: who finds whom, writes where, reads from where, registers where, waits on what, notifies whom, or hands off to whom?
6. Discover dependency and anchor points inside that chain: where does the component get addresses, state, identity, ownership, membership, or recovery information?
7. Ask "without it, where would the chain break or concentrate pressure?"
8. Then summarize the design purpose: throughput, availability, scaling, decoupling, consistency, recovery, isolation, etc.

The answer should feel like "follow the thing as it moves", not "list all components and properties".

## Role Vocabulary

Choose a concrete role that fits the object. Do not force everything into "manager".

Useful roles include:

- Manager / owner: keeps authority over some working unit.
- Entry point: receives outside interaction and routes it inward.
- Registry / directory: tells others where a living thing or state currently is.
- Scheduler / allocator: decides where work or resources go.
- Executor / worker: does assigned work.
- Translator / planner: converts one representation into another.
- Ledger / state source: preserves the fact that something happened or is true.
- Boundary / protocol: defines what can cross between two sides.
- Pipeline / channel: carries data, events, or control forward.

## Style Rules

- Use concrete nouns before abstract goals.
- Explain from mechanism to reason: "we see partitions; now ask what they are and why they help" before "partitions exist for throughput".
- Let dependencies emerge from the flow. Do not dump a static dependency table first.
- Keep roles small enough to connect: "Broker manages stored partition data" is better than "Broker is a distributed message service node".
- Use short conversational bridge sentences: "Now this creates a problem..." "So the next question is..." "This is where the anchor changes..."
- Preserve technical accuracy; after the flow is clear, name the official terms.

## Source Code Explanations

For code paths, use the same style but anchor it in real calls:

1. Start at the actual entry point, caller, route, command, handler, or test.
2. For each important function/class, say what it receives, what it directly changes or produces, and who it hands control/data to next.
3. Identify the smallest working unit in the code: object, row, event, DTO, AST node, request, task, buffer, config, transaction, etc.
4. Let module responsibility emerge after the call chain is clear.
5. Avoid starting with package-layer summaries unless the user asks for architecture first.

## Common Mistakes

- Starting with "In order to achieve X, the system introduces Y." That is a designer's path; tb usually needs "I see Y, what is it, how does it connect, and only then what purpose does it serve?"
- Listing all dependencies upfront. Instead, walk the component until it must ask for an address, state, owner, registry, or coordinator.
- Staying at a slogan level. Push down until there is a real working unit, object, file, partition, block, pod, task, record, page, node, or message.
- Treating normal/failure paths as mandatory headings. Use them when relevant, but the deeper invariant is flow-through reasoning.

## Bad / Good Direction

Bad:

```text
Kafka uses partitions to improve throughput and scalability.
```

Good:

```text
Start with the thing you can see: a Topic is not one physical chunk. Under it are partitions. A partition is the real data slice a Broker stores and serves. Once a Topic is split into partitions, those slices no longer have to sit on one Broker, so reads and writes can spread out. After that chain is clear, call the design purpose throughput and scalability.
```

Bad:

```text
ZooKeeper provides high availability for Flink JobManager failover.
```

Good:

```text
Start with TaskManager trying to find a JobManager. Without HA, the fixed JM address is the anchor, so the chain breaks when that JM dies. With ZooKeeper, the stable address is ZK; the current JM address becomes data registered inside ZK. TM follows ZK to the current JM, and if that registration changes, TM can follow the chain again.
```

## Mini Examples

Broker:

```text
Start with the thing you can see: a Topic is not one solid physical object. It is exposed as a name, but internally it lands on partitions. A Broker is the machine-side manager for some of those partitions. Once you notice partitions can live on different Brokers, the next question is what that buys: reads and writes can spread out. Then replicas explain availability: the same partition can have a Leader and Followers, so writes have a clear landing point and another copy can take over later.
```

ZooKeeper with Flink:

```text
Start with TaskManager moving through startup. It cannot just "know" the active JobManager. Without HA, its anchor is a fixed JM address; if that JM dies, the chain breaks there. With ZooKeeper, the stable thing becomes the ZK address. TM asks ZK who the current JM is, connects to that JM, and watches the registered address. When the JM disappears, the watched state changes, so TM asks ZK again. The anchor moved from a replaceable JM to a steadier registry.
```
