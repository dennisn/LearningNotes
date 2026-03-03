# Modern NPM script for NodeJS
  - Pluralsight course (https://app.pluralsight.com/ilx/video-courses/automating-nodejs-modern-npm-scripts/course-overview)
    + Demo + best practics samples

Notes:
  - script hooks (i.e. 'pre/postbuild'): run before & after build
  - environment variable in script: `.development.env`
  - script unning cross environment: `"build": "cross-env NODE-ENV=development npm run build:ts && npm run build:xxx"`
  - running script concurrently: `"dev": "concurrently \"npm run build:ts\" \"npm run build:xxx\""`
  - **Lint**:
    ```
    "lint": "eslint --cache --ext .ts src/",
    "lint:fix": "eslint --cache --ext .ts src/ --fix",
    ```
  - **Code format**:
    ```
    "format": "prettier --write \"src/**/*.ts\"",
    "check-format": "prettier --check \"src/**/*.ts\"",
    ```
==> combine lint & format
    ```
    "validate": "npm run lint && npm run check-format"
    ```
  - Best practice: for code standard, install eslint, prettier, eslint-config-prettier, husky & lint-staged together to avoid unecessary conflict
    ```
    npm install --save-dev \
      eslint \
      prettier \
      eslint-config-prettier \
      husky \
      lint-staged
    ```

# Containerization & Environment Automation
  - Shipping code + dependency, but re-use kernel --> reduce size, yet maintain consistency between environments
  - Multi-stage dockerfiles: separated "build" image vs. "run" image
    + "Run" image can have the minimal set of dependencies
  - Docker compose: combined & coordinate multiple services, in different containers, together into an application
  - Docker build context: context for docker to run its build
    + Include all the files/folders in the work dir --> filter unused files/folders in `.dockerignore` file to spped up docker build
    + `node_modules`: should be the 1st candidate for `.dockerignore`

# Scaling to Monorepos and CI/CD
  - Monorepos: house multiple applications & libraries in the same directory
    + Simplify dependency management, easire refactoring & consistent dev experience --> improve collaboration
    + Sample:
      - In root package.json: `"workspaces": [ "packages/*", "apps/*"]`
      ```
      my-monorepo/
       +- package.json (root)
       +- packages/
           +- ui-components/
               +- package.json
           +- utils/
               +- package.json
       +- apps/
           +- web-app/
               +- package.json
           +- api-server/
               +- package.json
      ```
# Editor integration & Development containers
  - In Visual Studio Code, there is Dev Containers extension that can run IDE within a container for development