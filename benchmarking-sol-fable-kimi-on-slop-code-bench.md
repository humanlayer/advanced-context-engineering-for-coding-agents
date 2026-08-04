# Benchmarking Fable, Sol, and Kimi K3 on SlopCodeBench

![title](./images/scb-2-title.png)

Last week when Opus 5 came out, I [ran some benchmarks](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/benchmarking-opus-5-on-slop-code-bench.md) against my favorite new coding agent eval - [SlopCodeBench](https://arxiv.org/abs/2603.24755).

The number one takeaway: this is a hard benchmark and even brand-new models didn't break 35% pass rate (that's 2024 SWE-Bench territory, the sonnet 3.5 days).

The number one ask people had: where do Fable, Sol, and Kimi K3 land?

So on Thursday I ran it again, for

1) Fable, Sol, Kimi K3, and a undisclosed model ("Expo")
2) a larger subset of problems: [...fill in...]

### The Results

Fable and Sol Tied for this set at **33.3%** pass rate (10/30 strict checkpoint passes), with stealth model and Kimi K3 close behind at **26.7%** and **23.3%**, respectively.

![...fill](./images/scb-2-pass-rates.png)

I'll refresh on how slop code bench works real quick, or you can jump to the detailed results.

### Why SlopCodeBench

[You can learn a bit more in the original post](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/benchmarking-opus-5-on-slop-code-bench.md), but briefly: 

