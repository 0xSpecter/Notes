# Assembly

[← Back: Languages](./README.md)

## RISC-V

### ABI

Format: hardware register · ABI name — comment (preserved across calls?)

- `x0` · `zero` — Hard-wired zero
- `x1` · `ra` — Return address (not preserved)
- `x2` · `sp` — Stack pointer (preserved)
- `x3` · `gp` — Global pointer
- `x4` · `tp` — Thread pointer
- `x5-7` · `t0-2` — Temporaries (not preserved)
- `x8` · `fp` — Frame pointer (preserved)
- `x8-9` · `s0-1` — Saved registers (preserved)
- `x10-11` · `a0-1` — Return values (not preserved)
- `x10-17` · `a0-7` — Function arguments (not preserved)
- `x18-27` · `s2-11` — Saved registers (preserved)
- `x28-31` · `t3-6` — Temporaries (not preserved)
