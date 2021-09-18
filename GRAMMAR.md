# How does the grammar work?

This is a grammar for a bot that will run as part of Cheap Bots Done Quick.

It generates a tweet that consists of:

- a sighting of an alien bioform, as reported by one of four fictional algorithms
- a twitter card that contains further information about the sighting, including metadata about the survey, the colours of the bioform, an image of the planet, etc.

The entry point for the grammar, like all CBDQ bots is the `origin` template. Its from here that we branch out to call different templates to generating the sights and the image.

The origin template has two entries:

* `probe` which is responsible for setting up some variables used throughout the message and in the twitter card, as well as generating the text produced by the algorithm
* `twitterCard` which is responsible for generating an SVG document that CBDQ turns into an image that will be attached to the tweet.

## Naming conventions

To make longer grammars more manageable it can be helpful to adopt a naming convention that groups related templates together.

This grammar uses the following rules:

* camelCase names, which seems to be the preferred default in tracery.
* a `set` prefix to indicate templates that primarily exist to set variables, rather than generate output
* a `card` prefix to indicate templates that are used to generate the SVG image
* algorithm names (e.g. `vert`, `phyt`) are used to prefix templates that are specific to the fictional algorith,s
* many templates are specific to individual probes. These have the probe named added as a suffix, e.g. `setColoursRidgway`
* generic templates used across the grammar, e.g. for numbers, basic SVG primitive, or other common content are not prefixed

## Variables

### How variables are assigned

Some brief background about how variables are assigned and used in this grammar.

Tracery has the ability to assign variables that can then be referenced in templates that are executed later.

This feature is used extensively to factor out common blocks of text into parameterised, reusable templates, e.g. for adding some text into an SVG document at different coordinates, with a different font, etc.

#### Setting variables

For example this template will end up setting two possible pairs of variables (`colourName` and `colourCode`):

```
 "setColour": ["[colourName:Snow White][colourCode:#f1e9cd]", "[colourName:Reddish White][colourCode:#f2e7cf]"]
```

We can also assign multiple variables to different values. E.g. the following sets two different colour pairs:

```
"chooseColors": ["[#setColour#][col1Name:#colourName#][col1Code:#colourCode#][#setColour#][col2Name:#colourName#][col2Code:#colourCode#]"]
```

These templates can later be used elsewhere:

```
 "example": "The name for #col1Code# is #col1Name#"
```

#### Parameter passing

By default variables have global scope so can be set and then used anywhere in the grammar. This is used to set high-level parameters like the name of the planet being surveyed.

But we can simulate function calls by passing named variables to templates, using the below syntax:

```
  "message": ["#[x:45][y:140][fill:#ffffff][size:large][font:monospace][label:My Message]cardText#"]
```

The `cardText` template can then use the `x`, `y`, `fill`, `size`, `font`, `label` parameters to generate output. Later uses of these variables will overwrite the previous values.

When setting parameters as part of calling another template we can also reference other variables allowing us to temporarily copy that value into another variable.

I've found this really helps to tidy up the grammar and encourage reuse of text.

## Choosing colours

Colour pickers use the `setColour` template to set a block of 5 colours which will be used to build up the description of the bioform and to add the colours to the twitter card.

When colours are set, we bind a series of `colourName` and `colourCode` pairs to a set of 5 variables (`col1`-`col5`).

The full list of pairings come from a separate spreadsheet. The list of possible colours are specific to individual planets.

The setting and use of colours relies heavily on using the above features to assign variables.

### List of global variables

The following are global variables that are referenced in various templates.

* `algo`
* `algoBioform`
* `ecoregion`
* `col1Code` - `col5Code`
* `col1Name` - `col5Name`
* `probeIndex`
* `probeName`
* `probePlanet`
* `surveyRegion`

## Sightings

Sightings consist of:

- a confirmation comment which references the type of bioform seen on a specific planet. This is standard, apart from the emojis used as prefixes (see `algoMatch`)
- a description of the bioform which references its colours. These are bespoke to the individual algorithms (e.g. `orniBioform`)
- an observation or comment made by the algorith, Again, these are bespoke to the algorithms (e.g. `orniComment`)

The `orni`, `phyt`, `vert`, `biof` templates generate the sightings for each algorithm.

## Twitter Cards

These are SVG images constructed from a mixture of pre-defined assets and some procedural text.

A card is made up of:

- a background image. These are predefined, one per planet. It is selected by the `probeIndex` parameter
- a "swatch" of colours that have been seen. These are the 5 colours assigned to the `col1Code` - `col5Code` variables initalised at the start
- a title and description which rely on the `probePlanet` and `probeName` variables
- the log, which is intended to look like debug output. It uses the `probePlanet`, `probeName`, `surveyRegion`, `algo` parameters, plus some procedurally generated content for confidence, flight numbers, etc.

The card content should only use generic variables that are set by previous templates. All probes and algorithm templates will need to ensure these are populated.
