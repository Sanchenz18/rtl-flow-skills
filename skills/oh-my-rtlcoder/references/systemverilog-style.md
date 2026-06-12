# SystemVerilog Project Style Reference

## File Organization

- Use `.sv` for SystemVerilog source files.
- Use `.vh` for header/define files.
- Include headers with `` `include "filename.vh"`` at the top of files.
- Use 4 spaces to indent.

## Naming Conventions

- Modules and signals: snake_case, for example `vector_compute_unit`, `compute_in_rs0`.
- Constants and macros: UPPER_CASE, for example `LANE`, `WBK_CHAN`, `ALWAYS_FF_P_N`.
- Enable signals: `*_en`, for example `abs_unit_en`, `mac_unit_en`.
- Valid signals: `*_vld`, for example `lane_vld`.
- Result signals: `*_rslt`, for example `va_rslt`.
- Instance names: `u_` prefix, for example `u_vcu_control`.
- Generate labels: descriptive names, for example `data_process_generate`.

## Code Structure Rules

### Combinational Logic

- Use `assign` for simple wiring and simple boolean expressions (e.g., `assign out = a & b;`, `assign sel = (state == IDLE) & en;`).
- Use `always_comb` for combinational logic that involves control structures (`if`/`case`/`for`), multi-statement blocks, or complex priority/decoding logic.
- One `always_comb` block drives one signal only.
- Use ternary operations and no `if-else` in `always_comb`.
- Every `if` in `always_comb` must have an else, a simple `if-else` statement can be placed on two line.
- Internal signal names should be concise; use abbreviations and add inline comments to explain meaning.
- Key logic should have brief comments that explain intent, not implementation mechanics.
- All comments in SV code must be English.

### Sequential Logic

- Use `always_ff` for all clocked registers; one `always_ff` block drives one signal only.
- Sequential logic may omit `else`; `if` without `else` is valid when no default action is needed.
- Data path registers may omit reset; reset is required for control or path-critical signals.

## Module Skeleton

```systemverilog
module module_name (
    input  logic       clk  ,
    input  logic       rst_n,

    // Input data paths
    input  logic [31:0] input_signal,

    // Output data and control
    output logic [31:0] output_signal
);
    // Local parameters
    localparam PARAM_NAME = value;

    // Internal signals
    logic             vld       ;  // valid flag
    logic      [31:0] alu_out   ;  // ALU result
    logic      [31:0] mux_dat   ;  // mux output data
    // Variable declaration must be made before use.
    logic [3:0][31:0] multi_rslt;  // multi-group rslt

    sub_module u_sub_module (
        .port_name ( signal_name ),
        .port_name ( signal_name )
    );

    assign output_signal = input_signal & mask;

    always_comb begin : block_name
        // Complex combinational logic
        // Bad coding
        /* 
        if (input_vld) multi_rslt = a_i * b_i;
        else           multi_rslt = 'b0;
        */
        // Good coding
        multi_rslt = input_vld ? a_i * b_i : 'b0;
    end

    always_ff @(posedge clk or negedge rst_n) begin : block_name
        if (!rst_n)      alu_out <= 'b0;
        else if (alu_en) alu_out <= alu_result;  // DFF ALU output when enabled
        // Alignment of signal assignment statements
    end

    always_ff @(posedge clk or negedge rst_n) begin : block_name
        if (vld_set)      vld <= 1'b1;  // set valid on condition
        else if (vld_clr) vld <= 1'b0;  // clear valid
    end

endmodule
```

## Comments

- Use `//` for single-line comments.
- Use `/* */` for multi-line comments.
- Place comments above the code they describe or aligned to the right.
- Use `//////` for major section headers.
- Keep comments brief and focused on why the logic exists.
- All comments must be English.

## Port Declarations

- Use one port per line.
- Align port types and names.
- Use consistent spacing for alignment.

```systemverilog
input  logic [31:0] input_signal ,
output logic [31:0] output_signal
```

## Signal Types

- Use `logic` for all signals, both combinational and sequential.
- Use `enum` for finite state machines.

## Instantiation Style

- Use named port connections only.
- Do not use positional port connections.
- Align port connections vertically.
- Use `generate` blocks for parameterized instantiation.
- Use `genvar` with explicit loop bounds.

## Reset and Clocking

- Clock signal name: `clk`.
- Active-low reset signal name: `rst_n`.
- Use `'b0` for zero reset values and specific values for non-zero defaults.
- Data path registers may omit reset.
- Control and path-critical signals should be reset.
- Sequential logic may omit `else` when no default action is needed.

## Data Widths

- Use `logic [N-1:0]` for N-bit signals.
- Use `logic [7:0][31:0]` for unpacked arrays.
- Use `logic [31:0]` for 32-bit data paths.
- Use `logic [255:0]` for vector register data.

## Generate Blocks

```systemverilog
    generate
        for (genvar i=0;i<GEN_NUM;i++) begin : block_generate_name
          // instantiation here
        end
    endgenerate
```

## Case Statements

- Use `case` for priority encoding.
- Always include a `default` case.
- Align case items vertically.

## Header File Organization

- Group defines by functionality.
- Use comments to separate sections.
- Define macros for common patterns.
- Use `ifndef` guards if needed.

## Signal Width Consistency

- Match signal widths in assignments.
- Use explicit zero-padding when needed, for example `{176'b0, va_rslt}`.
- Use concatenation for bit assembly.
- Use replication for repeated patterns, for example `{32{1'b1}}`.

## FSM Coding Style

```systemverilog
    enum logic [1:0] {IDLE, COMPUTE, DONE} curr_state, next_state;

    always_ff @(posedge clk or negedge rst_n) begin
        if (!rst_n) curr_state <= IDLE;
        else        curr_state <= next_state;
    end

    always_comb begin
        case (curr_state)
            IDLE:    next_state = start ? COMPUTE : IDLE;
            COMPUTE: next_state = done ? DONE : COMPUTE;
            DONE:    next_state = IDLE;
            default: next_state = IDLE;
        endcase
    end
```

## Code Density

- Keep modules focused on a single functionality.
- Use meaningful signal names.
- Avoid unnecessary intermediate signals.
- Group related signals together.

## Documentation

- Document non-obvious patterns in `AGENTS.md`.
- Include architecture diagrams when helpful.
- Document signal bit-field mappings.
- Explain control signal timing.

## Tool Integration

- Use consistent 2-space indentation.
- Use tabs for alignment only when needed by the existing file style.
