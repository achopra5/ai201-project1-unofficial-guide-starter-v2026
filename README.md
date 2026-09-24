# The Unofficial Guide

Aditya Chopra — Corpus: `city_guides`



---

# Unit 1

## What This Does

This project is a retrieval-augmented generation system built around the
`city_guides` corpus. It answers practical questions about transportation,
food, lodging, seasonal conditions, accessibility, and other details covered
by the regional travel guides. The system retrieves relevant guide sections,
rejects questions that are too far outside the corpus, and uses Gemini to
generate a short answer grounded only in the retrieved documents. Each answer
also names the source files it used.

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

## Chunking Strategy

**Chunk size: 780**
**Overlap: 0**

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

I used a section-based chunking strategy for the city guides instead of the
starter's fixed 800-character windows. The guides are organized with Markdown
headings, so each chunk keeps the document title together with one section.

I used 780 characters as the chunk-size target and no overlap. The sections in
this corpus are already organized around individual topics, so keeping them
intact preserves complete thoughts without duplicating text across neighboring
chunks. All of the section-based chunks fit within that target, with the
longest being 762 characters.

The final corpus produced 94 chunks with an average length of 322 characters.
The shortest chunk was 174 characters and the longest was 762 characters.

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `guide_accessibility.md#0` — produced by: `chunker.py::split_documents`

```
======================================================================
Chunk 1  |  source: guide_accessibility.md#0  |  produced by: chunker.py::split_documents
======================================================================
# Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.
```

**Chunk 2** — source: `guide_corry_vale.md#5` — produced by: `chunker.py::split_documents`

```
======================================================================
Chunk 2  |  source: guide_corry_vale.md#5  |  produced by: chunker.py::split_documents
======================================================================
# Corry Vale

## Where to stay

Perhaps thirty beds in the entire valley, spread across two pubs and a handful of farmhouse rooms. In summer these are booked months ahead. Camping is permitted on two marked fields and nowhere else.
```

**Chunk 3** — source: `guide_givens_mill.md#2` — produced by: `chunker.py::split_documents`

```
======================================================================
Chunk 3  |  source: guide_givens_mill.md#2  |  produced by: chunker.py::split_documents
======================================================================
# Givens Mill

## Getting around

Everything is on one street along the river. The mill is at one end and the church at the other, eight minutes apart. The riverside path continues in both directions for as far as you want to walk.
```

**Chunk 4** — source: `guide_kestrelford.md#4` — produced by: `chunker.py::split_documents`

```
======================================================================
Chunk 4  |  source: guide_kestrelford.md#4  |  produced by: chunker.py::split_documents
======================================================================
# Kestrelford

## What to see

The market square on a Saturday morning is the main event and has run continuously since the 1400s. The parish church has a 13th-century tower you can climb for £2. The old trackbed walk runs six miles to the next village along an easy gradient and is the best half-day here.

```

**Chunk 5** — source: `guide_pellew_sands.md#6` — produced by: `chunker.py::split_documents`

```
======================================================================
Chunk 5  |  source: guide_pellew_sands.md#6  |  produced by: chunker.py::split_documents
======================================================================
# Pellew Sands

## When to go

June and September for the beach without the crowds. July and August are busy and the town is at its most itself, for better and worse. Winter is bleak, largely closed, and has a following among people who like that sort of thing.

```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** How often do buses run from Brightwater to Kestrelford on Saturdays?

**Answer:**

```
Buses run from Brightwater to Kestrelford every two hours on Saturdays
(from `guide_kestrelford.md` and `guide_regional_transport.md`).

Sources retrieved: guide_brightwater.md, guide_givens_mill.md,
guide_kestrelford.md, guide_marchwood.md, guide_regional_transport.md
```

**My relevance cutoff: 0.60**

The five in-corpus questions had best distances ranging from 0.2176 to
0.3962. The five out-of-scope questions had best distances ranging from
0.8026 to 0.9753. There was a clear gap between 0.3962 and 0.8026, so I kept
the cutoff at 0.60. This accepts all five questions the corpus should cover
while rejecting all five clearly unrelated questions.

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---:|
| How often do buses run from Brightwater to Kestrelford on Saturdays? | Yes | 0.2176 |
| What time does Kestrelford's bakery usually sell out? | Yes | 0.3302 |
| What time should visitors arrive at Halden Bay in August to avoid parking problems? | Yes | 0.2825 |
| How long does the train from Brightwater to the regional hub take? | Yes | 0.2597 |
| What can happen to Kestrelford during snowy winter weather? | Yes | 0.3962 |
| What is the capital of Mongolia? | No | 0.8026 |
| How do I change the oil in a diesel engine? | No | 0.8881 |
| Who won the 1994 World Cup? | No | 0.9753 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8350 |
| How do I write a for loop in Rust? | No | 0.8365 |


