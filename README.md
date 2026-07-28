# GenRA — Human-Evaluation Study Data

This repository contains the stimuli, protocol, and anonymized ratings of the
blind pairwise human-evaluation study reported in the GenRA paper, in which
GenRA is compared against the closest state-of-the-art baseline
Puppeteer [[Song et al., 2025]](https://arxiv.org/abs/2508.10898).
It is released to support the reproducibility and scrutiny of the reported
results.

## Study design

We sample 40 rigged assets from Articulation-XL-2.0
[[Song et al., 2025](https://arxiv.org/abs/2502.12135);
[HF dataset](https://huggingface.co/datasets/Seed3D/Articulation-XL2.0)],
filtering for visually clean meshes and semantically meaningful joint names.
The final set spans mechanisms, characters, humanoids, plants, household
objects, animals, and vehicles/robots. For each asset, three prompts are
produced once by a VLM-assisted procedure from the asset render, category
label, and joint list, and are used unchanged for both methods; any existing
animations are discarded, making the comparison zero-shot. Three stochastic
trials are drawn per (asset, prompt), yielding
40 × 3 × 3 = **360 GenRA–Puppeteer pairs**.

A blind pairwise preference study is conducted with **27 raters**. Each pair
is judged **3 times independently**, for a total of **1080 ratings**. For each
pair, a rater selects the better animation or declares a tie. The
tie-aware preference score is reported as

$$
\text{score} = \frac{A + 0.5 \cdot \text{Tie}}{A + B + \text{Tie}},
$$

where $A$ and $B$ denote the number of ratings favoring GenRA and Puppeteer,
respectively.

## Instructions shown to raters

Each rater was presented with the following question alongside every pair:

> **Which animation is better overall for the given prompt?**
>
> Evaluate based on the following criteria:
>
> 1. **Prompt adherence.** How accurately and completely the animation
>    reflects the content, actions, constraints, and intent specified in the
>    prompt.
> 2. **Motion quality.** How good is the animation itself, including:
>    - motion naturalness, consistency, and physical plausibility;
>    - motion richness, granularity, and level of detail;
>    - absence of motion-related artifacts or failures of any kind.
>
> **Important.**
>
> - If the prompt explicitly requires unusual, exaggerated, stylized, or
>   physically implausible motion, do not penalize the animation for lacking
>   realism in those aspects. Instead, evaluate whether the motion faithfully
>   and clearly realizes the prompt.
> - Ignore factors unrelated to motion quality, such as static visual
>   appearance (textures, materials, colors), lighting, or overall rendering
>   quality, unless they prevent you from assessing the motion. Focus on
>   motion and behavior.
>
> As a guiding intuition, form an expectation of the motion and behavior
> implied by the prompt, and compare this expectation to the observed
> animations. You may want to prefer the animation whose motion and behavior
> most closely align with this expectation.

## Repository layout

```
assets/
└── <asset_uid>/                          # 40 rigged assets from Articulation-XL-2.0
    └── prompt_{01,02,03}/                # 3 prompts per asset
        └── try_{01,02,03}/               # 3 stochastic trials per prompt
            ├── prompt.txt                # text prompt shown to both methods
            ├── approach_a.mp4            # rendered animation — method A
            └── approach_b.mp4            # rendered animation — method B
GenRA_evaluation_anonymized.xlsx          # study protocol and ratings
```

### Spreadsheet contents — `GenRA_evaluation_anonymized.xlsx`

The workbook has three sheets.

- **Pairs** (360 rows). One row per evaluated pair with columns
  `Pair_ID`, `Model_UID`, `Prompt`, `Try`, `Prompt` (text).
- **Dashboard** (27 rows). One row per rater batch with columns
  `Batch_ID`, `Assigned_User_ID`, `Status`, `Assigned_At`, `Completed_At`,
  `Tasks_Total`. Each rater completed one batch of 40 tasks.
- **Statistics** (1080 rows). One row per individual rating with columns
  `Batch_ID`, `User_ID`, `Pair_ID`, `Model_UID`, `Prompt`, `Try`, `Winner`
  (`A`, `B`, or `Tie`), `Response_Time_ms`.

## Anonymization

During the study, the mapping from `approach_a` / `approach_b` to the
compared systems (GenRA, Puppeteer) was randomized per pair and hidden from
raters to ensure blinding. In the released spreadsheet, rater identities are
replaced with anonymous `USER_xxx` codes.

## Aggregate outcome

Over the 1080 collected ratings: $A = 706$, $B = 176$, $\text{Tie} = 198$.

Please consult the paper for the final tie-aware preference score and its
interpretation.

## Obtaining the data

The `.mp4` stimuli and the `.xlsx` ratings file are tracked with
[Git LFS](https://git-lfs.com/), so `git-lfs` must be installed before
cloning. On macOS use `brew install git-lfs`, on Debian/Ubuntu
`sudo apt-get install git-lfs`. Then run `git lfs install`. Afterwards, clone normally:

```bash
git lfs install
git clone https://github.com/genra-submission/GenRA_data.git
cd GenRA_data
```

If you already cloned the repository without `git-lfs` installed, pull the
actual LFS-tracked content with:

```bash
git lfs install
git lfs pull
```
