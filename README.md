# React Native DOM &middot; [![CircleCI](https://circleci.com/gh/vincentriemer/react-native-dom.svg?style=shield&circle-token=96448c580730a065cb93c0a10af0f85f6c954166)](https://circleci.com/gh/vincentriemer/react-native-dom) [![npm version](https://badge.fury.io/js/react-native-dom.svg)](https://badge.fury.io/js/react-native-dom) [![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lernajs.io/) [![All Contributors](https://img.shields.io/badge/all_contributors-9-orange.svg)](#contributors)

An experimental, comprehensive port of React Native to the web.

- **Multithreaded by default:** Following the exact same architecture as React
  Native on mobile, all of your react components/app logic are run in web
  worker, leaving the main thread to entirely focus on rendering.
- **Same layout behavior as React Native on mobile:** Powered by custom bindings
  to Yoga and compiled to WebAssembly, avoid layout inconsistencies between
  your native and web projects.
- **Built with the same bundler used for existing React Native platforms:**
  Build both the "native" main and JS threads with the Metro Bundler along with
  all the developer experience features it provides.
- **Ecosystem compatible escape hatch to the DOM:** Using the same native module
  bridge, expose DOM-specific APIs in a more generic way that can easily be made
  into a cross-platform module.

To see it in action, check out these live demos:

