# YoWASP Toolchain for VS Code

**Run [Yosys][], [nextpnr][], ... in VS Code without installation.**

This extension runs the open source FPGA toolchain *anywhere you can run VS Code*. Windows, Linux, macOS, Chromebooks, corporate networks, even [vscode.dev][]! Add it to VS Code, wait a few minutes, and get a bitstream; simple as that.

![A short video demonstrating an inverter written in Verilog being synthesized, placed, routed, and converted to a bitstream for the iCE40 HX8K FPGA](demo.gif)

[yosys]: https://github.com/YosysHQ/yosys
[nextpnr]: https://github.com/YosysHQ/nextpnr
[vscode.dev]: https://vscode.dev

## Getting started

After adding the [extension][] to VS Code, open workspace settings as JSON and add the commands you would like to run to build your design. For example, the demo above uses:

```json
{
    "yowaspToolchain.buildCommands": [
        ["yosys", "top.v", "-p", "synth_ice40", "-o", "top.json"],
        ["nextpnr-ice40", "--hx8k", "--json", "top.json", "--asc", "top.asc"],
        ["icepack", "top.asc", "top.bin"]
    ]
}
```

Then, invoke `YoWASP Toolchain: Build...` and enjoy the output in the terminal.

[extension]: https://marketplace.visualstudio.com/items?itemName=yowasp.toolchain

## Technical details

This extension is a part of the [YoWASP project][yowasp], which provides WebAssembly-based builds of the open source FPGA toolchain for several platforms. The JavaScript packages are published under the [`@yowasp/*`][yowasp-npm] namespace on [NPM][] and delivered by [jsDelivr][] (thanks! =^_^=). The extension itself does not include any toolchain code; rather, it parses the `package.json` metadata of the toolchain packages and imports their entry points, which contain all the code necessary to run the tools.

[yowasp]: https://yowasp.org
[yowasp-npm]: https://npmjs.org/org/yowasp
[npm]: https://npmjs.org/
[jsdelivr]: https://www.jsdelivr.com/

## Configuring delivery

By default, this extension downloads the latest version of each package (which can be up to 7 days out of date due to caching) from the [jsDelivr][] network. This may not always be the preferred option (in particular, it means builds are not reproducible), so the exact versions and delivery endpoints are configurable. The default configuration is:

```json
{
    "yowaspToolchain.baseURL": "https://cdn.jsdelivr.net/npm/",
    "yowaspToolchain.bundles": [
        "@yowasp/yosys",
        "@yowasp/nextpnr-ice40",
        "@yowasp/nextpnr-ecp5"
    ]
}
```

To require the use of a specific tool version, append `@<version>` to the bundle locator, e.g. `"@yowasp/yosys@0.37.13-dev.628"`.

To use the tool bundle provided at a specific URL, use an absolute URL as the bundle locator instead, e.g. `"http://localhost:8000/yosys/"`. The extension expects to find a built, unpacked NPM package at the provided URL; in this example, it will fetch metadata from `http://localhost:8000/yosys/package.json`.

To use a different CDN, change the base URL. Bundle locators that are not absolute URLs are appended to the base URL.

## License

The YoWASP extension is distributed under the terms of the [ISC license](LICENSE.txt).