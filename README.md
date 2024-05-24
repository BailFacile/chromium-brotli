<h1 align="center"> BailFacile Chromium Brotli files <h1>
<div align="center">

<p>
  
  ![Last Release](https://img.shields.io/github/v/release/BailFacile/chromium-brotli.svg)
  
</p>

</div>

## About this repository

This is where we save a copy of the Brotli files for the [Sparticuz/Chromium](https://github.com/Sparticuz/chromium) release package. These files are used in our Lambda CallChrome function, and keeping them in one of our organisation's repositories gives us certain security.

## Usage

In our Lambda function, Chromium requires an `executablePath()` to work properly. This path must point to Chromium's brotli files in order to use the necessary binary. There are several ways of reaching these files:

- Pointer to an internal Lambda file (using a layer)
- Download files from an S3 bucket
- Download from an external source

In our case, we decided to download them from an external source for reasons of simplicity and maintainability. This repository therefore serves as the external source.

The lamdba function must therefore retrieve the files from the repository's releases :

```js

const chromium = require('@sparticuz/chromium');
const puppeteer = require('puppeteer-core');

global.ReadableStream = require('web-streams-polyfill').ReadableStream;

browser = await puppeteer.launch({
  executablePath: await chromium.executablePath(
    'https://github.com/BailFacile/chromium-brotli/releases/download/vXXX.X.X/chromium-vXXX.X.X-pack.tar',
  ),
  args: [...chromium.args, ...(event.options.args || [])],
});

```

## Maintenance

When a new [release](https://github.com/Sparticuz/chromium/releases) of [Chromium](https://github.com/Sparticuz/chromium) is available and we want to use it. We first need to update the package itself in the [package.json](https://github.com/BailFacile/php-app/blob/master/resources/lambda/call-chrome/package.json) of the Lambda function.

Once this has been done, it is essential to use the correct brotli files within the function to avoid any conflicts. You will therefore need to download the `chromium-vXXX.X.X-pack.tar` file into the assets of the current Chromium release and then create a new release with the same number in this repository, including the `tar` file as an asset. Once this has been done, change the download path from `exectutablePath()` to the correct [chromium-brotli](https://github.com/BailFacile/chromium-brotli/releases) release.