## How I Used AI

**1.** I used ChatGPT to help analyze why the starter's fixed-size character
chunking was producing weak chunks. It suggested using the Markdown section
structure of the city guides and keeping the document title with each section.
I implemented that strategy, tested the resulting chunks, and then adjusted it
so the 780-character chunk-size target was enforced without changing the source
text.

**2.** I used ChatGPT to help interpret the retrieval distances from my five
in-corpus and five out-of-scope questions. It pointed out the gap between the
highest in-corpus distance, 0.3962, and the lowest out-of-scope distance,
0.8026. I kept the existing 0.60 cutoff because it sits inside that gap, then
verified that all five in-corpus questions were answered correctly and all five
out-of-scope questions were rejected.

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks do not begin or end mid-sentence | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Generated answer contains expected phrase | 4 of 5 | 4/5 | 5/5 | 5/5 | MET |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

### Evidence from the before run

Produced from `results/run_2026-09-23_1923_before.md` using
`run_eval.py::main`, with retrieval from `store.py::search` and chunks from
`chunker.py::split_documents`.

**Criterion 1 — retrieved chunks contain the answer**

For the Saturday bus question, retrieval returned the relevant Kestrelford and
regional transport documents as the top two results:

```text
Question: How often do buses run from Brightwater to Kestrelford on Saturdays?

#   distance   source
1   0.2176     guide_kestrelford.md
2   0.2323     guide_regional_transport.md
3   0.3604     guide_marchwood.md
4   0.3628     guide_givens_mill.md
5   0.3660     guide_brightwater.md

Gate: best distance 0.218 is under the 0.6 cutoff
```

All five test questions retrieved material containing their answers, so this
criterion scored 5/5 in all three runs.

**Criterion 2 — every answer names a source**

Example from run 1:

```text
Kestrelford's bakery usually sells out by 11am.

This information comes from `guide_kestrelford.md` and `guide_eating.md`.
```

All 15 generated answers named at least one source document.

**Criterion 3 — gate stops out-of-corpus questions**

Output from `run_eval.py::check_out_of_scope`:

```text
refused  (best distance 0.803)  What is the capital of Mongolia?
refused  (best distance 0.888)  How do I change the oil in a diesel engine?
refused  (best distance 0.975)  Who won the 1994 World Cup?
refused  (best distance 0.835)  What is the recommended dosage of ibuprofen for a headache?
refused  (best distance 0.836)  How do I write a for loop in Rust?
-> gate refused 5 of 5
```

Because retrieval and the gate are deterministic, this same 5/5 result applies
to all three run columns.

**Criterion 4 — sampled chunks do not begin or end mid-sentence**

Output from `chunker.py::split_documents`:

```text
Chunk 1 | source: guide_accessibility.md#0

# Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.

Chunk 2 | source: guide_corry_vale.md#5

# Corry Vale

## Where to stay

Perhaps thirty beds in the entire valley, spread across two pubs and a handful
of farmhouse rooms. In summer these are booked months ahead. Camping is
permitted on two marked fields and nowhere else.

Chunk 3 | source: guide_givens_mill.md#2

# Givens Mill

## Getting around

Everything is on one street along the river. The mill is at one end and the
church at the other, eight minutes apart. The riverside path continues in both
directions for as far as you want to walk.

Chunk 4 | source: guide_kestrelford.md#4

# Kestrelford

## What to see

The market square on a Saturday morning is the main event and has run
continuously since the 1400s. The parish church has a 13th-century tower you
can climb for £2. The old trackbed walk runs six miles to the next village
along an easy gradient and is the best half-day here.

Chunk 5 | source: guide_pellew_sands.md#6

# Pellew Sands

## When to go

June and September for the beach without the crowds. July and August are busy
and the town is at its most itself, for better and worse. Winter is bleak,
largely closed, and has a following among people who like that sort of thing.
```

All five sampled chunks begin and end on complete sentence or section
boundaries, so this criterion scored 5/5.

**Criterion 5 — generated answer contains the expected phrase**

Four of the five answers in run 1 contained the expected phrase exactly. The
Halden Bay answer was:

