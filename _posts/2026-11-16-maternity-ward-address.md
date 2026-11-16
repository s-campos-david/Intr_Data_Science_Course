---
layout: post
title: "The riskiest thing about a maternity ward might be its address"
date: 2026-11-16
---

![Best and worst eight states by share of hospitals labelled Bottom Performer]({{ "/assets/images/fig5_states.png" | relative_url }})

Every year the US government publishes a report card on childbirth safety. For every
Medicare-certified hospital in the country, Medicare measures how often mothers suffer severe
complications during delivery, adjusts the number for how sick the patients were to begin with, and
then sorts hospitals into three groups: **Top Performer**, **Middle Performer** and **Bottom
Performer**.

It is all public. Anyone can download it. So I did, and I spent a weekend asking it some questions.

The file covers 4,655 hospitals. About half of them deliver too few babies to be scored at all, which
leaves 2,360 hospitals with an actual grade. One in four of those is a Bottom Performer.

Here is what I wanted to know:

1. **What does a hospital with poor maternal outcomes actually look like?** Is it the big ones, the
   small ones, the ones doing a lot of C-sections?
2. **Does a hospital's own safety paperwork mean anything?** Hospitals can formally attest that they
   have adopted a maternal safety programme. Does that show up in the results?
3. **Could you predict which hospitals will land in the bottom group** without looking at the
   complication numbers themselves?

## 1. The single biggest factor is the state

I expected the answer to be about the hospital. It is mostly about the map.

In Ohio and Kansas, around 8% of scored hospitals are Bottom Performers. In New York, it is 51%. That
is the chart at the top of this post, and it is not a small gap: a hospital in New York is more than
six times as likely to carry the worst label as a hospital in Ohio.

Nothing else in the dataset comes close to that spread. Whatever is going on here, it operates at the
level of states, not individual buildings.

I want to be careful with this one, because there are at least two very different stories that would
produce the same chart. One is that care genuinely differs from state to state. The other is that
states differ in how thoroughly they record complications in the first place, and a state that
documents carefully will look worse than one that does not. This data cannot tell those two apart,
and anyone waving the chart around as proof of bad hospitals is going further than the numbers allow.

## 2. More C-sections, more complications

The second clearest pattern is the cesarean rate.

![Share of hospitals that are Bottom Performers, split by cesarean rate and by delivery volume]({{ "/assets/images/fig4_rate_by_bin.png" | relative_url }})

Split hospitals into five equal groups by how often they deliver by C-section, and the share of
Bottom Performers climbs steadily from **21.5%** in the lowest group to **32.5%** in the highest. A
C-section is major abdominal surgery, so the direction is not surprising, but it is useful to see how
consistently it shows up across 2,360 hospitals.

The right-hand chart is the one that surprised me. Size does not work the way I assumed. The safest
group is not the small hospitals, it is the mid-sized ones, around 600 to 1,200 deliveries a year, at
20.8%. The very largest hospitals are the worst group, at 31.1%.

That is almost certainly not because big hospitals are careless. It is because they are where the
difficult pregnancies end up. When a smaller hospital has a case it cannot handle, it transfers the
mother to the big regional centre. The complications follow the patient.

The same logic shows up in the starkest number I found. Hospitals with a university in the name are
labelled Bottom Performer **45%** of the time, against **24%** for everyone else. Teaching hospitals
take the hardest cases. Their report card reflects that.

## 3. The safety paperwork predicts nothing at all

This was the finding I did not expect.

Hospitals can formally attest to Medicare that they have adopted a maternal safety programme. It
sounds like exactly the sort of commitment that should show up in outcomes.

It does not. Among hospitals that said yes, **25.1%** are Bottom Performers. Among those that did
not, **24.7%**. The two numbers are effectively identical.

So a hospital ticking that box tells you nothing whatsoever about how often mothers are harmed there.
Not that the programme does not work, but that saying you have one and actually having a safe
delivery ward are, in this data, completely unrelated. If you were using that attestation as a
quality signal, you should stop.

## 4. So can you predict it?

Partly, and the honest answer is more interesting than a yes or a no.

I built a model that tries to spot Bottom Performers using only things you could know without the
complication numbers: the state, the number of deliveries, the cesarean rate, and whether the name
suggests a teaching hospital.

It catches **56% of the real Bottom Performers**. When it raises a flag, it is right about **35%** of
the time. Asked to choose which of two hospitals is the worse one, it picks correctly about **63%**
of the time, against 50% for a coin flip.

That is genuinely better than guessing and nowhere near good enough to accuse anyone of anything.

To see why it is still useful, imagine a state health department planning next year's inspections. It
can visit three hospitals, and none of the candidates has a published grade yet:

![Model risk scores for three hospitals with no published grade]({{ "/assets/images/fig10_scenario.png" | relative_url }})

- **Hospital A** - large university hospital in New York, 3,000 deliveries, 31% C-section rate
- **Hospital B** - community hospital in Ohio, 900 deliveries, 22% C-section rate
- **Hospital C** - small faith-based hospital in Texas, 250 deliveries, 26% C-section rate

The model puts A at the top by a wide margin and B at the bottom. Every signal it has points the same
way: A is a teaching hospital, in the state with the worst record, with a cesarean rate near the top
of the range. B is the opposite on all four counts.

That is the whole value of a weak model. It cannot tell you who is unsafe. It can tell you where to
look first when you cannot look everywhere, and that A is a better use of a visit than B.

## What I take away from this

If you are expecting a baby and you want to use this data, the useful version is short. Ask your
hospital what its cesarean rate is for first-time, low-risk births, and ask how many deliveries it
does a year. Those two questions get you most of the signal that exists in the public numbers.

And if your hospital is a large teaching centre with a poor-looking grade, that is probably a
reflection of the patients it accepts rather than the care it gives. The grade punishes hospitals for
taking hard cases.

The bigger thing I keep coming back to is how thin this data is. It knows a hospital's state, size,
cesarean rate and name. It knows nothing about staffing levels, nurse-to-patient ratios, who the
patients are, or what the surrounding community looks like. Those are the things that actually
determine whether a delivery goes well, and none of them are in the file.

A model can only find what is in front of it. In this case, what is in front of it is mostly a map.

---

*Data: Centers for Medicare & Medicaid Services, Maternal Health - Hospital dataset, reporting period
January to December 2025, from the [Provider Data Catalog](https://data.cms.gov/provider-data/). CMS
does not endorse this analysis.*

*The full analysis, with the cleaning steps, the model comparison and every caveat spelled out, is in
the notebook here: [github.com/s-campos-david/Intr_Data_Science_Course](https://github.com/s-campos-david/Intr_Data_Science_Course).*
