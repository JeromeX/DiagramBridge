# Third-party notices

## Larry-Labs/drawio-to-visio

Repository: https://github.com/Larry-Labs/drawio-to-visio
License: Apache License 2.0

The `src/DiagramBridge/Assets/engine/export_vsdx.js` file adapts the local Playwright-driven diagrams.net export approach published in the repository above. The user interface, Windows bootstrapper, conversion orchestration and VSDX package validator in DiagramBridge are separate project code.

The upstream repository pins diagrams.net 26.0.16 and Playwright 1.62.0 and documents that the VSDX export route is beta and that structural validation does not prove pixel-perfect rendering. See its README and validation notes before using the converter for critical files.

## Node.js

Node.js is downloaded from the official Node.js distribution host at first run. Node.js is distributed under its own license; see https://nodejs.org/en/about/license/.

## diagrams.net / draw.io

The diagrams.net source is downloaded from the official jgraph/drawio repository at a fixed commit. It is not bundled into the DiagramBridge application binary. See https://github.com/jgraph/drawio for its license and notices.

## Playwright

Playwright is installed from the NPM registry at first run, pinned to version 1.62.0. See https://github.com/microsoft/playwright for license and notices.

## Apache License 2.0

The adapted exporter portion is distributed under the Apache License, Version 2.0. A copy of the license is provided in `LICENSE`.