```text
To avoid parking problems in August, visitors should arrive before 10 am
(or plan to use the overflow lot).

This information comes from `guide_seasons.md` (and is also mentioned in
`guide_halden_bay.md` and `guide_regional_transport.md`).
```

`questions.py` defines the expected phrase as:

```text
before 10am
```

Because run 1 generated `before 10 am` with a space, that run scored 4/5 when
the criterion was applied literally. Runs 2 and 3 used `before 10am` and scored
5/5.

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunks contain the answer | MET | All five test questions retrieved at least one chunk containing the answer in every run, exceeding the 4-of-5 target. |
| 2 | Every answer names a source | MET | All 15 generated answers named at least one source document, meeting the 5-of-5 target in all three runs. |
| 3 | Gate stops out-of-corpus questions | MET | The relevance gate refused all 5 out-of-scope questions, exceeding the 4-of-5 target. |
| 4 | Sampled chunks do not begin or end mid-sentence | MET | All 5 sampled chunks began and ended on complete sentence or section boundaries, exceeding the 4-of-5 target. |
| 5 | Generated answer contains expected phrase | MET | The three runs scored 4/5, 5/5, and 5/5. Since the target was at least 4 of 5, it held in every run. |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

No criteria were missed in the before run. All five original targets were met
across all three runs, so there is no failed pipeline stage to diagnose.

The results suggest that some of my original targets were conservative.
Criterion 1 is the one I would tighten most. It currently requires at least
4 of 5 questions to have an answer-bearing chunk somewhere in the retrieved
results. Because the system achieved 5/5 consistently, a stronger version
would require all 5 questions to have an answer-bearing chunk within the top
3 retrieved results.

The retrieval output also showed why this would be a more useful test. For the
Kestrelford Saturday bus question, the first two results were directly relevant,
but lower-ranked results included documents such as `guide_marchwood.md` and
`guide_givens_mill.md`. The current criterion counts this as a complete success
as long as the answer appears somewhere in the top five, even though ranking
quality could still be improved.

Criterion 5 also exposed a measurement limitation rather than a system failure:
one correct Halden Bay answer said `before 10 am` while the expected phrase was
`before 10am`. The answer was semantically correct, so I would not diagnose
that as a generation failure.

## The Improvement

**What I changed:**

I reduced retrieval `TOP_K` from 5 to 3, so the generator now receives only
the three highest-ranked chunks instead of the top five.

**Why I picked it:**

The before run showed that the answer-bearing chunks were consistently ranked
near the top, while some lower-ranked results were only loosely related to the
question. For example, the Kestrelford Saturday bus question had the two
directly relevant documents as its first two results, followed by less relevant
documents such as `guide_marchwood.md` and `guide_givens_mill.md`.

I chose this change to test whether using fewer, more focused retrieved chunks
can preserve answer accuracy and source attribution while reducing irrelevant
context. No other part of the system was changed.

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks do not begin or end mid-sentence | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Generated answer contains expected phrase | 4 of 5 | 5/5 | 5/5 | 4/5 | MET |

### Evidence from the after run

Produced from `results/run_2026-09-23_2006_after.md` using
`run_eval.py::main` with `TOP_K = 3`.

For the Saturday bus question, the system retrieved three sources instead of
five:

```text
guide_kestrelford.md
guide_marchwood.md
guide_regional_transport.md
```

The generated answer remained correct:

```text
Buses run from Brightwater to Kestrelford every two hours on Saturdays.

Source: guide_kestrelford.md
(and also mentioned in guide_regional_transport.md).
```

All five test questions continued to retrieve answer-bearing material within
the top three results. All 15 generated answers named at least one source, and
the relevance gate still refused all 5 out-of-scope questions.

Criterion 5 scored 5/5, 5/5, and 4/5. In run 3, the Halden Bay answer said
`before 10 am`, while the expected phrase in `questions.py` is `before 10am`.
The generated answer was still semantically correct.

**Did it help?**

Yes. Reducing `TOP_K` from 5 to 3 preserved the same acceptance-criterion
performance while sending less retrieved context to the generator. The before
evaluation used 9,514 tokens, while the after evaluation used 6,692 tokens.

The change therefore reduced the amount of retrieved context passed to the
generator without reducing answer correctness, source attribution, or
relevance-gate performance. It did not solve the brittle exact-phrase
measurement in criterion 5 because that issue comes from formatting differences
such as `10am` versus `10 am`, rather than retrieval quality.

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
