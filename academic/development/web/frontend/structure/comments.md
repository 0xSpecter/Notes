# Comments

[(← back)](./README.md)

Useful ways to design comments and sectioning within files.

Especally for longer css files and scss partial files as they can get quite long too.

## Headers

Nice looking comment headers.
Every line is exactly 80 characters wide.
Just copy them over.

If indented just remove top and bottom straight lines x amount of indent.

### Header

With description:

```ts
// ╔═══════════════════════════════════════════════════════════════════════════╗
// ║  TITLE                                                                    ║
// ║  Description.                                                             ║
// ╚═══════════════════════════════════════════════════════════════════════════╝
```

Without description:

```ts
// ╔═══════════════════════════════════════════════════════════════════════════╗
// ║  TITLE                                                                    ║
// ╚═══════════════════════════════════════════════════════════════════════════╝
```

### Subheader

With title:

```ts
// ── TITLE ────────────────────────────────────────────────────────────────────
```

Empty:

```ts
// ─────────────────────────────────────────────────────────────────────────────
```

### Individual components

`//` `╔` `╗` `╚` `╝` `═` `║` `─`

Top

```ts
// ╔═══════════════════════════════════════════════════════════════════════════╗
```

Side

```ts
// ║                                                                           ║
```

Bottom

```ts
// ╚═══════════════════════════════════════════════════════════════════════════╝
```
