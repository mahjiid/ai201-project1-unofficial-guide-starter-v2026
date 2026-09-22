# The Unofficial Guide

**Abdulrasheed Abdulmajeed — Corpus: campus_life**

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This project builds a question-answering system over a campus-life corpus containing information about courses, housing, dining, registration, and other student experiences. The system loads and cleans the documents, splits them into meaningful chunks, creates embeddings, and stores those embeddings for similarity search. When a user asks a question, the system retrieves relevant chunks and uses them to produce a grounded answer with source information. Questions outside the campus-life corpus are rejected when their retrieval distance is above the relevance cutoff.


## Chunking Strategy

**Chunk size:** Up to 500 characters when combining consecutive paragraphs
**Overlap:** None

The original starter used a fixed-size fallback splitter with an 800-character chunk size and 120-character overlap. The starter baseline produced 26 chunks from the `advice_threads` corpus.

For my `campus_life` corpus, I found that the documents were short and naturally organized into paragraphs. The corpus contained 88 documents and 271 paragraphs. The longest document was 549 characters and the longest paragraph was 373 characters. Because of this, fixed-size windows were unnecessary and could split related information awkwardly.

I changed the chunker to group consecutive paragraphs together when the combined text stays within 500 characters. This keeps related information together while avoiding unnecessary splitting in the middle of a paragraph. The final custom chunker produced 90 chunks.



## Sample Chunks

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documents`

```text
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160_exams.txt#0` — produced by: `chunker.py::split_documents`

```text
BIOL 160 Cell Biology — assessment

Four unit tests and a cumulative final. Not curved.

The unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_math_220_exams.txt#0` — produced by: `chunker.py::split_documents`

```text
MATH 220 Linear Algebra — assessment

Two midterms and a cumulative final. Curved to a b- median.

The problem sets are the course; the lectures make sense afterwards rather than during.
```

**Chunk 4** — source: `dining_the_ridgeway_cafe.txt#0` — produced by: `chunker.py::split_documents`

```text
The Ridgeway Café

Second-year here. Wait times: 10 to 15 minutes at 12:30, none after 2:00. The thing worth going for is the only place on campus with real espresso. The thing to know is that seating is tight; about 40 seats for a building of 900.

Hours are 7:00am to 4:00pm weekdays only. Costs declining balance only, no meal swipes.
```

**Chunk 5** — source: `housing_morrow_house.txt#0` — produced by: `chunker.py::split_documents`

```text
Morrow House — what it's actually like

Just finished a year in this building. Built 1954, partially renovated 2008. Rooms are singles and doubles, hall bathrooms.

The good: cheapest housing tier by about $900 a year, and the singles are real singles.

The bad: known damp problem on the ground floor; two rooms were taken offline in 2024.

Laundry costs $1.50 wash, $1.25 dry, coin or card. On noise: loud until about 1am on weekends, no enforced quiet hours.
```

Starter baseline: 26 chunks total
Source: `thread_bike_commute.txt#0`
Produced by: `chunker.py::fallback_split`

Custom `campus_life` chunking: 90 chunks total
Strategy: paragraph-aware grouping with a 500-character context limit
Produced by: `chunker.py::split_documents`

## Sample Answer

**Question:** How much time per week should students expect to spend on CS 210?

**Answer:** Students should expect to spend about 8–10 hours per week outside of class on CS 210. The course materials also note that this workload includes reading, problem sets, and other coursework.

**Source:** `course_cs_210_workload.txt` and `course_cs_210.txt`

**My relevance cutoff:** `0.6`

I kept the starter relevance cutoff of 0.6. My evaluation showed a clear separation between questions covered by the `campus_life` corpus and questions outside it. The five in-corpus questions had best distances between 0.158 and 0.300, while the five out-of-corpus questions had distances between 0.825 and 0.934. This means the cutoff of 0.6 falls between the two groups.

| Question                                                          | In corpus? | Best distance |
| ----------------------------------------------------------------- | ---------- | ------------: |
| How does the housing lottery work for rising sophomores?          | Yes        |         0.158 |
| How much time per week should students expect to spend on CS 210? | Yes        |         0.258 |
| What are the main assessments in CS 210?                          | Yes        |         0.300 |
| What are the laundry costs at Innisfree Hall?                     | Yes        |         0.224 |
| What advice is given about the CS 340 term project?               | Yes        |         0.278 |
| What is the capital of Mongolia?                                  | No         |         0.825 |
| What type of oil should I use in a diesel engine?                 | No         |         0.934 |
| Who won the 1994 World Cup?                                       | No         |         0.886 |
| What is the correct ibuprofen dosage?                             | No         |         0.844 |
| How do I write a for loop in Rust?                                | No         |         0.896 |



## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.**

**2.**

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

| Criterion                                    | Target |  Run 1 |  Run 2 |  Run 3 | Verdict |
| -------------------------------------------- | ------ | -----: | -----: | -----: | ------- |
| 1. Retrieved chunk contains the answer       | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET     |
| 2. Every answer names a source               | 5 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET     |
| 3. Gate stops out-of-corpus questions        | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET     |
| 4. Complete, understandable chunks           | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET     |
| 5. Final answer identifies supporting source | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET     |


<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion                                 | Verdict | How I decided                                                                                                             |
| - | ----------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------- |
| 1 | Retrieved chunks contain the answer       | MET     | All five test questions had a retrieved chunk containing the information needed to answer the question in all three runs. |
| 2 | Every answer names a source               | MET     | Every generated answer identified at least one specific source document in all three runs.                                |
| 3 | Gate stops out-of-corpus questions        | MET     | The relevance gate refused all five out-of-corpus questions, exceeding the target of 4 of 5.                              |
| 4 | Complete, understandable chunks           | MET     | The five sampled chunks were understandable on their own and did not cut sentences or separate related paragraphs.        |
| 5 | Final answer identifies supporting source | MET     | All five test answers identified one or more specific source documents supporting the answer in all three runs.           |


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

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. Complete, understandable chunks | 4 of 5 |  |  |  |  |
| 5. Final answer identifies supporting source | 4 of 5 |  |  |  |  |


**Did it help?**

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
