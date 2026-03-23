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

- Goal: compatibility to all dev workstations, custom yet consistent style to our organisation
  - the default structure is more familiar for developers
- Main directories: source, output (generated JS files) and declaration folder (e.g. `xxx.d.ts`)
  - *Out-File*: single JS file with all output, for browser to load
  - *Out-Directory*: multiple output files --> allow flexibility of packaging the JS files
- Configure options:
  - `noEmit`: flag deciding if any outputs are generated
  - `declarationDir`: declaration folder (default to the source folder)
  - `outDir`: output folder (default to the source folder)
  - `outFile`: single output file
  - `rootDir`: the source folder (default: `src`)
- Configure watch settings:
  - Two watch settings `watchOptions.watchFile`, or `watchOptions.watchDirectory`
  - Can use priority/dynamic polling or File System events, but the latter is OS dependent
    - often file use polling, while directoriy use FS event
    - `dynamicPriorityPolling`: less CPU usage, but slight delay --> good for large project
  - Can use environment variable, which fallbacks to tsconfig settings
- Base configuration: sensible defaults settings
  - can stacks multiple base config --> later one overwritten early settings ==> unique custom config
  - Example: `"extends": [ "@tsconfig/recommended", "@tsconfig/vite-react" ]`

## TypeScript Configuration: Supporting JavaScript

- Usage: use of legacy code, support existing JS developers, or for fast prototyping/experimentation
  - More disadvantages in the long run --> reduce code quality
- Main option: `allowJS` --> flag to support JS
  - `checkJS`: inferring types from code ==> dependent on code quality for accurate inferrence
    - Alter. way: using declaration files

## TypeScript Configuration: Configuring Type Checking

- Main benefit of TypeScript vs. JavaScript is type checking --> want to ensure its optimally configured
  - To prevent potential error & bad practice
- Some type checking settings:
  - `noFallthroughCasesInSwitch` --> easily misleading
  - `allowUnreachableCode` --> confusing
  - `noImplicitAny` --> clarity of intent
  - `noImplicitOverride` --> clarity of intent

## Compiling TypeScript Project References

- Project refereces: allows breaking big system into smaller modules (i.e. projects)
  - Must specify every included file
  - Must employ stricter error checking
  - Must support declarations
  - project can be imported via `references` settings
- Benefits:
  - Can do incremental build (i.e. faster dev. loop), or all-in-one build (production release)
  - Can be easily turned into APIs or libraries
