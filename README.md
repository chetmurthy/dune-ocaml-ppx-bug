# dune-ocaml-ppx-bug

This appears to be a bug triggered by a combination of

``
ocaml
ppxlib
ppx_expect
```

And, I guess, `ocamlfind`

# Prerequisites to reproduce

You'll need to install `dune`, `ocamlfind` and `ppx_expect`.

```
opam install ocamlfind ppx_expect dune
```

# To repro

1. Run `dune runtest`.  Observe that there's an (expected) test error.

2. Run `./MAKE.OK`.  Observe the same (expected) test error.

3. Run `./MAKE.BUSTED`.  Observe that it fails.
