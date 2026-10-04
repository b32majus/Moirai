# Moirai — Product Vision V0

## 1. Why this exists

Moirai exists to reduce the cognitive and practical friction of dressing well with a real, finite wardrobe.

The user’s problem is not lack of interest in fashion content. It is the absence of a reliable decision system at the moment a clothing decision has to be made.

That currently creates four recurring failure modes:

1. **Combination failure** — difficulty seeing which existing pieces work together.
2. **Selection repetition** — a small number of safe combinations are reused while much of the wardrobe remains inactive.
3. **Context mismatch** — uncertainty about what is appropriate for a specific professional, social or travel context.
4. **Purchase fragmentation** — new clothes are considered item-by-item rather than as additions to an existing wardrobe system.

The desired product should externalize these decisions while still preserving personal taste and user control.

---

## 2. Product promise

> Given what I actually own, who I am, where I am going and how I want to feel, help me make a good clothing decision with as little effort as possible.

Moirai is therefore closer to a **personal wardrobe operating system / stylist with memory** than to a fashion recommendation feed.

---

## 3. Core user jobs

### J1 — Decide what to wear

Examples:

- “What should I wear today?”
- “I have a normal workday.”
- “I have a senior meeting and then an informal dinner.”
- “I want to look polished without looking overdressed.”

Expected output: one or several complete outfit options based on owned items, with the right level of explanation.

### J2 — Start from one item

Examples:

- “I want to wear these trousers today.”
- “Build three options around this shirt.”

Expected output: combinations using actual inventory, including shoes/accessories when relevant.

### J3 — Understand and clean the wardrobe

The system should help distinguish:

- **KEEP — core**: works and deserves a permanent place;
- **KEEP — secondary**: useful, even if not frequently used;
- **RESTYLE**: good item that is underused because combinations are unclear;
- **ALTER / REPAIR**: worth keeping if adjusted or repaired;
- **REPLACE**: function is useful, current item is not;
- **EXIT**: no longer useful, suitable or wanted;
- **UNCERTAIN**: requires a real-world try-on before a decision.

The audit should not reduce everything to “fashionable/outdated”. It should consider fit, comfort, condition, style alignment, combinability, usage and functional role.

### J4 — Discover wardrobe gaps

The system should identify bottlenecks, not merely suggest more shopping.

Example:

> “You do not need another printed blouse. A neutral mid-layer would unlock many more combinations across your current trousers and dresses.”

### J5 — Evaluate purchases before buying

Given an image or link to a candidate item, assess:

- fit with personal style;
- redundancy with owned items;
- number and quality of outfits it unlocks;
- contexts covered;
- whether it fills a real gap;
- whether a different type of item would have more wardrobe value.

### J6 — Pack for travel

Inputs may include:

- destination;
- duration;
- weather;
- event schedule;
- baggage constraints;
- comfort requirements.

The output should minimize unnecessary pieces while maximizing reuse across planned contexts.

### J7 — Learn from actual behaviour

After an outfit:

- Was it worn?
- Was it comfortable?
- Did the user like it?
- Would she repeat it?

The system should use this to improve future choices instead of treating each request as a blank slate.

---

## 4. Inventory model — minimum useful concepts

A garment should retain its image(s) and also a structured representation.

Candidate fields include:

- stable item ID;
- category / subtype;
- dominant and secondary colours;
- pattern;
- material / fabric characteristics;
- silhouette / cut / fit;
- formality;
- seasonality;
- compatible contexts;
- condition;
- comfort;
- subjective fit / “how I feel in it”;
- favourite status;
- usage history;
- photo(s);
- notes / constraints.

Not all fields need manual entry. The product should automate objective extraction and reserve user effort for subjective information that only the user can provide reliably.

### Objective vs subjective

**Good candidates for automated image extraction:** category, colour, pattern, visible garment type and possibly fabric/style cues.

**Good candidates for user confirmation:** comfort, perceived fit, emotional preference, contexts the user accepts, whether an item is actually flattering/useful, and practical restrictions.

---

## 5. Style profile

Before a full wardrobe audit, Moirai must establish the user’s target style.

The profile should capture at least:

- desired appearance / identity;
- everyday versus professional formality;
- comfort threshold;
- preferred and disliked colours;
- silhouettes and proportions;
- garment types never or rarely worn;
- footwear tolerance;
- jewellery/accessory habits;
- willingness to follow trends versus preference for a stable style;
- typical professional/social contexts;
- “I like this on someone else but would never wear it” distinctions.

The system should optimise for **the user’s life**, not for abstract fashion rules.

---

## 6. Recommendation contract

A useful recommendation should:

1. use real wardrobe item IDs;
2. never present an invented item as if owned;
3. distinguish “use this owned item” from “a future purchase could fill this gap”;
4. consider context and comfort, not colour matching alone;
5. include accessories when they materially improve the outfit;
6. offer alternatives when ambiguity is useful;
7. explain why an outfit works when explanation adds learning value;
8. learn from rejection instead of simply generating more random options.

A validator should reject invalid outputs that reference nonexistent inventory items or incomplete combinations.

---

## 7. Key product behaviours

### Daily styling

Context + weather + preferences + available wardrobe + recent use → outfit options.

### Item-first styling

Chosen item → several valid combinations using owned pieces.

### Wardrobe audit

Style profile + garment state + usage + combinability → keep/restyle/repair/replace/exit/uncertain.

### Travel mode

Trip schedule + destination weather + luggage constraint + wardrobe → compact capsule and day-by-day plan.

### Shopping mode

Candidate item + current wardrobe + gaps + style profile → buy / consider / reject, with reasoning.

---

## 8. Non-goals for V0

Moirai V0 does **not** need:

- a fashion social network;
- trend feeds;
- ecommerce integrations;
- influencer content;
- automatic purchasing;
- virtual try-on;
- body scanning;
- complex recommendation embeddings by default;
- a vector database simply because AI is involved;
- a swarm of specialised agents;
- enterprise-scale ingestion;
- perfect recognition of every visual fashion attribute.

The target is useful personal decision support, not fashion-tech maximalism.

---

## 9. Success criteria for the first real pilot

The pilot is successful if, with a limited representative wardrobe sample, the user can repeatedly obtain recommendations she would genuinely wear.

Minimum evidence:

- recommendations use real items;
- item-first combinations are useful;
- outputs reflect context rather than generic fashion advice;
- feedback changes later recommendations in a sensible direction;
- browsing/editing inventory is not more work than the value it creates;
- the system reveals at least some underused but useful items or genuine wardrobe gaps;
- the user prefers using the system to solving the same decision manually.

The strongest validation is not “the AI can make an outfit”. It is:

> “Yes. That feels like me, and it made the decision easier.”
