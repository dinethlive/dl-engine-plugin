---
name: bloom
description: Read a lesson, a past paper or a set of questions through the revised (2001) Bloom's taxonomy, and write questions or notes at a named cognitive level. Use when the teacher mentions Bloom, cognitive levels, higher order or lower order thinking, HOTS or LOTS, learning objectives, or asks what level a question or lesson sits at, what levels a paper tests, or for questions pitched at a particular level.
---

# The revised Bloom's taxonomy, over this corpus

Use the **2001 revision** (Anderson, Krathwohl et al.), not the 1956 original.
It has **two dimensions**, and most of the value is in the second one, which
almost everybody drops.

| | Remember | Understand | Apply | Analyze | Evaluate | Create |
| :--- | :-: | :-: | :-: | :-: | :-: | :-: |
| **Factual** | | | | | | |
| **Conceptual** | | | | | | |
| **Procedural** | | | | | | |
| **Metacognitive** | | | | | | |

Rows are what is known, columns are what is done with it, and an objective
lands in a **cell**, not on a level. The blank cells are the point: they are
where a unit is not teaching or not testing, and they only become visible once
both axes are drawn.

**Never draw a pyramid.** The triangle appears in neither the 1956 nor the 2001
book. Lorin Anderson, who led the revision, is explicit: *"The triangle does not
appear anywhere in either Taxonomy."* The processes are a toolbox rather than a
ladder, so it is normal and useful to apply in order to understand.

## The two axes

**Cognitive processes** (the verb), with their subprocesses:

- **Remember** — recognizing, recalling
- **Understand** — interpreting, exemplifying, classifying, summarizing, inferring, comparing, explaining
- **Apply** — executing, implementing
- **Analyze** — differentiating, organizing, attributing
- **Evaluate** — checking, critiquing
- **Create** — generating, planning, producing

**Knowledge types** (the noun), concrete to abstract:

- **Factual** — terminology, specific details, the elements of the discipline
- **Conceptual** — classifications, principles, generalizations, theories, models
- **Procedural** — skills, algorithms, techniques, and knowing when to use them
- **Metacognitive** — strategy, awareness of one's own thinking, self-knowledge

Metacognitive was the genuinely new row in 2001. In an A/L corpus it is almost
always empty, and saying so is often the single most useful observation.

## The trap, and it is the one everybody falls into

**A verb does not determine a level.** Classifying by looking a verb up in a
table is the documented error, not the method. "Explain" is `Understand` when a
student must put a mechanism in their own words, and it is `Remember` when the
lesson already states that explanation in a sentence they can reproduce.

So classify against **what the corpus actually contains**:

1. Read the lesson before judging anything derived from it.
2. Ask what the student has to do that the source does not do for them. If the
   answer is printed, the task is `Remember` however grand its verb.
3. Say which cell, meaning a process AND a knowledge type, never a bare level.

**"Higher" does not mean harder, or better.** A brutal `Remember` question and
a gentle `Create` task both exist. Lower levels are the foundation, not
disposable steps, and calling them inferior misreads the framework.

## Every Bloom label is inference

The corpus does not say what level anything is. A classification is your
judgment about a lesson or a question, so under
[the evidence contract](../evidence/SKILL.md) it is marked `Inference:` and the
thing being classified is cited normally:

> `Inference:` "state the function of the CPU" (`computer-architecture`:88) sits
> at **Remember / Factual**. The lesson gives the function in one sentence, so a
> student reproduces it rather than reconstructs it.

Never present a level as though the corpus asserted it. The citation proves what
the source says; the level is what you concluded.

## Job 1: map a lesson

The question behind this is "what is my lesson actually asking of them".

`corpus_read` the lesson, then place what it teaches into cells. Report:

- the cells the lesson occupies, with a quoted line for each
- **the empty cells**, and what a filled one would look like here
- whether the lesson stops at `Remember` and `Understand`, which is the common
  finding and the actionable one

If a lesson has `examRelevance` on its concept card (`corpus_index format=full`),
classify that too and say whether it sits above the lesson body. A lesson that
teaches at `Understand` while its exam relevance demands `Analyze` is a gap the
teacher can close.

## Job 2: profile a paper

The question behind this is "what does this examiner actually reward".

`paper_contents` for the shape, then `open_question` on the questions that
matter. **You cannot classify a question you have not looked at**, because a
question is page images and its `topic` and `keywords` are filing labels, not
content.

Report a distribution across cells, with each question's handle. Then the two
findings worth having:

- **What the paper tests that the corpus does not teach.** Cross-check with
  `corpus_search`; separate "I could not find it" from "it is not taught", per
  the evidence contract.
- **What the corpus teaches that the paper never asks.** Equally useful, and
  invisible without both libraries.

Across several years this becomes the real answer: whether the examiner is
drifting up the process axis, and which knowledge row they favour.

## Job 3: write questions or notes at a level

Ground every item in the corpus and cite the lesson it comes from. An item the
corpus cannot support is an item the student was never taught.

State the **target cell** before writing, then check the item actually lands
there. Ask what the student must do, not which verb you used.

Useful verbs, as a starting point and never as a classifier:

| Level | Verbs | A stem |
| :--- | :--- | :--- |
| Remember | define, list, name, identify, recall | "List the..." |
| Understand | explain, summarize, compare, classify, interpret | "Explain in your own words..." |
| Apply | solve, demonstrate, calculate, execute, implement | "How would you use... to..." |
| Analyze | differentiate, organize, attribute, distinguish | "How does X relate to Y?" |
| Evaluate | judge, justify, critique, defend, rank | "Which is better, and why?" |
| Create | design, construct, formulate, compose, produce | "Propose an alternative..." |

For a **learning objective**, use ABCD: Audience, Behavior (one Bloom verb),
Condition, Degree. One verb per objective, and the assessment must demand that
same verb. If an item's demand and its objective's verb disagree, one of them is
wrong; that check is the highest-leverage thing in the whole framework.

For **theory notes**, structure by what the student does with the material
rather than by heading order, and say which parts are recall and which need
working through. Keep the corpus's own vocabulary: `keyConcepts` on a concept
card are the terms already written down.

## When to reach for something else

- **Webb's DOK** measures the *depth* a task demands, not the type of thinking.
  A hard multi-step calculation is still DOK 1 if it is a recalled procedure.
  Use it to check a task truly demands what you intended.
- **SOLO** grades the structure of a student's actual answer, not the task. It
  is the better tool for marking.

Say which you are using. They answer different questions and are complements.

## Honesty about the framework

It is a shared language and a heuristic, not a validated theory of how people
learn. It predates cognitive science, its authors claim empirical support only
for roughly the lower four levels, and the strict hierarchy was deliberately
relaxed in 2001. Use it to expose imbalance and to align objectives with
assessment. Do not present a classification as a fact about a student's mind.
