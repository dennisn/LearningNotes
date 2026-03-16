# Modern NPM Scripts for Node.js

- Pluralsight course: <https://app.pluralsight.com/ilx/video-courses/automating-nodejs-modern-npm-scripts/course-overview>
- Includes demos and best-practice samples.

## Notes

- Script hooks (for example, `prebuild` and `postbuild`) run before and after `build`.
- Environment variables in scripts can use files like `.development.env`.
- Run scripts across environments:

```json
"build": "cross-env NODE-ENV=development npm run build:ts && npm run build:xxx"
```

- Run scripts concurrently:

```json
"dev": "concurrently \"npm run build:ts\" \"npm run build:xxx\""
```

- Lint scripts:

```json
"lint": "eslint --cache --ext .ts src/",
"lint:fix": "eslint --cache --ext .ts src/ --fix"
```

- Formatting scripts:

```json
"format": "prettier --write \"src/**/*.ts\"",
"check-format": "prettier --check \"src/**/*.ts\""
```

- Combine lint and format checks:

```json
"validate": "npm run lint && npm run check-format"
```

- Best practice: for code standards, install `eslint`, `prettier`, `eslint-config-prettier`, `husky`, and `lint-staged` together to avoid unnecessary conflicts.

```bash
npm install --save-dev \
  eslint \
  prettier \
  eslint-config-prettier \
  husky \
  lint-staged
```

## Containerization and Environment Automation

- Ship code and dependencies while reusing the host kernel to reduce image size and maintain consistency between environments.
- Multi-stage Dockerfiles separate `build` image and `run` image.
- The `run` image can contain only the minimum dependencies.
- Docker Compose combines and coordinates multiple services in separate containers as one application.
- Docker build context is the set of files/folders Docker can access during build.
- Include needed files/folders from the working directory, and exclude unused ones with `.dockerignore` to speed up Docker builds.
- `node_modules` should be one of the first entries in `.dockerignore`.

## Scaling to Monorepos and CI/CD

- Monorepos keep multiple applications and libraries in one repository.
- Benefits: simplified dependency management, easier refactoring, and more consistent developer experience for better collaboration.
- Example workspace config in root `package.json`:

```json
"workspaces": ["packages/*", "apps/*"]
```

```text
my-monorepo/
+- package.json (root)
+- packages/
   +- ui-components/
   |  +- package.json
   +- utils/
      +- package.json
+- apps/
   +- web-app/
   |  +- package.json
   +- api-server/
      +- package.json
```

## Editor Integration and Development Containers

- In Visual Studio Code, the Dev Containers extension lets you run the IDE inside a development container.
