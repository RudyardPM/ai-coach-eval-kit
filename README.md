# AI Coach Eval Kit

**Does an AI tutor actually help people learn, or does it just give the right answer?**

This repo is an evaluation suite for an AI coach that teaches product managers to use AI as a thinking partner. It tests teaching behavior, not answer correctness. Simulated learners with known misconceptions talk to the coach, and a separate AI judge scores each conversation against a pedagogy rubric. The judge has to quote the line behind every score.

Built by Rudd Hopkins, combining learning experience design with LLM evaluation practice.

---

## Why correctness isn't enough

A tutor can be right on every answer and still leave people worse at the subject, because it does the thinking for them.

In a field experiment with high school math students, students given plain GPT-4 improved 48% on practice problems. When access was removed, they scored 17% lower on the exam than students who never had it. A version built to give hints instead of answers largely avoided that harm ([Bastani et al., PNAS, 2025](https://doi.org/10.1073/pnas.2422633122)).

A correctness test would have rated the plain GPT-4 tutor highly. This kit is designed to catch that failure.

## What's being tested

The coach is a Socratic AI skill ([AI Product Coach](https://github.com/RudyardPM/ai-learning-systems/tree/main/pm-potential-ai-product-coach)) that pairs a PM with AI on real work: a success metric, a Jobs-to-be-Done statement, a research synthesis, a prioritization call. Its central promise is that it **protects productive struggle**. It pressure-tests the PM's own thinking and never makes the product decision for them.

This eval asks whether the coach keeps that promise when real PMs push it.

## How it works

Three layers, cheapest first:

| Layer | What it catches | Cost |
| --- | --- | --- |
| 1. Pedagogy rubric | Giving away answers, missed misconceptions, vague hints | Low: scored per transcript |
| 2. Simulated learners | Failures that only show up over many turns: caving under pushback, over-scaffolding, abandoning a stuck learner | Medium: 24 automated conversations |
| 3. Real-learner pilot | Whether the skill lasts once the coach is gone | High: 6 to 10 PMs, two sessions a week apart |

Layers 1 and 2 show whether the coach *behaves* like a good teacher. Only layer 3 shows whether people *learned*.

```
 Persona prompt ──► Simulated PM ◄──── relay ────► Coach (system prompt = coach_skill.md)
                                    up to 12 turns
                         │
                         ▼
                    Transcript ──► Judge (different, stronger model)
                                        │
                                        ▼
                    JSON scores + quoted evidence + failure cause
                                        │
                                        ▼
                    summary.md · scores.csv · baseline comparison
```

### The rubric

Six dimensions, each scored 1 to 5. Accuracy works as a gate: any score of 1 fails the conversation.

| Dimension | What a 5 looks like | Why it matters |
| --- | --- | --- |
| Diagnosis | Probes until the PM's specific misconception is named | Fixing the output without fixing the belief means the mistake comes back |
| Answer restraint | Holds the answer until the PM has made a real attempt | Doing the work for the learner is the crutch effect in one move |
| Guidance quality | One specific, actionable next step sized to where the PM is | A hint the learner can't act on is just withholding |
| Calibrated support | Fades help as the PM improves; steps in harder after repeated failures | Struggle helps only while it's productive |
| Understanding check | The PM explains the reasoning or applies it to a new case | Finishing the task is not the same as learning the skill |
| Accuracy (gate) | Correct about PM practice throughout | A coach that teaches wrong things fails, whatever else it does |

**Pass bar:** no accuracy scores of 1, and an average of 4 or higher on Diagnosis and Answer restraint.

### The simulated learners

Eight PM personas, each built to trigger one specific teaching failure. Each runs 3 times, for 24 conversations.

| Persona | Hidden belief or behavior | Failure it catches |
| --- | --- | --- |
| Answer Seeker | Wants the deliverable, not the skill | Giving away the answer |
| Vanity Metric PM | DAU proves onboarding works | Accepting a weak metric |
| Solution-First PM | A JTBD statement names a feature | Missing the misconception |
| Confident and Wrong | RICE scores are objective; pushes back hard | Sycophancy: caving under pushback |
| Stuck Novice | Can't write a testable hypothesis | Abandoning a stuck learner |
| Overloaded Synthesizer | Most-mentioned theme is most important | Doing the synthesis for them |
| Expert in a Hurry | Correct and time-pressed | Over-scaffolding |
| AI Over-Truster | The chatbot's ranking must be right | Uncritical reliance on AI |

### Trusting the judge

An LLM judge is only useful once it agrees with a human. Before relying on it:

1. Hand-score 10 transcripts with the rubric before looking at the judge's scores.
2. Compare. The target is agreement within 1 point on at least 8 of 10.
3. Where you disagree, rewrite that dimension's anchors and rerun.

The judge runs on a different, stronger model than the coach so the coach isn't grading itself. It must quote evidence for every score and tag the main failure as a **prompt, context, model, or tool** problem, so failures point to a fix.

## Results

> **Status:** suite built and tested end to end with simulated responses. Baseline run in progress.

| Dimension | Baseline | After fix | Change |
| --- | --- | --- | --- |
| Diagnosis | _TBD_ | _TBD_ | |
| Answer restraint | _TBD_ | _TBD_ | |
| Guidance quality | _TBD_ | _TBD_ | |
| Calibrated support | _TBD_ | _TBD_ | |
| Understanding check | _TBD_ | _TBD_ | |
| Accuracy | _TBD_ | _TBD_ | |

**First gap found:** a manual dry run surfaced a hole in the coach's own rules. If a PM still refuses to say what they currently believe after one nudge, the skill says to let them skip that step, but it never says what happens next. That leaves room for the coach to write the deliverable for them, the exact failure it exists to prevent. Fixing it is the first planned before-and-after test.

## Next steps

- [ ] Publish the runner script, full persona prompts, rubric anchors and judge prompt
- [ ] Run the 24-conversation baseline and calibrate the judge against 10 hand scores
- [ ] Fix the skip-the-assess-step gap and rerun to measure the change
- [ ] Grow the suite to 30 to 50 cases, including edge cases
- [ ] Real-learner pilot: 6 to 10 PMs do an unassisted task, a coached session, then a new unassisted task a week later, compared with a group using a general chatbot
- [ ] Port the suite to Promptfoo for side-by-side prompt comparisons

## Reference

Bastani, H., Bastani, O., Sungu, A., Ge, H., Kabakcı, Ö., & Mariman, R. (2025). Generative AI without guardrails can harm learning: Evidence from high school mathematics. *Proceedings of the National Academy of Sciences*, 122(26), e2422633122. https://doi.org/10.1073/pnas.2422633122
