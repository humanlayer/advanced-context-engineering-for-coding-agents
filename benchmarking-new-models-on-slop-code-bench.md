# SlopCodeBench update 10/2026 - Opus 5.5 update, Astra improves by 1.5% over gpt-5.5, xhigh considered harmful

> **NOTE:** This is Part I of our October 2026 writeup: the Q4 state of the slop. Gabe and the [SlopCodeBench team](https://arxiv.org/abs/2603.24755) will share their analysis and a more qualitative deep dive into slop patterns in Part II.

> For my previous writeups on SlopcodeBench, see [benchmarking-opus-5-on-slop-code-bench.md](./benchmarking-opus-5-on-slop-code-bench.md) and [benchmarking-sol-fable-kimi-on-slop-code-bench.md](./benchmarking-sol-fable-kimi-on-slop-code-bench.md). Both include deeper dives on the core structure of SCB and how each of its challenges is broken into checkpoints.

In May, the last time SCB was published, gpt-5.5 got a **14.8% strict pass rate on slopcodebench**. This month, I ran the frontier models through the same campaigns, and the highest scorer was gpt-6 astra, hitting **16.3%**. 

While the benchmark reports several different pass rates, the one we focus on is **strict pass rate** - in how many checkpoints did the model pass every test case while still passing all regression tests the frontier models we tested here had strict pass rates in the **12.2-16.3%** range:

- Opus 5.5
- Fable 5.1
- SOL 6 and 6.1
- Astra
- GLM 5.3

For comparison, the benchmark also reports a "core pass rate" which is less strict, which lands in the **65.8-74.0%** pass rate for these models.

One interesting thing here that I'll dig into - medium effort performed better than XHigh for some newer models. XHigh costs **2.11-4.22x** as much as Medium in the three complete pairs, and most notably, **opus 5.5 did *BETTER* on medium effort than on xhigh**

Over the past few months, I ran subsets of the bench. I [started with three problems](./benchmarking-opus-5-on-slop-code-bench.md), then [tried six](./benchmarking-sol-fable-kimi-on-slop-code-bench.md). This time: **36 problems and 196 checkpoints per setting**. Since we used a subset before, we can't use the earlier subsets to measure improvement or decline. The full benchmark lets us compare these results with the official research findings. It also means while the 30% fable 5 score from the latter of those two is interesting, its not comparable here, because it only covers 6 out of 36 problems.

## quick SCB background

[SlopCodeBench](https://arxiv.org/abs/2603.24755) gives a coding agent a problem in stages. It writes a program, gets new requirements, and changes the existing code. Later checkpoints test both new behavior and old behavior.

![Diagram from the SlopCodeBench paper: new requirements arrive in stages, and code quality can degrade as the agent changes its solution.](./images/scb-paper-degradation.png)

The idea here is that this maps more onto how software is really built - with unclear and evolving requirements during the course of the build.

For folks who want to try to repro these findings: I used the pinned v1.0 catalog, the stock `just-solve` prompt, seed 42, the `any-case` runner pass policy, and Docker with Python 3.12 and uv. 

I ran each model and effort setting once. Each checkpoint builds on the code from earlier checkpoints. Some problems have more checkpoints than others. Each checkpoint counts equally toward the pass rate, so problems with more checkpoints have more weight.

## Core vs. Strict pass rates

- **Strict:** every test category passes, including Regression. 
- **Core:** all tests in a nonempty Core category pass. For example, if a model fails checkpoint 2, it can still get a core pass for a later checkpoint, even if the ck2 tests remain failing.

![Stacked checkpoint pass-rate bars for ten complete campaigns. Solid segments show strict passes and tinted extensions show Core-only passes. Astra XHigh leads strict success at 32/196; Opus Medium leads Core success at 145/196.](./images/scb-3-pass-rates.png)

*Strict passes: 24-32 of 196. Core passes, including strict: 129-145. The tinted portion isn't extra strict success.*

| Complete campaign | Strict passes | Strict rate | Core rate | Estimated cost |
| --- | ---: | ---: | ---: | ---: |
| GPT-6 Astra XHigh | 32/196 | 16.33% | 69.39% | $758.62 |
| GPT-6 SOL Medium | 30/196 | 15.31% | 67.86% | $245.69 |
| GPT-6 Astra Medium | 29/196 | 14.80% | 66.33% | $261.05 |
| GPT-6.1 SOL Medium | 29/196 | 14.80% | 65.82% | $178.51 |
| Opus 5.5 Medium | 28/196 | 14.29% | 73.98% | $193.47 |
| GPT-5.6 SOL XHigh | 28/196 | 14.29% | 70.92% | $753.30 |
| Fable 5.1 XHigh | 26/196 | 13.27% | 72.45% | $1,550.07 |
| GPT-6 SOL XHigh | 26/196 | 13.27% | 68.37% | $517.32 |
| Opus 5.5 XHigh | 25/196 | 12.76% | 71.94% | $817.04 |
| GLM 5.3 XHigh | 24/196 | 12.24% | 66.33% | $365.13 |

Astra XHigh has the most strict passes in this snapshot. But it's lead over SOL 6.1 Medium is only **two checkpoints**.

Opus Medium leads Core success at **145/196**, but passes only **28/196** strictly. 

## more effort == more spend, but not necessarily better outcomes

![Medium and XHigh cost and strict-pass comparisons: Opus costs 4.22x more with 28 to 25 strict passes, GPT-6 SOL costs 2.11x more with 30 to 26, and Astra costs 2.91x more with 29 to 32.](./images/scb-3-effort.png)

*XHigh costs more in all three pairs; strict success rises only for Astra. These single runs don't isolate effort as the cause.*

| Model | Strict: Medium → XHigh | Core: Medium → XHigh | Estimated cost multiplier |
| --- | ---: | ---: | ---: |
| Opus 5.5 | 28 → 25 | 145 → 141 | 4.22x |
| GPT-6 SOL | 30 → 26 | 133 → 134 | 2.11x |
| GPT-6 Astra | 29 → 32 | 130 → 136 | 2.91x |

This doesn't prove that more reasoning hurts coding. It says **higher effort was not a reliable way to buy more strict passes in these runs**. One campaign per setting can't tell me how much is run-to-run variation.

![Scatterplot of recorded estimated cost versus strict checkpoint pass rate. GPT-6.1 Medium records $178.51 and 29 strict passes, Fable $1,550.07 and 26, and Astra XHigh $758.62 and 32.](./images/scb-3-cost-strict.png)

*Recorded estimates span $178.51-$1,550.07 within a 12.24-16.33% strict-score range.*

GPT-6.1 Medium has the lowest recorded cost at $178.51, with the same strict count as Astra Medium. Fable's recorded estimate is about **8.68x** as much, with three fewer strict passes. 

## the final code still fails tests

Checkpoint success also isn't the same as ending with a working program.

![Final failed test cases across 36 problem snapshots per campaign, from 1,435 for Astra XHigh to 2,831 for GPT-6 SOL Medium. A separate column shows zero to three strictly passing final checkpoints per campaign.](./images/scb-3-final-failures.png)

*Only each problem's last checkpoint counts here. Failed test cases aren't distinct bugs; skips stay separate.*

| Complete campaign | Strict final checkpoints, out of 36 | Final failed test cases | Of those, Regression |
| --- | ---: | ---: | ---: |
| GPT-6 Astra XHigh | 3 | 1,435 | 1,178 |
| GPT-6 Astra Medium | 1 | 1,747 | 1,516 |
| GPT-6 SOL XHigh | 1 | 1,651 | 1,366 |
| GPT-6 SOL Medium | 1 | 2,831 | 2,479 |
| GPT-6.1 SOL Medium | 3 | 2,757 | 2,404 |
| GPT-5.6 SOL XHigh | 2 | 2,727 | 2,376 |
| Fable 5.1 XHigh | 1 | 2,463 | 2,126 |
| Opus 5.5 Medium | 0 | 2,508 | 2,206 |
| Opus 5.5 XHigh | 1 | 2,446 | 2,123 |
| GLM 5.3 XHigh | 1 | 2,748 | 2,311 |

## code quality findings - blurry

First - the models left a ton of junk and scratch code while working. My first reaction was to remove the random temp files from the quality scores. But I chatted with [gabe](https://x.com/gorlanski) and he made a good point: **the benchmark should measure how much garbage models leave in the codebase. If we're not reading the code, we need to measure it.** Tests, scratch code, and generated copies probably shouldn't get a free pass just because they aren't the main program.

I did use a fixed list of folder names to exclude installed packages, virtual environments, and caches from both analyses.

The main results analyze all leftover code. For comparison, we also calculate the same metrics after removing a fixed list of temporary and generated files. That lets us see how much the cleanup changes the numbers. The rules and the actual difference are below.

These figures cover **the same 28 problems for all ten completed runs**, not all 36 problems. The scanner couldn't fully measure some files in eight problems (more on that in gabe's update later), so we left those problems out for every model.

![Ten campaigns ranked from lowest to highest SLOC-weighted heuristic coverage across 28 matched problems. Opus XHigh is lowest at 77.75%; Opus Medium is highest at 94.25%, or 92.90% after filtering.](./images/scb-3-quality-slop-coverage.png)

Some high level details on how we compute "what % of this code triggers at least one slop rule":

- The scanner flags slop patterns and repeated code within each file. 
- "Coverage" is the share of source lines flagged by those checks. 
- We count each line only once, even if several checks flag it.
- A check can flag a whole function without every line being a problem. 

We total the flagged lines and source lines across all 28 problems, then divide to get the percentage. Larger programs therefore have more weight. **In the earlier posts, we averaged each problem's percentage instead.**

Opus XHigh has the lowest slop percentage at **77.75%**, versus **94.25%** for Opus Medium. Interestingly, once again, the xhigh version has fewer strict passes: 25 versus 28.

<h3><blockquote>while Opus 5.5 Medium had more strict passes, Opus 5.5. XHigh wrote code that was close to ~5x cleaner.</blockquote></h3>

It is possible that this is because newer models come with new / different slop patterns, and the paper's original battery of 200+ linters is focused on a set of hand-curated patterns derived from agent-authored PRs in the months preceding the original publication in march 2026 and the v1.1 in may 2026. These new models may produce new maintainability anti-patterns that we haven't built linters for yet (more on that in Gabe's follow up post).

![Total source lines across 28 matched final problems per campaign. Opus Medium has 216,881 SLOC, including 41,265 removed by the deterministic filter, leaving 175,616.](./images/scb-3-quality-volume.png)

*Opus Medium leaves the most code, even after filtering. We don't remove all tests.*

For the filtered score, we leave out folders named `_work`, `tmp`, `temp`, `scratch`, `generated`, `build`, or `dist`, plus `*_pb2.py` and `*_pb2_grpc.py` files. We use the same rules for every run. We don't guess whether code looks generated or exclude tests as a group. In these runs, the filter only removed `_work` files: **45 files and 41,265 source lines**, all from Opus Medium's final `dynamic_buffer` code. Its total drops from **216,881 to 175,616 source lines**, and slop-presence drops to **92.90%**. It still ranks most-frequent on slop presence. The filter changes nothing for the other models.

![Functions and methods alongside callable-weighted mean complexity. Fable has 8,967 callables and mean complexity 3.57; GPT-6 SOL Medium has 1,083 and mean complexity 11.70.](./images/scb-3-quality-callables.png)

*Fable has the lowest mean complexity, 3.57; GPT-6 SOL Medium has the highest, 11.70. Functions and methods each get one vote.*

As you might expect, splitting code into more helper functions can lower average complexity without making the code easier to change. Less code isn't always better either.

There's a ton more metrics and I'm excited to get Gabe's take on the more qualitative side of these outputs. 

## my takeaway

The full benchmark is hard in a way that matters to me: the agents get the main behavior right much more often than they keep **all** tested behavior working as requirements change.

We're getting better at maintaing a codebase over time (14.8% to 16.3% during may->september), but not as fast as anyone would have you believe. I think we'll be keeping the lights on for a while to come.

> **NOTE:** This is Part I of our October 2026 writeup: the Q4 state of the slop. Gabe and the [SlopCodeBench team](https://arxiv.org/abs/2603.24755) will share their analysis and a more qualitative deep dive into slop patterns in Part II.
