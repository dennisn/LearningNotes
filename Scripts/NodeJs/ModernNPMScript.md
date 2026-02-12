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