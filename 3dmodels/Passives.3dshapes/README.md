# Passives.3dshapes

STEP models, filename matching the footprint that references them.

Footprints should reference models as:

    (model "${KICAD_LIBRARY_3DMODELS}/Passives.3dshapes/<name>.step" ...)

The indexer resolves this by ignoring the variable and matching the remaining
relative path against the repository, so it works regardless of what each
contributor has configured locally.
