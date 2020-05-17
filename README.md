# dune-ocaml-ppx-bug

This appears to be a bug triggered by a combination of

```
ocamlc
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

1. Run `dune runtest`.  Observe that there's an (expected) test failure.

2. Run `./MAKE.OK`.  Observe the same (expected) test failure.

3. Run `./MAKE.BUSTED`.  Observe that it fails to even build.

# What did I just see?

1. dune builds and runs this expect-test correctly

2. a hand-built script that invokes the PPX rewriters does the same.  This script was built by reverse-engineering from dune's `_build/log`.

3. but a naive `ocamlfind ocamlc` invocation fails, complaining
```
File "<command-line>", line 1, characters 0-3:
	Error: constant expected
```

# What's wrong?

Well, ppx_expect needs a "library-name" arugment, and the format of that argument (after all shell-quoting has been removed) is

```
library-name="foo"
```

This needs to be quoted in a list of shell-arguments, or the
double-quotes will be parsed by the shell.  But it has to be quoted
again, to be passed to ocaml's `-ppx` argument.  Ocamlfind doesn't do
any of this, assuming that the user will do so.

One could blame this on ocamlfind, or on ocamlc, but I think the blame
falls on ppxlib (which is the library asking for this argument).  It
uses the ocaml AST (parsetree.mli) parser to parse these arguments,
and if it's going to do that, it should provide a way to pass such
arguments in a file, and that way should be the default.

# Why doesn't this fail in `dune` ?

`dune` doesn't use ocaml's `-ppx` facility -- it builds and invokes
PPX rewriters directly before passing the results to ocamlc.
