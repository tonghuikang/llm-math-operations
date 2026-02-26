# Number Rendering Requirements

This file documents the current number-display rules used by [`index.html`](/Users/htong/Desktop/llm-math-operations/index.html).

## Scope

- **Matrix/trace/weight values** use `formatValue(...)`.
- **Token stream** uses raw token digits (`0..9`) as plain integers.
- **Result bar** (`model`, `expected`, `check`) uses plain full integers (no forced `+`).

## Matrix / Trace Formatting Rules

For each numeric value `v` (Note: there should be at most 4 characters):

1. `v` is non-finite (`NaN`, `Infinity`): render as `String(v)`.
   - `Infinity` renders as `+inf`, `-Infinity` as `-inf`.
2. Exact zero (`0` or `-0`): render as `0`.
3. Very small non-zero (`|v| < 0.1`):
   - Convert with `toExponential(0)` and parse `mantissa e exponent`.
   - If exponent is negative and `-exp <= 9`, render compact form:
     - `sign + mantissa + "f" + (-exp)`
     - Examples: `-1e-3 -> -1f3`, `+5e-4 -> +5f4`.
   - Otherwise, render `+0.0` for super-tiny values.
4. Large (`|v| >= 1000`): scientific with integer mantissa for exponent 0 to 3.
   - `+1e3`, `-9e3`.
   - If exponent is 9 or larger (`>=9`), render `+inf`/`-inf` instead.
5. Medium (`100 <= |v| < 1000`):
   - Round to nearest integer.
   - If rounded value becomes `1000`, render scientific (`+1e3`) instead of `+1000`.
   - Otherwise render signed integer (`+123`, `-987`).
6. Sub-large (`0.1 <= |v| < 100`):
   - If `1 <= |v| < 10`: one decimal place with truncation toward zero (`+1.5`, `-1.9` -> `-1.9`).
   - If `10 <= |v| < 100`: integer by truncating positive values and flooring negative values, then show trailing decimal point (`+70.5` -> `+70.`, `-70.9` -> `-71.`).
   - If `0.1 <= |v| < 1`: show first decimal digit only (`+0.45` -> `+0.4`, `-0.96` -> `-0.9`).
   - Output in this branch is exactly 4 characters.

## Sign Rules

- Positive non-zero values include `+`.
- Negative values include `-`.
- Exact zero is always `0`.

UI highlighting also treats `+0.0` as zero-colored (grey), since its numeric value is not exactly `0` even though it renders as near-zero.
Compact `f` values with exponent greater than 4 (e.g. `+5f5`) are shown with a grey background and black text for emphasis.

## Input / Output Exceptions

- **Token row** (`tok`) stays raw integers with no sign/scientific formatting.
- **Result bar** values are plain integers:
  - Example: `model 12`, `expected 12`, `check 1`.
- **generation_argmax** values are also plain integers in flow matrices (`act.generation_argmax`).

## Cell Width / Typography

- Matrix-like cells use fixed width `4ch`.
- Numeric rendering is monospaced with tabular figures.
- Title and control/result cells are allowed to expand to avoid clipping.

## Reference Example (`embed 10x3`, expected style)

- `r0`: `+1e3`, `0`, `0`
- `r1`: `+1e3`, `+1`, `+5f4`
- `r2`: `+1e3`, `+2`, `+1f3`
- `r3`: `+1e3`, `+3`, `+2f3`
- `r4`: `+1e3`, `+4`, `+2f3`
- `r5`: `+1e3`, `+5`, `+3f3`
- `r6`: `+1e3`, `+6`, `+3f3`
- `r7`: `+1e3`, `+7`, `+4f3`
- `r8`: `+1e3`, `+8`, `+4f3`
- `r9`: `+1e3`, `+9`, `+5f3`
