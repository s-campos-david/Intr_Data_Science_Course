---
layout: post
title: "The riskiest thing about a maternity ward might be its address"
date: 2026-11-16
---

![Lowest and highest eight states by share of hospitals labelled Bottom Performer]({{ "/assets/images/bottom_rate_by_state.png" | relative_url }})

Every year the US government publishes a report card on childbirth safety. For every
Medicare-certified hospital in the country, Medicare measures how often mothers suffer severe
complications during delivery, adjusts the number for how sick the patients were to begin with, and
sorts hospitals into three groups: **Top Performer**, **Middle Performer** and **Bottom Performer**.

It is all public, so I downloaded it and spent a weekend asking it some questions.

The file covers 4,655 hospitals. About half deliver too few babies to be scored, which leaves 2,360
with an actual grade. One in four of those is a Bottom Performer.

Three things I wanted to know:

1. **What does a hospital with poor maternal outcomes look like?** The big ones, the small ones, the
   ones doing a lot of C-sections?
2. **Does a hospital's own safety paperwork mean anything?** Hospitals can formally attest that they
   have adopted a maternal safety programme. Does it show up in the results?
3. **Could you predict which hospitals land in the bottom group** without looking at the
   complication numbers themselves?

## 1. The biggest factor is the state

I expected the answer to be about the hospital. It is mostly about the map.

In Kansas and Ohio, around 8% of scored hospitals are Bottom Performers. In New York it is 51%. That
is the chart at the top, and it is not a small gap: a hospital in New York is more than six times as
likely to carry the worst label as one in Ohio.

Nothing else in the data comes close. Whatever is going on works at the level of states, not
individual buildings.

I want to be careful here, because two very different stories would produce the same chart. One is
that care genuinely differs from state to state. The other is that states differ in how thoroughly
they record complications, and a state that documents carefully looks worse than one that does not.
This data cannot tell them apart. Anyone using this chart as proof of bad hospitals is going further
than the numbers allow.

## 2. More C-sections, more complications

![Share of hospitals that are Bottom Performers, by cesarean rate and by delivery volume]({{ "/assets/images/bottom_rate_by_group.png" | relative_url }})

Split hospitals into five equal groups by how often they deliver by C-section and the share of
Bottom Performers climbs from **21.5%** in the lowest group to **32.5%** in the highest. A C-section
is major abdominal surgery, so the direction is not surprising, but it is useful to see it hold
across 2,360 hospitals.

The right-hand chart surprised me. Size does not work the way I assumed. The safest group is not the
small hospitals, it is the mid-sized ones at around 600 to 1,200 deliveries a year, at **20.8%**. The
largest hospitals are the worst group at **31.1%**.

That is almost certainly not because big hospitals are careless. It is because they are where the
difficult pregnancies end up. When a smaller hospital has a case it cannot handle, it transfers the
mother to the big regional centre, and the complications follow the patient.

The same logic produces the starkest number I found. Hospitals with a university in the name are
labelled Bottom Performer **45%** of the time, against **24%** for everyone else. Teaching hospitals
take the hardest cases and their report card reflects it.

## 3. The safety paperwork predicts nothing

This was the finding I did not expect.

Hospitals can formally attest to Medicare that they have adopted a maternal safety programme. It
sounds like exactly the sort of commitment that should show up in outcomes.

It does not. Among hospitals that said yes, **25.1%** are Bottom Performers. Among those that did
not, **24.7%**. Effectively identical.

So ticking that box tells you nothing about how often mothers are harmed there. Not that the
programme does not work, but that saying you have one and actually having a safe delivery ward are,
in this data, unrelated. If you were using that attestation as a quality signal, stop.

## 4. So can you predict it?

Partly, and the honest answer is more interesting than yes or no.

I built a model that tries to spot Bottom Performers using only things you could know without the
complication numbers: the state, the number of deliveries, the cesarean rate, and whether the name
suggests a teaching hospital.

It catches about **half** of the real Bottom Performers. When it raises a flag it is right about
**a third** of the time. Asked to pick which of two hospitals is the worse one, it gets it right
**62%** of the time, against 50% for a coin flip.

Better than guessing, and nowhere near good enough to accuse anyone of anything.

I also tried a more complicated model, a random forest, hoping it would do better. It came out
0.005 ahead on my first test, which looked like a win until I re-ran the comparison with the data
shuffled five different ways. Both models moved around by four times that much just from the
reshuffling. The "win" was noise, so I kept the simpler model.

To see why a weak model is still useful, imagine a state health department planning next year's
inspections. It can visit a few hospitals, and none of the candidates has a published grade:

![Model ranking scores for three hospitals with no published grade]({{ "/assets/images/scenario.png" | relative_url }})

- **A** - large university hospital in New York, 3,000 deliveries, 31% C-section rate
- **B** - community hospital in Ohio, 900 deliveries, 22% C-section rate
- **C** - small hospital in Texas, 250 deliveries, 26% C-section rate

A comes out far ahead and B far behind. Every signal the model has points the same way for both: A is
a teaching hospital, in the state with the worst record, with a high cesarean rate. B is the
opposite on all three.

C lands almost exactly on the line, which is the honest answer for that hospital. The model has
nothing strong to say about a small Texas hospital with an average cesarean rate, and a score in the
middle is it admitting so.

That is the whole value of a weak model. It cannot tell you who is unsafe. It can tell you that A is
a better use of a visit than B.

## What I take away

If you are expecting a baby and want to use this data, the short version: ask your hospital its
cesarean rate for first-time, low-risk births, and how many deliveries it does a year. Those two
questions get you most of the signal that exists in the public numbers.

And if your hospital is a large teaching centre with a poor-looking grade, that probably reflects the
patients it accepts rather than the care it gives. The grade punishes hospitals for taking hard
cases.

The thing I keep coming back to is how thin this data is. It knows a hospital's state, size,
cesarean rate and name. It knows nothing about staffing levels, nurse-to-patient ratios, who the
patients are, or what the surrounding community looks like. Those are the things that actually
determine whether a delivery goes well, and none of them are in the file.

A model can only find what is in front of it. Here, what is in front of it is mostly a map.

---

*Data: Centers for Medicare & Medicaid Services, Maternal Health - Hospital dataset, January to
December 2025, from the [Provider Data Catalog](https://data.cms.gov/provider-data/). CMS does not
endorse this analysis.*

*The full analysis, with the cleaning steps, the model comparison and every caveat spelled out, is in
the notebook: [github.com/s-campos-david/Intr_Data_Science_Course](https://github.com/s-campos-david/Intr_Data_Science_Course).
That repo also documents where I used an AI assistant on this project and where I did not.*
