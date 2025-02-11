# Support rollup.config.ts in NX

## Convert to TS project as a prerequisite

- Set up TS project linking like is described [here](https://nx.dev/concepts/typescript-project-linking#typescript-project-linking)
- NX has a special logic which analyses tsconfig and tsconfig.base and determines is it's properly set up to use a new logic
- If all done right - then you may see that `nx g @nx/js` will generate quite a different set up

## Key differences

- project.json will be of type module
- tsconfig.lib.json now uses bundler
- rollup config is much simpler
- build is made right in a package/dist folder
- publishing is happening from also from same folder
- this helps to avoid copying files around (like Readme and other assets)
- package.json is also not copied now and source package.json is used for publishing

## How to use rollup.config.ts?

Original file rollup.config.cjs looks like this

```js
const { withNx } = require("@nx/rollup/with-nx");

module.exports = withNx(
  {
    main: "./src/index.ts",
    outputPath: "./dist",
    tsConfig: "./tsconfig.lib.json",
    compiler: "swc",
    format: ["esm"],
  },
  {
    // Provide additional rollup configuration here. See: https://rollupjs.org/configuration-options
    // e.g.
    // output: { sourcemap: true },
  }
);
```

Now let's convert it to TS

```typescript
import { withNx } from "@nx/rollup/with-nx.js";

export default withNx(
  {
    main: "./src/index.ts",
    outputPath: "./dist",
    tsConfig: "./tsconfig.lib.json",
    compiler: "swc",
    format: ["esm"],
  },
  {
    // Provide additional rollup configuration here. See: https://rollupjs.org/configuration-options
    // e.g.
    // output: { sourcemap: true },
  }
);
```

## Does it work?

Unfortunately, not out of the box yet.

When running a code we have the error like:

```
npx nx build sample

 NX   Failed to process project graph.

An error occurred while processing files for the @nx/rollup/plugin plugin.
  - sample/rollup.config.cts: Unknown file extension ".cts" for /workspaces/nx-rollup-ts-config/sample/rollup.config.cts
```

## How to fix it?

Currently, the fix is to use this [patch](patches/@nx+rollup+20.4.2.patch).

There will be PR submitted too
