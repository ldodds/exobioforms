# How does the grammar work?


## Origin

`origin` this is the entry point for CBDQ its here that we branch out from.

Its main job is to setup the context for what follows, which includes choosing which probe (and hence planet) is providing the content.

Currently:

It also chooses the ecoregion for the sighting, the algorithm, then generates a msg.

## Messages

Generating a message:

Messages consist of a

- a sighting
- a card

## Sightings

- match comment, which references algoritm and planet
- bioform description, which is bespoke to the algorithm
- observation, which is bespoke to the algorith

The match comment can be generic, the other templates need to be bespoke

## Twitter Cards

These are SVG images constructed from a mixture of pre-defined assets and some procedural text.

A card is made up of:

- a background image. These are predefined, one per planet. It is selected by the `probeIndex` parameter
- a "swatch" of colours that have been seen. These are the 5 colours assigned to the `col1Code` - `col5Code` variables initalised at the start
- a title and description which rely on the `probePlanet` and `probeName` variables
- the log, which is intended to look like debug output. It uses the `probePlanet`, `probeName`, `surveyRegion`, `algo` parameters, plus some procedurally generated content for confidence, flight numbers, etc.

The card content should only use generic variables that are set by previous templates. All probes and algorithm templates will need to ensure these are populated.

## Choosing colours

Colour pickers use the `setColour` template to set a block of 5 colours which will be used in swatches.

When colours are set, we bind the `colourName` and `colourCode` pairs. The full list of pairings come from a separate spreadsheet.

## Naming conventions

* use camelcase
* `setXXX` indicates template that chooses and sets variables
* algo specific templates should be prefixed with the algo name, e.g. `orniXXX`
* probe specific templates should be prefixed with probe names
* generic templates can be unprefixed