- [Movies Demo](https://rndom-movie-demo.now.sh)
- [RNTester (component playground used for manual testing)](https://rntester.now.sh)
- [Expanding Grid Item Demo](https://github.com/vincentriemer/rndom-expanding-grid-item-demo)

---

## Why?

For the best introduction to this project, check out
[my talk at React Europe 2018](https://youtu.be/aOWIJ4Mgb2k) introducing it.

---

## **WARNING**

This project is still highly experimental and many aspects of it are subject to
breaking changes, continue at your own risk.

---

## Getting Started

Getting your React Native project configured to use `react-native-dom` is a lot
like the process for other 3rd party platforms such as `react-native-windows`.

If you're starting a new project from scratch: ensure you have the react-native
CLI installed globally.

```
npm install -g react-native-cli
# or
yarn global add react-native-cli
```

Next, initialize your React Native project.

```
react-native init [project name]
```

Then, `cd` into your project and install `rnpm-plugin-dom` into your
`devDependencies`, after which you can initialize your React Native DOM
scaffolding with the `react-native dom` command.

```
npm install --save-dev rnpm-plugin-dom
# or
yarn add --dev rnpm-plugin-dom

# Add DOM support to your React Native project
react-native dom
```

To run your initialized project in your browser, you can either:

- Start the packager yourself using `react-native start` and navigate open your
  browser to `localhost:8081/dom`
- Leverage the built-in rnpm command `react-native run-dom` which will start the
  packager and open the browser to the correct URL for you

**NOTE:** After setting up the DOM platform you may need to run
`react-native start` with the `--reset-cache` flag at least once if you recieve
an error message like `Unable to resolve module AccessibilityInfo`.

### Overview of files generated by the RNPM plugin

- `dom/bootstrap.js` - Entry point to the main thread bundle where you can set
  runtime configuration options, register custom native modules, or any other JS
  initialization you would like to do.
- `dom/entry.js` - Entry point to the JS thread bundle, will likely only be
  importing your App's entry point from the top-level folder of your project.
- `dom/index.html` - HTML file which is what references and loads the JS
  bundles.
- (conditionally) `rn-cli.config.js` - Depending on if the project already has
  one, the rnpm plugin will either create it with the proper configuration
  options to support the DOM platform or will simply add the necessary entries
  to your existing one.

---

## React Developer Tools

You can use
[the standalone version of React Developer Tools](https://github.com/facebook/react-devtools/tree/master/packages/react-devtools)
to debug the React component hierarchy. To use it, install the react-devtools
package globally:

```
npm install -g react-devtools
```

Now run react-devtools from the terminal to launch the standalone DevTools app:

```
react-devtools
```

In order to activate the connection with the devtools app add '?devtools' to the
end of your development url (e.g. localhost:8081/dom?devtools)

## Building for Production

A built-in script for performing a production build is still in the backlog but
here is a manual script which does so (assuming the same directory structure
that gets generated from the rnpm plugin).

```shell
# Ensure development-speecific code is stripped from the bundle
export NODE_ENV=production

# Make the dist directory, or the build command below will fail.
mkdir -p ./dom/dist

# Build the main thread bundle
react-native bundle \
  --config $(pwd)/rn-cli.config.js \
  --dev false \
  --platform dom \
  --entry-file ./dom/bootstrap.js \
  --assets-dest ./dom/dist \
  --bundle-output ./dom/dist/bootstrap.bundle

# Build the JS thread bundle
react-native bundle \
  --config $(pwd)/rn-cli.config.js \
  --dev false \
  --entry-file ./dom/entry.js \
  --platform dom \
  --bundle-output ./dom/dist/entry.bundle \
  --assets-dest ./dom/dist

# Copy the index.html file to the build destination
cp dom/index.html dom/dist/index.html
```

The resulting folder in `dom/dist` will contain static HTML & JS ready to be
deployed to your provider of choice.

---

## Writing Native Modules/Views

### _Work In Progress_

The API for this is going to be overhauled soon with accompanying documentation.
If you want to see what it currently looks like take a look at some of the built
in native modules such as
[AsyncLocalStorage](../master/packages/react-native-dom/ReactDom/modules/RCTAsyncLocalStorage.js)

---

## Repository Structure

This project is a lerna-managed monorepo with all the projects living in the
`packages` folder.

### Package Overview

- `react-native-dom` - The library itself (this is most likely the package
  you're interested in).
- `rnpm-plugin-dom` - RNPM plugin primarily used for bootstrapping DOM support
  into a React Native project.
- `rndom-*` - Custom web components (built with
  [svelte](https://svelte.technology)) used for some of the built-in
  widgets/views in `react-native-dom`.

One noticeable omission to the list of packages is the custom build of Yoga
which can be found in
[this separate repo](https://github.com/vincentriemer/yoga-dom). `yoga-dom` is
not included in this monorepo due to requiring a significantly different build
environment than this repo's entirely JS codebase.

---

### Running RNTester/Examples

To run the examples located in the `react-native-dom` source, run the following
commands from the root of the monorepo:

```sh
# be sure to update the git submodules to pull the RNTester code
git submodule update --init

# install dependencies
yarn && yarn compile

# start the react-native packager
yarn run-examples
```

Then navigate to `localhost:8081/Examples` and choose which example you would
like to see.

A live deployment of the RNTester project (used primarily for manually testing
changes) can be found at [rntester.now.sh](https://rntester.now.sh)

## Contributors

Thanks goes to these wonderful people
([emoji key](https://github.com/kentcdodds/all-contributors#emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
| [<img src="https://avatars2.githubusercontent.com/u/1398555?v=4" width="100px;"/><br /><sub><b>Vincent Riemer</b></sub>](http://vincentriemer.com)<br />[💻](https://github.com/vincentriemer/react-native-dom/commits?author=vincentriemer "Code") [🐛](https://github.com/vincentriemer/react-native-dom/issues?q=author%3Avincentriemer "Bug reports") [📖](https://github.com/vincentriemer/react-native-dom/commits?author=vincentriemer "Documentation") [💡](#example-vincentriemer "Examples") [🤔](#ideas-vincentriemer "Ideas, Planning, & Feedback") [🚇](#infra-vincentriemer "Infrastructure (Hosting, Build-Tools, etc)") [📦](#platform-vincentriemer "Packaging/porting to new platform") [📢](#talk-vincentriemer "Talks") | [<img src="https://avatars0.githubusercontent.com/u/8888799?v=4" width="100px;"/><br /><sub><b>Joe Goodall</b></sub>](https://uk.linkedin.com/in/joegoodall)<br />[📖](https://github.com/vincentriemer/react-native-dom/commits?author=joegoodall1 "Documentation") | [<img src="https://avatars3.githubusercontent.com/u/31624379?v=4" width="100px;"/><br /><sub><b>François Rosato</b></sub>](https://github.com/frosato-ekino)<br />[📖](https://github.com/vincentriemer/react-native-dom/commits?author=frosato-ekino "Documentation") | [<img src="https://avatars2.githubusercontent.com/u/2246565?v=4" width="100px;"/><br /><sub><b>Moti Zilberman</b></sub>](https://github.com/motiz88)<br />[💻](https://github.com/vincentriemer/react-native-dom/commits?author=motiz88 "Code") [📖](https://github.com/vincentriemer/react-native-dom/commits?author=motiz88 "Documentation") | [<img src="https://avatars2.githubusercontent.com/u/983071?v=4" width="100px;"/><br /><sub><b>thebetterjort</b></sub>](https://github.com/thebetterjort)<br />[📖](https://github.com/vincentriemer/react-native-dom/commits?author=thebetterjort "Documentation") | [<img src="https://avatars0.githubusercontent.com/u/3089012?v=4" width="100px;"/><br /><sub><b>Bilo Lwabona</b></sub>](https://github.com/bilo-io)<br />[📖](https://github.com/vincentriemer/react-native-dom/commits?author=bilo-io "Documentation") | [<img src="https://avatars2.githubusercontent.com/u/40002855?v=4" width="100px;"/><br /><sub><b>Madhav Varshney</b></sub>](https://github.com/madhavarshney)<br />[📖](https://github.com/vincentriemer/react-native-dom/commits?author=madhavarshney "Documentation") |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [<img src="https://avatars0.githubusercontent.com/u/1106239?v=4" width="100px;"/><br /><sub><b>Eric Rozell</b></sub>](https://github.com/rozele)<br />[💻](https://github.com/vincentriemer/react-native-dom/commits?author=rozele "Code") | [<img src="https://avatars0.githubusercontent.com/u/2847127?v=4" width="100px;"/><br /><sub><b>empyrical</b></sub>](https://twitter.com/empyrical)<br />[💻](https://github.com/vincentriemer/react-native-dom/commits?author=empyrical "Code") |

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the
[all-contributors](https://github.com/kentcdodds/all-contributors)
specification. Contributions of any kind welcome!
