# llm-math-operations

## Context

This project is a spreadsheet-style visualization of a hand-crafted **121-parameter Qwen3 transformer** that performs addition of two 10-digit numbers.

The web UI (`index.html`) shows:
- model weights (always visible),
- forward-pass intermediate tensors (always visible),
- tokenized input/output stream for generation,
- decoded addition result and correctness check.

## Sources

- Original model/source gist: https://gist.github.com/Wonderfall/7d6f49aa6703352f94d3d80b4cd31e15
- Author context thread: https://x.com/w0nderfall/status/2026496621668634997
- Layout reference (architecture diagram): https://raw.githubusercontent.com/tonghuikang/llm_architecture/refs/heads/master/architecture.drawio.png
- Spreadsheet-style inspiration: https://byhand.ai/
- Spreadsheet-style inspiration: https://github.com/dabochen/spreadsheet-is-all-you-need
- Spreadsheet-style inspiration: https://github.com/tonghuikang/llm_architecture
