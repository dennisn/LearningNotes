# Configuring and Compiling TypeScript 5 Projects

- Pluralsight course: <https://app.pluralsight.com/ilx/video-courses/typescript-5-projects-configuring-compiling/course-overview>

## Introduction

- Need to compile to JS --> supported by all web browsers
- TypeScript enables "typed" development experience, but for web applications
- Benefits of correct configuration:
  - Prevent coding error (with correct degrees of strictness)
  - Optimize load speed & compatibility with browsers
  - Maximise benefits of type-checking & modularize projects
- Main tools: `tsc` and `tsconfig`
  - `tsconfig`: all options affecting a TS project in JSON --> indicate a TypeScript project when folder has this file
    - Default file can be generated with `tsc --init`
  - `tsc`: TypeScript compiler to turn TS into JS code
    - This is often installed by node as dev dependency: `npm install typescript -D`

## Compiling TypeScript 5 Applications

- Real world TS application often has
  - Multiple TS source file compile to one JS file --> efficient loading in browser
  - Include view engine (e.g. React, Angular, etc.): render & update view
  - Import non-TS files: CSS, JSON, JS, React resources, etc.
- Compiling On-Demand: essential for dev, yet customizable
  - need "noEmit" set to false first
  - Command: `tsc --watch`
  - Using Vite: automatic include code watch, also load the browser page when changes

### Key steps for TypeScript setup

```Bash
# init project
npm init

# Install TypeScript as Dev dependency
npm install typescript -D

# Install type definition from Node.js (optional)
npm install @types/node @types/express -D

# Create TS configuration file
npx tsc --init
```

## Configuring Application Structure and Watch Settings

## TypeScript Configuration: Supporting JavaScript

## TypeScript Configuration: Configuring Type Checking

## Compiling TypeScript Project References
