# Chrome Launcher [![NPM chrome-launcher package](https://img.shields.io/npm/v/chrome-launcher.svg)](https://npmjs.org/package/chrome-launcher)

Launch Google Chrome with ease from node.

* [Disables many Chrome services](https://github.com/GoogleChrome/lighthouse/blob/master/chrome-launcher/flags.ts) that add noise to automated scenarios
* Opens up the browser's `remote-debugging-port` on an available port
* Automagically locates a Chrome binary to launch
* Uses a fresh Chrome profile for each launch, and cleans itself up on `kill()`
* Binds `Ctrl-C` (by default) to terminate the Chrome process
* Exposes a small set of [options](#api) for configurability over these details

### Installing

```sh
yarn add chrome-launcher

# or with npm:
npm install chrome-launcher
```


## API

### `.launch([opts])`

#### Launch options

```js
{
  // (optional) remote debugging port number to use. If provided port is already busy, launch() will reject
  // Default: an available port is autoselected
  port: number;

  // (optional) Additional flags to pass to Chrome, for example: ['--headless', '--disable-gpu']
  // See all flags here: http://peter.sh/experiments/chromium-command-line-switches/
  // Do note, many flags are set by default: https://github.com/GoogleChrome/lighthouse/blob/master/chrome-launcher/flags.ts
  chromeFlags: Array<string>;

  // (optional) Close the Chrome process on `Ctrl-C`
  // Default: true
  handleSIGINT: boolean;

  // (optional) Explicit path of intended Chrome binary
  // By default, any detected Chrome Canary or Chrome (stable) will be launched
  chromePath: string;

  // (optional) Chrome profile path to use
  // By default, a fresh Chrome profile will be created
  userDataDir: string;

  // (optional) Starting URL to open the browser with
  // Default: `about:blank`
  startingUrl: string;

  // (optional) Logging level: verbose, info, error, silent
  // Default: 'info'
  logLevel: string;
};
```

#### Launched chrome interface

#### `.launch().then(chrome => ...`

```js
// The remote debugging port exposed by the launched chrome
chrome.port: number;

// Method kill Chrome (and cleanup the profile folder)
chrome.kill: () => Promise<{}>;

// The process id
chrome.pid: number;
```


## Examples

#### Launching chrome:

```js
const chromeLauncher = require('chrome-launcher');

chromeLauncher.launch({
  startingUrl: 'https://google.com'
}).then(chrome => {
  console.log(`Chrome debugging port running on ${chrome.port}`);
});
```


#### Launching headless chrome:

```js
const chromeLauncher = require('chrome-launcher');

chromeLauncher.launch({
  startingUrl: 'https://google.com',
  chromeFlags: ['--headless', '--disable-gpu']
}).then(chrome => {
  console.log(`Chrome debugging port running on ${chrome.port}`);
});
```

### Continuous Integration

In a CI environment like Travis, Chrome may not be installed. If you want to use `chrome-launcher`, you can install Chrome using Lighthouse's `download-chrome.sh` script:

`curl -L https://raw.githubusercontent.com/GoogleChrome/lighthouse/v2.1.0/lighthouse-core/scripts/download-chrome.sh | bash`

Then in `.travis.yml`, use it like so:

```yaml
language: node_js
install:
  - yarn install
before_script:
  - export DISPLAY=:99.0
  - export LIGHTHOUSE_CHROMIUM_PATH="$(pwd)/chrome-linux/chrome"
  - sh -e /etc/init.d/xvfb start
  - curl -L https://raw.githubusercontent.com/GoogleChrome/lighthouse/v2.1.0/lighthouse-core/scripts/download-chrome.sh | bash
```