SlopCodeBench is a new-ish (March 2026, last update May 2026) long-horizon coding benchmark from [@GOrlanski](https://x.com/GOrlanski)'s lab at UW Madison. It addresses the thing that bothers me most about most coding benchmarks - that even "larger" more complex ones still divulge the whole problem up front. 

Each challenge in SlopCodeBench has multiple "checkpoints" - the model doesn't know the whole problem up front, it has to *evolve* the codebase over time as new requirements are divulged. 

It's a good paper. It's not that long. You should [read it](https://arxiv.org/html/2603.24755v1).

![the mechanic, from the paper: an initial spec becomes spec 2 becomes spec 3, and the solution goes Good Quality to Degradation Begins to Unmaintainable](images/scb-paper-degradation.png)

The biggest takeaway from "the fact that SlopCodeBench is 

## the benchmark subset

i pinged Gabe and asked him for a slightly broader subset of problems for this run - this one ran with 6 challenges, with circuit_eval being the overlap challenge. These are subsets, meant to be **directional**, not exhaustive.

- ...fill in....
- circuit_eval — **easy** (8 checkpoints)
- ...fill in....

There's an appendix at the end with all ..fill in... checkpoints explained in detail but I won't put that all here.

and then I ran them across all four models, in parallel, with a fresh context window per checkpoint. All models got the same prompts, and a mix of harnesses:

- Fable - Claude Code 2.1.219
- Sol - Codex CLI 0.145.0
- Kimi K3 - OpenCode 1.18.0
- Expo (stealth) - OpenCode 1.18.0

the metric we decided on for the first benchmark is the **strict pass**: everything new is green including every regression test that was inherited from previous checkpoints. So I carried that forward here.

A model fails a checkpoint if the solution has a **defect** - defects are detected by taking the models output, a CLI to run or in some cases e.g. an api server to poke at, and running a set of held-out black-box tests **against the produced entrypoint**.

- Model writes code for ck1
- Eval harness runs black-box tests against ck1
- Model writes code for ck2
- Eval runs black-box tests for ck1 and ck2
- etc

Again, the strict pass criteria means that if a model bungles something in checkpoint 4, it can't pass the following checkpoints because that failing part of the code carries forward (unless the model indavertently fixes an eval case in checkpoint 6 that was broken in checkpoint 4, but we didn't see this happen in practice).

## live progress

While each model got a few strict passes, they all steadily accumulated defects throughout the run

![images/scb-2-defect-counts.png](images/scb-2-defect-counts.png)

## final result

If our definition of success is "reached the final checkpoint with no defects" then opus 5 failed all three problems, but it failed slightly-less-badly than the other models. 

![defects left open at the end of each problem](images/scb-2-defects.png)

As far as cost vs. defects go, we see a totally different curve from the opus 5 eval. Expo is not plotted because we wont publish $/mtok for that one.

![cost vs defects, one point per model per problem](images/scb-2-cost-defects.png)

Fable and Sol tied for strict passes with 10 each. If you want a tiebreaker, Fable got 16 **isolated** passes while Sol got only 14.

![strict-passes](images/scb-2-strict-passes.png)

`circuit_eval` was the one challenge shared with the previous run. The newer models earned more strict passes across its eight checkpoints, while the previous Opus 5 run still left the fewest final defects. The suites diverge from checkpoint 3, though: the new final suite has 566 tests and the previous one has 557, so the raw final totals are context rather than a direct ranking.

![circuit_eval checkpoint results for the round-two models and the previous Opus 5 run](images/scb-2-circuit-round-comparison.png)

## the slop meter

You can check the previous [post](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/benchmarking-opus-5-on-slop-code-bench.md#the-slop-meter) for a breakdown of the code quality metrics in play here. 

I reproduced the same spread chart from the last post, for the new run. Looks like fable got the highest growth in cloned lines (9x increase betwen ck1 and ck8!!)

![percent change from checkpoint 1 to 8 for each rate metric, one dot per model, sorted by spread — cc_max and cloned_pct separate the models sharply, the rest cluster](images/scb-2-metric-spread.png)

Just as with the Opus 5 bench set, these models are close on growth in most metrics, with Fable growing more in some slop metrics, and Sol/Kimi growing more in others.

Again, I like that these measures are repeatable and don't use a model for judgement. But the link between any one of them and "is this codebase easy to change and evolve" is not yet established.

### <take on volume>

![source lines written — opus 5 wrote 29,065 against roughly 9,000 each for the other two](images/scb-volume.png)


<take on test fraction>

![the same source lines split into production and test code — opus 5 is 51% tests, opus 4.8 is 11%, sonnet 5 is 24%](images/scb-testsplit.png)

Will have to dig in more to know whether this is a model tic signal or just a "this is actually a really hard problem and warrants this much code".

### almost all the code written triggered the slop meter

For all models, a huge majority of the code lines tripped at least one of the benchmark's slop rules. These are the averages across each run's final problem snapshots:

Results from this run:

- Fable 5 — **86%**
- GPT-5.6 Sol — **95%**
- Kimi K3 — **82%**
- Expo — **79%**

Previous results, for context:

- opus 4.8 — **98%**
- opus 5 — **93%**
- sonnet 5 — **89%**

![slop density — new models in color vs old models in gray](images/scb-2-slop-density.png)

Again, I'd probably say that more than anything else, this is a sign that some of the code quality measures are a bit over-aggressive. But its nice to see the comparison

### these models write a lot of functions

Another interesting data point - opus 5 wrote 5x more functions than the other two models. But Opus 4.8 wrote a higher %% of single-use functions (almost 50% of its functions were called exactly once). And Sonnet 5's share of single-use functions is the highest at 71.5%.

![single-use functions as a share of all callables — opus 5 at 14.9%, opus 4.8 at 49.1%, sonnet 5 at 71.5%](images/scb-single-use.png)

FWIW I don't think lots of small functions is bad. I take it with a grain of salt these days, but I used to be a die-hard [Clean Code](https://www.oreilly.com/library/view/clean-code-a/9780136083238/) guy. Small descriptive function names are way better than lots of comments, yada yada

### Complexity grows over time for all models

<take - did the new models do worse or did they actually add even more complexity than the prev generation>

![mean cyclomatic complexity and duplicated-line percentage across all eight circuit_eval checkpoints, one line per model, including gray lines for previous 3 opus/sonnet run models](images/scb-2-complexity.png)

<!--Not a single model made it through all the challenges without increasing complexity across checkpoints. While Opus 5 has the lowest mean complexity, it also wrote 2000 functions. There's a tradeoff here: lots of small functions or fewer big ones. I don't think any of these complexity metrics can stand alone, but they give us some kind of composite signal.-->

![callables written against mean cyclomatic complexity, one point per model per problem — 4 new models plus the 3 previous models](images/scb-2-fnscatter.png)

<!--Both sonnet and Opus 4.8 answered the increasing complexity of the challenge checkpoints by making individual functions bigger rather than moving things around. Opus 4.8 is the extreme, up 70% over eight checkpoints, and its single worst function ended at a cyclomatic complexity of 93.-->

<!--Duplication is where they split. Opus 4.8 goes from 4.6% to 16.8%, with an inflection at ck3 — ~roughly where new requirements start fighting the initial design.-->

Again - here's what the first three checkpoints of circuit_eval ask for (full listing for all challenges in the appendix at the end):

- **ck1** — a CLI with `--help`, `--version`, a JSON output mode, and a `check` command that parses and validates a `.circ` circuit file. Every signal is a single bit.
- **ck2** — an `eval` command: pass the circuit some inputs, get the outputs back. Still one bit per signal, standard boolean operators.
- **ck3** — signals become **vectors**. `data[7:0]` instead of `data`, plus slicing, indexing, concatenation, new operators, and a width check on every operand.

<!--By the end one line in six is a copy of another line. The other two models came *down* over the same stretch.-->

<!--However, opus 5 is basically flat, 2.41 to 2.64. I've previously argued that "improving codebase quality over time" had not moved much between model generations. So if you trust duplication as a golden metric, you could argue that we did getting incrementally better in the last ~3 months. Big if though, and I think most software architecture experts would agree that it's not black and white.-->

## the shape of a better oracle for software quality

I haven't made progress on this part ([where does the time go!?](./side-quests/where-does-the-time-go.md)), but the idea is to have a small dumb model try each checkpoint.

This amplifies the **signal** of whether the smart models did a good job maintaining high-quality code that is easy to change. Whether a small model like Sonnet or Terra or even Haiku can implement checkpoint 8 impacts the smart models' score on checkpoints 1-7.

I might even make sense to have a small model do checkpoint N+1 for EVERY checkpoint and factor that into the pass rates for the smart model's checkpoint N - grading the quality of the codebase left behind.

![three lanes of the same eight checkpoints — fable 5, gpt-5.6-sol and opus 5 each build ck1 through ck7, then hand the codebase to sonnet 5 for ck8](images/scb-2-handoff-tree.png)


## something you can actually measure

My personal read of all this is that SlopCodeBench gives a signal for something I've so far only been able to argue from experience - that for real-shaped software engineering work, building one issue at a time, today's models can't be relied on to run lights-off without steering.

SCB is a measure of the future that I'll be keeping a close eye on. I wouldn't bet my codebase on a good score in Frontier Code, SWE-Marathon, or DeepSWE, but if/when models can score 80%+ on a (well-held-out) benchmark like SlopCodeBench which measures iteration over time, I'll feel a LOT better about setting them loose with the lights off. 

I won't posit when that will happen, because "when" matters less than having a good signal to know that it's happening. 
(Assuming nobody "accidentally" trains on test in the meantime).

## what's next / things i'd do differently 

I'll be reading some of the slopcodebench problems more deeply for inspiration and to curate a few that I think map well to the day-to-day building we do here at [@humanlayer_dev](https://x.com/humanlayer_dev).

Claude decided to parallelize by model, running each through three challenges in *sequence*. We could have just as easily done 3 models x 3 challenges in 9 parallel sessions and finished in 1-2 hours instead of 6.


As I said, I looked into applying the ruleset to our typescript monorepo, but the current slop-code-bench detectors are python only. It would be interesting to port those to TS and a few other languages. I hate to be that guy but I'd bet python is a more slop-prone language than most.

I think instead of focusing on strict pass and total defects, it will be interesting to explore more dimensions of the benchmark. In the current scoring, we're taking any failure along the way as an accumulated defect - unless the model happens to resolve that past defect in a future session, all of the remaining checkpoints cannot pass.

Many software factories include prompting for better style, include deterministic feedback during the code loop for complexity and a lot of these other software quality metrics. Today's results don't evaluate model code quality or success rates with those sort of guardrails in place. We used the "just-solve" version of the prompt from SlopCodeBench but there are other variations like including instructions about quality/duplication in the prompt. And it would be very interesting to re-run the whole eval with either or both of 1) an "aversarial review" loop w/ a model judging quality and 2) code-quality backpressure for things like cyclomatic complexity.

