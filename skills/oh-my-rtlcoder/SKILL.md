---
name: oh-my-rtlcoder
description: Write, edit, or review SystemVerilog code using the user's project coding style. Use when Codex works on .sv or .svh files, reviews SV naming/formatting/reset/clocking/FSM/generate/case/instantiation style, or needs comments and signal names to match the project conventions.
---

# SystemVerilog Code Style

## Quick Use

Apply this style whenever writing or reviewing SystemVerilog for the user's codebase.

Before producing code, read `references/systemverilog-style.md` if the task involves:

- Creating a new `.sv`or `.svh` file.
- Changing module ports, internal signals, sequential logic, FSMs, generates, cases, or instantiations.
- Reviewing code for style compliance.
- Deciding whether to reset a register, use `assign` vs `always_comb`, or split `always_ff` blocks.

## Core Rules

- Focus on RTL coding, don't write testbench platforms.
- Use `.sv` for SystemVerilog sources and `.vh` for headers.
- Use snake_case for modules and signals.
- Use UPPER_CASE for constants and macros.
- Use `clk` and active-low `rst_n`.
- Use `logic` for signals; use `enum` for FSM state types.
- Use one port per line and align port direction/type/name columns.
- Use named port connections only; prefix instances with `u_`.
- Use `assign` for simple combinational assignments; reserve `always_comb` for complex multi-statement logic.
- Use one `always_ff` block to drive one signal only.
- Allow data path registers to omit reset; reset control or path-critical signals.
- Allow sequential `if` without `else` when no default action is required.
- A brief comment must be written when declaring a signal.
- Keep comments brief, in English, and focused on intent.

## Review Checklist

When reviewing existing SV code, check:

- File suffixes and header includes.
- Naming patterns: `*_en`, `*_vld`, `*_rslt`, `u_*`, UPPER_CASE macros.
- Port formatting and one-port-per-line declarations.
- Width consistency, explicit padding, concatenation, and replication.
- Separate sequential blocks per driven signal.
- Reset policy: control signals reset, data path registers reset only when necessary.
- `case` statements include `default`.
- Generate loops use `genvar`, explicit bounds, and descriptive labels.

## Output Expectations

- Preserve the surrounding repository style when it is more specific than this skill.
- Keep modules focused on one function.
- Avoid unnecessary intermediate signals.
- Group related signals together.
- Add comments only for key or non-obvious logic.
- Do not add Chinese comments inside SV code; comments must be English.
