# GitHub action
  - Sample repository: https://github.com/a-a-ron/github-actions-big-picture

## Basic structure:
  - Create the workflow as YAML file in the ".github/workflows" directory, or through "Actions" tab on github.com website
Example
```
name: learn-github-actions
on: [push]
jobs:
  Checks-bats-version:
    runs-on: ubentu-latest
    steps:
      - name: check out repository code
        uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm install -g bats
      - run: bats -v
```