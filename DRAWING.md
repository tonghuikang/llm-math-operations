# Visual Edge Alignments in architecture.drawio.png

Source: https://raw.githubusercontent.com/tonghuikang/llm_architecture/refs/heads/master/architecture.drawio.png

This document describes how the boxes in the Grouped Query Attention (GQA) diagram are visually aligned to show which tensor dimensions correspond to each other.

## Box Inventory (bottom to top)

| Box | Dimensions (y-axis x x-axis) | Depth |
|---|---|---|
| hidden_states (input) | embedding size x sequence length | - |
| q_proj, k_proj, v_proj | linear projection layers | - |
| position_embeddings | applied to query and key | - |
| query_states | (num heads x head dim) x sequence length | - |
| key_states | (num heads x head dim) x sequence length | - |
| value_states | embedding size x sequence length | - |
| attn_weight (lower) | sequence length x sequence length | num heads |
| attn_weight (upper) | sequence length x sequence length | num heads |
| hidden_states (output) | embedding size x sequence length | - |

## Visual Edge Alignments

### Pairwise dot product: query_states x key_states -> attn_weight

- **query_states** x-axis (sequence length) aligns with **attn_weight** y-axis (sequence length rows)
- **key_states** x-axis (sequence length) aligns with **attn_weight** x-axis (sequence length columns)
- **query_states** y-axis (num heads x head dim) aligns with **key_states** y-axis (num heads x head dim) — these are drawn at the same height; this dimension is contracted away in the dot product

### Matrix multiplication: attn_weight x value_states -> hidden_states (output)

- **attn_weight** (upper) x-axis (sequence length columns) aligns with **value_states** x-axis (sequence length) — this dimension is contracted away
- **attn_weight** y-axis (sequence length rows) carries through to **hidden_states** (output) x-axis (sequence length)
- **value_states** y-axis (embedding size) carries through to **hidden_states** (output) y-axis (embedding size)

## Pink/Red Edge Highlights

The pink highlighting on certain edges of boxes tracks the **num_heads** dimension as it flows through the diagram:

- Pink edges on **query_states** and **key_states** indicate the heads dimension within those tensors
- Pink edges become the stacked depth (num heads) of the **attn_weight** cube
- Pink edge on **value_states** shows it also carries the heads dimension
- Pink on **hidden_states** (output) represents the output after heads are recombined

## Data Flow Summary (bottom-to-top, matching the PNG)

```
                              hidden_states (output)
                                     ^
                                     |
                              matrix multiplication
                               ^              ^
                               |              |
                          attn_weight    value_states
                               ^              ^
                               |              |
    causal masking + scaling + softmax        |
                               ^              |
                               |              |
                      pairwise dot product    |
                          ^         ^         |
                          |         |         |
                    query_states  key_states  |
                          ^         ^         ^
                          |         |         |
                          +--pos_emb+         |
                          |         |         |
                          |  +-repeat_kv------+
                          |  |                |
                        q_proj  k_proj     v_proj
                          |     |             |
                          +-----+-------------+
                                |
                         hidden_states (input)
```
