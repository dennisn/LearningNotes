# JavaScript Big picture

- Pluralsight course: https://app.pluralsight.com/ilx/video-courses/javascript-big-picture/course-overview

## Why JavaScript

- JavaScript: differ to Java
- Characteristics:
  - Dynamic programming language: not need to define type for variable
  - Interpreted language: run in engine(s) that translate instructions on the go
  - Script: used to be run "only" in browser (i.e. with fixed browser context) --> no longer the case

## Where is JS used ?

- In the browser: allow user-interaction with static page (html+css) --> resulted in web application
  - React (by Meta), Angular (by Google), Vue.js and Svelte --> still based on HTML+CSS+JavaScript
- On the server
  - Node.js: JS environment, which run JS inside the V8 engine, with libraries to do things normally can't within the browser (e.g. IO, network, encrypt/decrypt data)
- Full stacks: as JS can be used both in browser & on server --> there are JS frameworks that allows development of both front/back-end in one project (e.g. Next.js)
- Extra use-cases
  - Desktop apps for both Windows & Mac (e.g. Electron)
  - Mobile apps for both iOS & Android (e.g. React Native)
  - IoT such as Raspberry Pi support Node.js with some libraries to interact with different sensors & input devices
  - Configure & deploy cloud infra-structure

## How is JS versioned ?

- Who governs JavaScript ?
  - Largely by ECMA international (ak European Computer Manufactures Association) --> standard called ECMAScript --> started to be replaced with JavaScript
  - The committee within ECMA: TC-39 (i.e. Technical Committee 39)
  - Only cover the core JavaScript (e.g. the one run on browser)
  - Node.js (a big influence outside the core JS): governed by a technical steering committee & a community committe
- JavaScript releases --> are fully backward compatible
  - ES1-ES3: basic specs
  - ES4: big fight for future of JS --> never finalised
  - ES5: many new capability --> finalised in 2009, but fully implemented from 2013
  - ES6: Important change, modern foundation from 2015 --> change to yearly release
  - ES7: add async/await
- Extending JavaScript
  - Polyfill: code fragment used to provide modern functionality on older browsers that do not natively support it, at the cost of performance
    - Popular polyfill libraries: core-js
  - Transpiler: translator to convert JS code to be compatible with older specs
    - TypeScript compiler
    - Most popular "transpiler" is Babel: can also convert TypeScript to JavaScript
