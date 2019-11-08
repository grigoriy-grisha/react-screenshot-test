# react-screenshot-test

[![CircleCI](https://img.shields.io/circleci/build/github/fwouts/react-screenshot-test)](https://circleci.com/gh/fwouts/react-screenshot-test/tree/master)
[![npm](https://img.shields.io/npm/v/react-screenshot-test)](https://www.npmjs.com/package/react-screenshot-test)

This is a dead simple library to screenshot test React components.

```typescript
// my-component.spec.jsx (or .tsx)

import React from "react";
import { ReactScreenshotTest } from "../lib";
import { VIEWPORTS } from "./viewports";

ReactScreenshotTest.create("Using runner")
  .viewports(VIEWPORTS)
  .shoot("with title", <MyComponent title="Hello, World!" />)
  .shoot("without title", <MyComponent title={null} />)
  .run();
```

That's it. Well, almost!

All that's left is configuring Jest's `globalSetup` and `globalTeardown` scripts:

```json
{
  "globalSetup": "react-screenshot-test/global-setup",
  "globalTeardown": "react-screenshot-test/global-teardown"
}
```

You can then generate screenshots with `jest -u`, just like you normally do with
snapshot tests.

## What does it look like?

Here's a real example of a pull request where a component was changed:
[![Example PR](example-pr.png)](https://github.com/fwouts/react-screenshot-test/pull/12/files#diff-e61c081cbc935befbe8d3333de26ff6d)

## How does it work?

Under the hood, we start a local server which renders components server-side. Each component is given its own dedicated page (e.g. /render/my-component). Then we use Puppeteer to take a screenshot of that page.

## Cross-platform consistency

If you work on a team where developers use a different OS (e.g. Mac OS and
Linux), or if you develop on Mac OS but use Linux for continuous integration,
you would quickly run into issues where screenshots are inconsistent across
platforms. This is, for better or worse, expected behaviour.

In order to work around this issue, `react-screenshot-test` will default to
running Puppeteer (i.e. Chrome) inside Docker to take screenshots of your
components. This ensures that generated screenshots are consistent regardless of
which platform you run your tests on.

You can override this behaviour by setting the `SCREENSHOT_MODE` environment
variable to `local`, which will always use a local browser instead of Docker.

## CSS support

CSS-in-JS libraries such as Emotion and Styled Components are supported.

However, CSS imports are mocked out by Jest, which means that `react-screenshot-test` cannot support them.

| CSS technique                                          | Supported |
| ------------------------------------------------------ | --------- |
| `<div style={...}`                                     | ✅        |
| [Emotion](https://emotion.sh)                          | ✅        |
| [Styled Components](https://www.styled-components.com) | ✅        |
| `import "./style.css"`                                 | ❌        |
| `import css from "./style.css"`                        | ❌        |

## Storing image snapshots

We recommend using [Git LFS](https://git-lfs.github.com) to store image
snapshots. This will help prevent your Git repository from becoming bloated over time. See our [`.gitattributes`](.gitattributes) for an example setup.

## TypeScript support

This library is written in TypeScript. All declarations are included.
