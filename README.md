actiondoc
====

[![Tests](https://github.com/aisrael/actiondoc/actions/workflows/tests.yml/badge.svg)](https://github.com/aisrael/actiondoc/actions/workflows/tests.yml)

Generates documentation from your GitHub Action's `action.yml`

## Inputs

| Name               | Description                                                                             | Required | Default |
|--------------------|-----------------------------------------------------------------------------------------|----------|---------|
| template-filename  | The path to the ERB template to use to generate the documentation.                      | No       |         |
| output-filename    | The filename to save the output to. If not specified, simply prints to standard output. | No       |         |
| path-to-action-yml | The path to the `action.yml` file to generate documentation for.                        | No       |         |
| working-directory  | The directory to perform the action in, if not $GITHUB_WORKSPACE                        | No       |         |

See [Basic Usage](features/basic_usage.feature) for some basic usage of the gem.

### Example

Given an `action.yml` that contains:

```yaml
name: actiondoc
  description: >-
    Generates documentation from your GitHub Action's `action.yml`

  inputs:
    path-to-action-yml:
      description: >-
        The path to the `action.yml` file
        required: false
```

Then when running `actiondoc` it should output

```markdown
actiondoc
====

Generates documentation from your GitHub Action's `action.yml`

## Inputs

| Name               | Description                       | Required | Default |
|--------------------|-----------------------------------|----------|---------|
| path-to-action-yml | The path to the `action.yml` file | No       |         |
```

## Use it in a workflow to update your action's README

The following sample workflow uses this repository as a GitHub Action directly, and creates a PR (using
[peter-evans/create-pull-request](https://github.com/peter-evans/create-pull-request)) to
update `README.md` on any changes to either `action.yml` or `README.md.erb`.


```yaml
name: Generate README
on:
  push:
    paths:
      - action.yml
      - README.md.erb

jobs:
  generate-readme:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: if [ -f README.md ]; then rm README.md; fi
      - name: Generate README
        uses: aisrael/actiondoc@v1
        with:
          template-filename: README.md.erb
          output-filename: README.md
      - run: |
          cat README.md
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v4
        with:
          add-paths: README.md
          base: main
          branch: update-readme
          delete-branch: true
          title: Update README.md
```
