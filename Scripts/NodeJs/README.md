# NodeJS

## Big pictures
  - Node.js is the runtime environment for javascript (i.e. replace the browser as the running container) --> allow javascript to run on desktop, interact with OS, etc.
  - It has an extensive libraries of components, updated continuously
  - Characteristics: light-weight, single threads but is event-driven ==> can handle concurrent tasks in pseudo-parallel
  - `npm`: package management system (i.e. registry, commandline tools to install & manage packages), run node
    + `npm audit`: check for security problems with installed packages
    + `npm update`: update installed package (normally will keep the same major version)
    + `npm audit fix`: specifically fix security problems by pulling patched version