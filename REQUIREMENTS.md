# REQUIREMENTS

This file is the canonical requirements spec for the project. If requirements conflict across messages, the latest user decision wins.

## 1) Project Context

- Build a spreadsheet-style visualization of a hand-crafted 121-parameter Qwen3 transformer that adds two 10-digit integers.
- Primary sources:
  - https://gist.github.com/Wonderfall/7d6f49aa6703352f94d3d80b4cd31e15
  - https://x.com/w0nderfall/status/2026496621668634997
- Visual alignment reference:
  - https://raw.githubusercontent.com/tonghuikang/llm_architecture/refs/heads/master/architecture.drawio.png

## 2) Core UI

- Single-page app in `index.html`.
- Top bar includes:
  - input `a` and `b` (range `0..9999999999`),
  - `Compute` and `Random` buttons,
  - token strip,
  - `model`, `expected`, `check`, `status` fields.
- Token strip must show input and generated tokens with color coding.
- `model`, `expected`, `check` must show full plain integers (no forced `+` formatting).

## 3) Spreadsheet Aesthetic

- Must look like a spreadsheet implementation (Google Sheets/Excel style).
- Render as cells/tables with numbers.
- No explanatory prose blocks in the visualization area.
- No separate diagram widgets or explanation panel for dependencies.
- Light/white theme and monospaced typography.
- Wide layout (1600px+), no squeezed/compressed matrix look.

## 4) Color Rules

- Parameters: purple/lilac.
- Intermediate activations: orange/amber.
- Input tokens/cells: green.
- Output tokens/cells: blue.
- Exact zeros must be visually greyed.

## 5) Always-Visible Data (No Collapse)

- All weight matrices always visible.
- All intermediate activation matrices always visible after forward pass.
- No accordion/click-to-reveal data for matrices.

## 6) Matrices To Render

### 6.1 Weight matrices (always shown)

- `embed` `10x3`
- `input_norm` `1x3`
- `q_proj` `8x3`
- `k_proj` `2x3`
- `v_proj` `2x3`
- `q_norm` `1x2`
- `k_norm` `1x2`
- `o_proj` `3x8`
- `post_attn_norm` `1x3`
- `gate_proj` `2x3`
- `up_proj` `2x3`
- `down_proj` `3x2`
- `final_norm` `1x3`

### 6.2 Activation/trace matrices

- Full sequence attention matrices must be shown as `35x35`:
  - `attn_score 35x35`
  - `attn_weight 35x35`
- Q/K/V activations must be shown (full sequence, not only a single-token slice).
- RoPE-transformed head activations must be shown.
- Residual, norm, MLP, and logits stages must be shown.

## 7) Layout and Alignment Rules

- Weight matrices live on the side column.
- Activation matrices live in the flow column.
- Activation stack ordering must follow dependency flow consistently in one direction (no mixed up/down dependency direction).
- Arrangement must follow the architecture reference style for tensor-flow alignment.
- `attn_score` and `attn_weight` placement must match latest requested ordering.

## 8) Interaction and Dependency Links

- Clicking any visible cell toggles modes in this cycle:
  - sink mode (show parents / derivation inputs),
  - source mode (show children / downstream effects),
  - none.
- Clicking a different cell resets selection and starts at sink mode.
- No separate "link panel": dependencies are shown as lines in-place.
- For exact zero values, links must still render.
- Masked attention cells must still participate in linkage visualization.
- Token row cells must participate in dependency links.

## 9) Graph/Lineage Integrity Requirements

- Except true input/base cells and the final generated token output, visible cells should participate as both source and target in lineage.
- Final generated token output is sink-only.
- Cells with no parents (true literals/params/inputs) show selected highlight only and no dependency lines.
- Lineage should be generated from computation structure (not brittle manual one-off edge hacks).

## 10) Number Formatting Requirements

- Canonical number rules are maintained in `NUMBERS.md`.
- Additional required points:
  - exact zero renders as `0`,
  - `+1000` style values render as `+1e3`,
  - small exponents use lowercase `f` form (example: `-1f3` for `-1e-3`),
  - `+0.0` is allowed only for extremely tiny non-zero values,
  - avoid forms like `-10.0` when integer form `-10` applies,
  - all matrix numbers are monospaced,
  - matrix cells are fixed width (`4ch`).

## 11) Forward Pass Engine Requirements

- Pure JavaScript, no external dependencies.
- Must implement Qwen-like operations used in this handcrafted model:
  - `rmsNorm(x, weights, eps)`,
  - RoPE rotation,
  - causal masked attention,
  - GQA-style attention path used by model,
  - SwiGLU MLP (`down(silu(gate(x)) * up(x))`),
  - tied embedding logits.
- Must run autoregressive generation for output digits.

## 12) Correctness Checks

- Must verify known cases:
  - `5 + 7 = 12`
  - `9999999999 + 1 = 10000000000`
  - `1234567890 + 9876543210 = 11111111100`
  - `0 + 0 = 0`
  - `9999999999 + 9999999999 = 19999999998`

## 13) Documentation Requirements

- `README.md` includes context and source citations.
- `NUMBERS.md` defines number rendering behavior.
- `REQUIREMENTS.md` (this file) captures the full requirements baseline.
