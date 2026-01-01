When we build AI features, we often pass structured data (DTOs, lists, tables) to an LLM. The default is usually **JSON** because it’s easy to produce and debug.

But JSON wasn’t designed to be token-efficient for LLMs.

**TOON (Token-Oriented Object Notation)** is a format designed for communicating structured data to LLMs with less token overhead than JSON—especially for **arrays of similar objects** (rows in a table).

This article focuses on:
- why JSON wastes tokens in prompts
- what TOON changes
- how to compare TOON vs JSON fairly
- how to apply TOON in ABP without changing your public APIs

---

## Why JSON is inefficient for LLM prompts

JSON is excellent for APIs, but expensive for prompts because it includes many tokens that add structure rather than meaning:

### 1) Repeated property names
If you send a list of 200 records, each record repeats keys like `"id"`, `"name"`, `"creationTime"`, `"status"`.

### 2) Structural punctuation
Characters like `{ } [ ] , : " ` are necessary for parsers, but they add tokens that don’t help the model understand your data.

### 3) Nested objects amplify overhead
The more nesting you have, the more braces and repeated keys you pay for.

This is why “just send the DTO as JSON to the LLM” can unexpectedly inflate prompt token count.

---

## What TOON changes

TOON represents the same JSON data model (objects, arrays, primitives) but optimizes the way structured data is written so that prompts often contain **less repeated text** and **less structural clutter**.

The most useful shape (and the one you’ll feel immediately in real products) is:

### Arrays of similar objects → tabular representation
Instead of repeating keys in every row, TOON can define the “columns” once and then list rows.

---

## Practical comparison: JSON vs TOON (same data)

Let’s use a realistic “records list” shape (the kind you commonly pass to LLMs): timestamps + user + text.

### JSON (typical)
```json
[
  {
    "creationTime": "2026-01-01T10:15:00Z",
    "userName": "admin",
    "text": "On mobile the Post button disables randomly..."
  },
  {
    "creationTime": "2026-01-01T10:20:00Z",
    "userName": "hamza",
    "text": "Please add pagination for large comment lists..."
  }
]
````

### TOON (conceptual tabular array)

> Exact syntax depends on the encoder you use; the important part is the structure:
> define columns once, then provide rows.

```text
# records (tabular)
[creationTime userName text]
[
  ["2026-01-01T10:15:00Z" "admin" "On mobile the Post button disables randomly..."]
  ["2026-01-01T10:20:00Z" "hamza" "Please add pagination for large comment lists..."]
]
```

<img width="1742" height="856" alt="Screenshot-2025-11-19-234750" src="https://github.com/user-attachments/assets/e513900a-6414-4ada-a86a-5da8768ec691" />

### Why TOON is often more token-efficient here

* JSON repeats `"creationTime"`, `"userName"`, `"text"` in every object
* TOON pays those field names once, then streams rows
* For larger lists (50–500 rows), repeated-key overhead becomes significant

---

## Where TOON helps most (and where it doesn’t)

### TOON usually helps when

* You send **lists of records** (audit logs, tickets, comments, search results, transactions)
* You return structured results to the model (tool output / function output)
* You frequently hit prompt size limits or cost issues

### JSON is still fine when

* Payloads are tiny (1–3 objects)
* Structures are deeply nested and irregular
* Human readability of raw prompt payloads is more important than tokens

---

## How to compare TOON vs JSON fairly

To avoid misleading comparisons, keep these rules:

### 1) Compare against minified JSON

Pretty JSON is larger than the JSON you’d actually send. Always compare TOON against **minified JSON**.

### 2) Same data, same ordering

Ensure records are identical, same order, same quoting rules, same escaping.

### 3) Use the same tokenizer as your target model

Token counts differ by model/tokenizer. Use the tokenizer that matches your provider/model whenever possible.

### 4) Measure at multiple sizes

The win usually grows with the number of rows:

* 10 rows
* 50 rows
* 100 rows
* 200 rows

---

## A simple “real numbers” section template (fill after measuring)

Once you’ve measured your real payloads, include a short results section:

### Token comparison results

* Dataset: `{N}` records
* Fields: `{field list}`
* JSON (minified) tokens: `{JSON_TOKENS}`
* TOON tokens: `{TOON_TOKENS}`
* Reduction: `{PERCENT}%`
  
<img width="2000" height="1000" alt="02-token-comparison" src="https://github.com/user-attachments/assets/a44fd1ea-43e4-41fe-ba2f-f30cd0356b4e" />

---

## How TOON fits into ABP apps

ABP applications already have clean separation between:

* API contracts (DTOs)
* application services
* integrations

That makes it easy to adopt TOON *without changing your API*:

### Recommended rule

* Keep your ABP HTTP APIs as **JSON**
* Use TOON only in the **AI boundary**:

  * prompt context formatting
  * tool output formatting before sending back to the model

### Clean layering idea

Create a small abstraction like:

* `IPromptContextFormatter` or `IToonFormatter`

So the rest of your application stays provider-agnostic and format-agnostic.

---

## Migration strategy (safe adoption)

If you’re introducing TOON into an existing AI feature:

1. Add TOON formatting behind an interface
2. Keep a fallback to minified JSON while you validate correctness
3. Add a feature flag:

   * `UseToonForAiContext = true/false`
4. Measure token & latency changes

This lets you roll TOON in gradually and safely.

---

## Conclusion

JSON is a great API format, but for LLM prompts it often carries unnecessary token overhead—especially for large lists of similar objects.

TOON is a practical alternative for the AI boundary: it preserves structure while often reducing repeated-key and punctuation overhead. The best way to adopt it in ABP is not to replace JSON everywhere, but to **format AI context** as TOON while keeping your public APIs unchanged.