I'm not made of money or time but it would be fun to do a bigger dataset here.

And of course, most interesting is this idea of "can we amplify the slop signal by handing fable's codebase to a smaller model like sonnet".

## vibe check - the frontier is still dumb af

while this whole experiment was happening, in another session, opus 5 decided to go rogue and rewrite an email draft with new formatting, then send it to 100 people without checking in with me. Terrible.

> The user is upset because I made a critical mistake: 
> I overwrote their edited draft by patching it with the final version, then sent it out.

Sorry if you got one of those humanlayer product updates with the ugly header banner (I think the new claude-ism for this is "kicker"??)

really feeling the AGI over here friends

🫡 -dex

* * *

### Links From This Post

- [SlopCodeBench (paper)](https://arxiv.org/html/2603.24755v1)
- [SlopCodeBench runner (SprocketLab/slop-code-bench)](https://github.com/SprocketLab/slop-code-bench)
- [SlopCodeBench problems (gabeorlanski/scb-problems)](https://github.com/gabeorlanski/scb-problems)
- [Gabe Orlanski on X](https://x.com/GOrlanski)
- [SWE-bench Multilingual (dataset)](https://huggingface.co/datasets/SWE-bench/SWE-bench_Multilingual)
- [SWE-Marathon (Abundant AI)](https://www.swe-marathon.org/)
- [DeepSWE (Datacurve)](https://deepswe.datacurve.ai/blog/deepswe)
- [Frontier Code (Cognition)](https://cognition.com/blog/frontier-code)
- [Mutation testing (Wikipedia)](https://en.wikipedia.org/wiki/Mutation_testing)
- [Why Software Factories Fail (the longer argument this follows from)](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/wsff.md)

### Appendix: the challenge checkpoints

All 17, in order, straight from the prompts the models were handed. Each one arrives cold — the model has no idea any of the later ones exist.

**circuit_eval** — easy, simulation, 8 checkpoints

- **ck1** — a CLI with `--help`, `--version`, a JSON output mode, and a `check` command that parses and validates a `.circ` circuit file. Every signal is a single bit.
- **ck2** — an `eval` command: pass the circuit some inputs, get the outputs back. Still one bit per signal, standard boolean operators.
- **ck3** — signals become **vectors**. `data[7:0]` instead of `data`, plus slicing, indexing, concatenation, new operators (`MUX`, reductions, `EQ`), a width check on every operand, and `--radix` output formatting.
- **ck4** — three-valued logic. Inputs can now be `X` (unknown), and every operator has to say what it does with one.
- **ck5** — two more input formats. `check` and `eval` now read `.json` and `.bench` files as well as `.circ`, behind a `--format` flag.
- **ck6** — three analysis commands: `stats` for metrics, `lint` for warnings, `dot` for Graphviz export. All of them work with all three formats.
- **ck7** — `cone` (pull out a subcircuit), `truth-table` (enumerate every output), `equiv` (check two circuits match), plus a `--seed` flag for reproducible randomness.
- **ck8** — `opt`: a circuit optimizer with configurable passes, deterministic output, optional equivalence verification, and BENCH export.

**database_migration** — medium, databases, 5 checkpoints

- **ck1** — a CLI that reads migration specs out of JSON files and applies them to a SQLite database: create tables, add columns, change table structure.
- **ck2** — data migrations. Transform the rows that are already in there using SQL expressions, not just the schema around them.
- **ck3** — foreign keys, custom indexes, and advanced constraints. Relational integrity and query performance.
- **ck4** — rollback. Undo migrations one at a time or in batches, with dependency handling.
- **ck5** — dependency management. Migrations declare `depends_on`, and the tool has to resolve the order and detect circular dependencies.

**dynamic_config_service_api** — hard, system-design, 4 checkpoints

- **ck1** — a REST service that stores JSON config objects with immutable versions, scoping, rollback to any earlier version, and imports/inheritance across configs.
- **ck2** — a schema registry with its own versioning, schemas bound to configs, validation on create and on resolve, and ingesting raw YAML/TOML/JSON parsed into canonical JSON internally.
- **ck3** — a change-management workflow. Every new version starts as a draft, proposals gather human reviews, activation requires a quorum, and each proposal carries a deterministic diff.
- **ck4** — an org-level guardrail layer that runs policy bundles against resolved configs and the graph around them, blocking unsafe proposals with violation details distinct from schema errors.
