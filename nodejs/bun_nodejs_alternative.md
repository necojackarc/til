## Bun - One of the Node.js Alternatives

[Bun](https://bun.sh) is one of the Node.js alternatives that natively supports TypeScript. It's significantly faster than Node.js and Deno.

If you'd like to use REPL, `bun repl` is much nicer to use than `node`.


## Installation

Use [mise](https://mise.jdx.dev/) or the follow the official document. I personally prefer to use mise for almost everything I need to manager versions.

```
mise install bun
mise use -g bun@<LATEST_VERSION>
```

## REPL

```
bun repl
```

This automatically loads the libraries in `.node_modules`.
