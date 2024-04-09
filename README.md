# Reproduction Repository for Node16 Module Resolution Bug

This repository serves as a reproduction for a bug concerning module resolution in Node16, specifically when transpiling a mixed TypeScript/JavaScript file set using Node16/NodeNext as module/moduleResolution.

## Problem Description

I expect tsc to produce __esModule transpilation marker only for modules that are NOT CommonJS as source, but CommonJS as final target. All else results in failed synthetic default import detection:

From TS file:
```
import syntheticDefault from './commonjs.cjs'

// syntheticDefault is now undefined!
```

## Steps to Reproduce

1. Clone this repository.
2. Run `npm install` to install the necessary dependencies.
3. Run `npm run build` to start the build process.
4. Check the output files in `dist/`

## Expected Behavior

All files transpiled from ESM to CJS should have a transpilation marker. No other files should have the marker.

## Actual Behavior

The full test matrix is as follows:

Expected result:

| Source | Target ESM | Target CJS |
|--------|------------|------------|
| JS-ESM |     ?      |     M      |
| JS-CJS |     -      |     -      |
| TS-ESM |     -      |     M      |
| TS-CJS |     ?      |     -      |

Actual result:

| Source | Target ESM | Target CJS |
|--------|------------|------------|
| JS-ESM |     X      |     M      |
| JS-CJS |     -      |     M      |
| TS-ESM |     -      |     M      |
| TS-CJS |     X      |     -      |

Symbols:
* - = No marker, no error
* M = Marker, no error
* X = Error (file did not transpile or causes bad transpiled code)
* ? = Should potentially be trated as error? Not sure, out of scope of this bug

## Current Workaround

Make sure to never rely of synthetic default imports in mixed ts/js node16 projects that rely on legacy CommonJS sources.

## Contact

Alexander Schrab
alexander.schrab@gmail.com