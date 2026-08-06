---
name: design-signals
description: Design or review Saber signal and enrichment definitions for company or contact research. Use when turning a business need into a focused question, choosing an answer type, splitting a broad request, writing or checking a customer JSON Schema, or preparing definitions for signal activation.
---

# Design Signals

Produce creation-ready Saber definitions. Define the research contract only. Leave execution, subscriptions, and credit use to the activation skills.

## Workflow

1. Identify the decision or action the answer must support and whether the subject is a company or contact. Infer clear context. Ask one concise question only when a material ambiguity remains.
2. Separate facts that need different research, change at different rates, or support different decisions. Keep related fields together when they form one answer.
3. Write one direct question for each definition:
   - Use `this company` or `this contact`.
   - Ask for the information, not the final business decision.
   - Define a role, product, geography, entity, unit, or time period when it changes the answer.
   - Omit research instructions. Saber handles source discovery, verification, conflicts, and uncertainty.
   - Avoid absolute terms such as `all`, `complete`, and `exact` unless the scope is bounded and the requirement is necessary.
4. Choose the simplest answer type that preserves the distinctions the user needs.
5. For `json_schema`, read [references/json-schema.md](references/json-schema.md) and apply every rule.
6. Return the definitions in the required output shape. Add rationale only when the user asks for it.

## Answer Types

| Answer type | Use for |
|---|---|
| `open_text` | One short text value |
| `number` | One numeric value |
| `boolean` | A yes or no result where unavailable evidence can safely behave like `false` |
| `list` | A list of short text values |
| `percentage` | One percentage from 0 to 100 |
| `currency` | One monetary amount with its currency |
| `url` | One web address |
| `contacts` | People who match defined roles or criteria |
| `contact_posts` | Posts published by a contact |
| `contact_engagements` | Posts a contact commented on or reacted to |
| `json_schema` | Related fields, named states, nested data, or an automation contract |

Use `json_schema` with an enum such as `yes`, `no`, and `unknown` when missing evidence must remain distinct from a negative result. Use a boolean only when collapsing `unknown` into `false` is acceptable.

## Required Output

For one definition:

```text
Name: <short descriptive name>
Question: <direct question>
AnswerType: <supported answer type>
OutputSchema: <JSON object, only for json_schema>
```

For several definitions, number and repeat this block. Stop when every definition is focused, preserves the states needed by its consumer, and is ready to create without further rewriting.
