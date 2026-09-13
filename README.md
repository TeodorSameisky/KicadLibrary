# KiCad Library

Company KiCad library: symbols, footprints and 3D models, served to KiCad's
Remote Symbols panel by the KiCad Library Manager.

## Layout

```
parts/<IPN>.yaml                                     internal part numbers
mpns/<MPN>.yaml                                      manufacturer part numbers

symbols/<Library>.kicad_symdir/<Symbol>.kicad_sym    one symbol per file
footprints/<Library>.pretty/<Footprint>.kicad_mod    one footprint per file
3dmodels/<Library>.3dshapes/<Model>.step             one model per footprint
```

## Parts and manufacturer parts

Only **IPNs** are placed in KiCad. An IPN is a container: it names a symbol and
a footprint, carries the field values, and lists the approved manufacturer
parts that satisfy it.

```
IPN  RES-0603-10K-1P  --+-- MPN  RC0603FR-0710KL   (Yageo, preferred)
                        +-- MPN  CRCW060310K0FKEA  (Vishay)
```

MPNs are separate files rather than embedded in each IPN, because one MPN can
serve several IPNs. When a manufacturer obsoletes a part you edit one file
instead of hunting every IPN that references it.

Approving an alternate source is a small, readable diff -- which is the point
of keeping this in git.

The three directories mirror each other by library name. `Passives` exists in
all three, and that correspondence is what lets references resolve by lookup
rather than by search.

`.kicad_symdir` is KiCad 10's *unpacked* symbol format: one symbol per file
instead of many symbols in a single `.kicad_sym`. It exists so that libraries
diff and merge sanely in git.

## Conventions

**Filename matches the asset name.** `(symbol "R")` lives in `R.kicad_sym`;
`(footprint "R_0603_1608Metric")` in `R_0603_1608Metric.kicad_mod`.

The one exception is inheritance. A derived symbol names its parent, and the
parent's file is named after *the parent*:

```
R_0603_10K_1P.kicad_sym   contains  (symbol "R_0603_10K_1P" (extends "R") ...)
R.kicad_sym               contains  (symbol "R" ...)   <- the graphics live here
```

A derived symbol carries no graphics of its own, so it is only usable together
with its parent.

**Generic parent, package-specific children.** `R` and `C` define the drawing.
`R_0603` extends `R` and binds a footprint. Symbols describe *shape and land
pattern only* -- no manufacturer data, no values.

Everything orderable lives in `parts/`. A second approved source is a change to
one IPN file; it must never mean editing a symbol.

**3D model references** use a variable and a repo-relative path:

```
(model "${KICAD_LIBRARY_3DMODELS}/Passives.3dshapes/R_0603_1608Metric.step" ...)
```

## Using this library directly in KiCad

Set these path variables (Preferences -> Configure Paths):

| Variable | Value |
| --- | --- |
| `KICAD_LIBRARY_DIR` | the path to this repository |
| `KICAD_LIBRARY_3DMODELS` | `${KICAD_LIBRARY_DIR}/3dmodels` |

`sym-lib-table` and `fp-lib-table` in the repository root can then be merged
into your global tables. Most users will not need this -- the Remote Symbols
panel fetches parts on demand instead.

## Git LFS

Not enabled. At a few hundred 3D models the repository stays around a few
hundred megabytes, which plain git handles fine, and GitHub bills LFS
bandwidth on every fetch.

Enable it if 3D models start being revised often: binaries do not
delta-compress, so each revision stores a full copy. Enabling LFS for *new*
files takes two minutes; moving *existing* files into it rewrites history and
forces everyone to re-clone.

## Example content

`Passives` contains four example symbols showing the inheritance pattern, plus
two IPNs and three MPNs. Delete them once real content is imported.

Footprints and 3D models are not included -- the example symbols reference
footprints that do not exist yet, which the indexer reports as unresolved.
That is the expected state until the company library is imported.
