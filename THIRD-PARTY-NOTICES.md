# Third-party notices — DiagramBridge 0.3.20

This document identifies third-party software and adapted material used by DiagramBridge. It is not a blanket open-source license for the entire DiagramBridge application.

The original DiagramBridge WPF application, user interface, localization, conversion orchestration, Windows engine bootstrapper, VSDX processing and validation code, product-specific graphics, and documentation are proprietary project work. See [PROPRIETARY-NOTICE.md](PROPRIETARY-NOTICE.md).

## Larry-Labs/drawio-to-visio — Apache License 2.0

Repository: https://github.com/Larry-Labs/drawio-to-visio  
License: Apache License 2.0  
License text: [licenses/Apache-2.0-Larry-Labs-Exporter.txt](licenses/Apache-2.0-Larry-Labs-Exporter.txt)

The `src/DiagramBridge/Assets/engine/export_vsdx.js` file adapts the local Playwright-driven diagrams.net VSDX export approach published in the repository above. The adapted exporter material is identified separately from the original DiagramBridge application code.

When distributing the adapted exporter material, retain the applicable Apache 2.0 license and attribution notices. If the adapted file is modified, preserve applicable upstream notices and identify the modifications as required by the license.

## Node.js

Node.js is downloaded from the official Node.js distribution host during first-time engine setup. It is installed separately on the user's computer rather than bundled into the DiagramBridge executable. Node.js has its own license and contains separately maintained third-party components. Refer to the license and third-party notices supplied by the official Node.js distribution:

https://nodejs.org/en/about/license/

https://github.com/nodejs/node/blob/main/LICENSE

## diagrams.net / draw.io

DiagramBridge downloads the pinned diagrams.net source from the official JGraph repository during first-time engine setup. It is not embedded into the DiagramBridge executable. The diagrams.net source code is licensed under Apache License 2.0, while icon sets, stencil libraries, templates, and third-party assets can have additional terms. Refer to the upstream project and its notices:

https://github.com/jgraph/drawio

https://github.com/jgraph/drawio/blob/dev/LICENSE

## Playwright

The Playwright Node package is installed separately from the npm registry during first-time engine setup and is pinned by the application to version 1.62.0. Playwright is licensed under Apache License 2.0 and includes its own notices:

https://github.com/microsoft/playwright

https://github.com/microsoft/playwright/blob/main/LICENSE

## Scope

The presence of these third-party components does not mean that the complete DiagramBridge application is licensed under Apache 2.0. Apache 2.0 applies to the identified adapted exporter material and to the upstream components that use that license. Each external component retains its own license terms.
