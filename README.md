# The Unofficial Guide

**Abdulrasheed Abdulmajeed â€” Corpus: campus_life**

> **This file is your submission.** Fill it in as you go â€” most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders â€” you can leave them
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

**Chunk 1** â€” source: `admin_add_drop_deadline.txt#0` â€” produced by: `chunker.py::split_documents`

```text
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window â€” through the end of week six â€” but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** â€” source: `course_biol_160_exams.txt#0` â€” produced by: `chunker.py::split_documents`

```text
BIOL 160 Cell Biology â€” assessment

Four unit tests and a cumulative final. Not curved.

The unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** â€” source: `course_math_220_exams.txt#0` â€” produced by: `chunker.py::split_documents`

```text
MATH 220 Linear Algebra â€” assessment

Two midterms and a cumulative final. Curved to a b- median.

The problem sets are the course; the lectures make sense afterwards rather than during.
```

**Chunk 4** â€” source: `dining_the_ridgeway_cafe.txt#0` â€” produced by: `chunker.py::split_documents`

```text
The Ridgeway CafÃ©

Second-year here. Wait times: 10 to 15 minutes at 12:30, none after 2:00. The thing worth going for is the only place on campus with real espresso. The thing to know is that seating is tight; about 40 seats for a building of 900.

Hours are 7:00am to 4:00pm weekdays only. Costs declining balance only, no meal swipes.
```

**Chunk 5** â€” source: `housing_morrow_house.txt#0` â€” produced by: `chunker.py::split_documents`

```text
Morrow House â€” what it's actually like

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

**Answer:** Students should expect to spend about 8â€“10 hours per week outside of class on CS 210. The course materials also note that this workload includes reading, problem sets, and other coursework.

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

**1.** I asked ChatGPT to help me choose and implement a chunking strategy for my `campus_life` corpus. The initial paragraph-only approach produced 183 chunks and could separate related paragraphs, including information about the CS 340 term project. I inspected the corpus and changed the implementation myself so consecutive paragraphs are grouped when they fit within a 500-character limit, producing 90 chunks.

**2.** I asked ChatGPT to help me diagnose an inconsistency in the generated source attribution after the Before evaluation. One housing-lottery answer put the source in parentheses while other answers used a separate source line. I changed `generate.py` myself so the grounding instruction requires the supporting filename on a separate line in the format `Source: filename.txt`, then ran the formal After evaluation to verify the change.
<!-- â”€â”€ Stretch features â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 â€” the point is that someone can see what you said before you knew
     how it went. -->

## Run Log â€” Before

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
     runs â€” the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit â€” not a new one. Plus a sentence on how you decided. That sentence
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

### Before Evidence

Produced by `run_eval.py::main`, using retrieval from `store.py::search` and chunks from `chunker.py::split_documents`. The full run is recorded in `results/run_2026-09-22_1340_before.md`.
**Criterion 1 — retrieved chunks contain the answer**

Actual output from the housing-lottery question:

```text
For rising sophomores, the housing lottery involves getting a number drawn at random (admin_housing_lottery.txt).
```

**Criterion 2 — every answer names a source**

Actual output from the CS 210 assessments question:

```text
The main assessments in CS 210 are two midterms and a final.

Sources: `course_cs_210_exams.txt` and `course_cs_210.txt`
```

**Criterion 3 — gate stops out-of-corpus questions**

Actual output from `run_eval.py::check_out_of_scope`:

```text
What is the capital of Mongolia? | 0.825 | refused
How do I change the oil in a diesel engine? | 0.934 | refused
Who won the 1994 World Cup? | 0.886 | refused
What is the recommended dosage of ibuprofen for a headache? | 0.844 | refused
How do I write a for loop in Rust? | 0.896 | refused
```

The gate refused 5 of 5 out-of-corpus questions.

**Criterion 4 — complete, understandable chunks**

One sampled chunk from the `campus_life` corpus was:

```text
CS 340 Databases - assessment

One midterm and a final, both open-book. Lightly curved, usually two or three points.

Start the term project in week three, not week eight; everyone learns this the hard way.
```

This chunk was produced by `chunker.py::split_documents`. The five sampled chunks were manually inspected and each formed an understandable piece of information without cutting a sentence or separating related paragraphs.

**Criterion 5 — final answer identifies the supporting source**

Actual output from the CS 340 term-project question:

```text
The advice given is to start the term project in week three, rather than week eight.

Sources: `course_cs_340_exams.txt`, `course_cs_340.txt`
```

The complete raw output is recorded in `results/run_2026-09-22_1340_before.md`.

## Diagnoses

No acceptance criterion was missed in the Before runs. All five criteria met their targets across all three runs. The main quality issue I found was consistency in source attribution during generation: the housing-lottery answer in one Before run put the source in parentheses instead of using the same dedicated `Source:` format used by other answers. This came from the generation stage, where the grounding instruction required the model to name the source but did not require a specific output format.

## The Improvement

**What I changed:** I updated `generate.py` so the grounding instruction requires the supporting filename to appear on a separate line in the exact format `Source: filename.txt` and tells the model not to put the source only in parentheses.

**Why I picked it:** The Before run already met the source-related acceptance criteria, but its source formatting was inconsistent. I chose a small generation-stage improvement that makes the source attribution easier to identify and verify without changing retrieval or chunking.

### Run Log - After

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 2. Every answer names a source | 5 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 4. Complete, understandable chunks | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 5. Final answer identifies supporting source | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |

**Did it help?**

Yes, for the specific consistency issue I targeted. In the After run, all 15 generated answers used a separate `Source: filename.txt` line, including all three housing-lottery answers. The acceptance-criterion scores did not increase because they were already 5 of 5 before the change. Retrieval distances and the out-of-corpus gate also remained unchanged, which is expected because the change affected generation instructions rather than retrieval or gating.

## What's Still Broken

No acceptance criterion is still missed. One remaining quality issue is that the housing-lottery answer can still be awkwardly worded, for example saying that the lottery is "not random" while explaining that a number is drawn at random. I would address that with a clearer generation instruction or by improving the underlying source wording, but I did not change it in this unit because the answer still contained the required information and the goal of this experiment was source-attribution consistency.

## What I'd Do Differently

I would tighten Criterion 3 after seeing the evaluation results. The current target is 4 of 5, but the measured best distances showed a wide separation: the in-corpus questions ranged from 0.158 to 0.300, while the out-of-corpus questions ranged from 0.825 to 0.934. Based on this evidence, I would consider a stricter gate target such as 5 of 5 and test whether a higher relevance cutoff can reject unsupported questions without rejecting the in-corpus questions.