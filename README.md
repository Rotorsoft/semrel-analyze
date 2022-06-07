# semrel-analyze

GitHub action to analyze conventional commits in a mono-repo and suggest next semantic version and tag

## Rules

We use the following subset of conventional commit rules to decide when to bump the semantic version:

* All rules are based on the commit short message and not the body

* MAJOR: When the commit message (after prefix:) contains either `BREAKING CHANGE` or `BREAKING CHANGES`

* MINOR: When the commit message prefix is `feat:`

* PATCH: When the commit message prefix is `fix:` or `perf:` or `refactor:`

The semantic analysis looks at **all semantic commits meeting the previous rules** that ocurred **after the latest semantic tag** for the given package

Our semantic tags have this fixed format: `PACKAGE-vSEMVER`

## Usage

This action was created to tackle Yarn2 mono-repos where individual workspace releases are required. It can also be used with regular repos in the basic format below.

### Basic

**Make sure both `package` and `directory` input arguments are the name of the repo**

```yaml
name: ci-cd

on: [push]

jobs:
  cd:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
          
      - id: analyze
        uses: rotorsoft/semrel-analyze@v1
        with:
          package: 'chatbot'
          directory: 'chatbot'
          
      - name: analysis
        run: |
          echo "last-tag: ${{ steps.analyze.outputs.last-tag }}"
          echo "next-tag: ${{ steps.analyze.outputs.next-tag }}"
          echo "next-version: ${{ steps.analyze.outputs.next-version }}"
          echo "${{ steps.analyze.outputs.change-log }}"
```

### Mono-Repo

```yaml
name: ci-cd

on: [push]

jobs:
  cd:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        workspace:
          - lib1
          - lib2
          - service

    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
          
      - id: analyze
        uses: rotorsoft/semrel-analyze@v1
        with:
          package: "@scope/${{ matrix.workspace }}"
          directory: "workspace/${{ matrix.workspace }}"
          
      - name: analysis
        run: |
          echo "last-tag: ${{ steps.analyze.outputs.last-tag }}"
          echo "next-tag: ${{ steps.analyze.outputs.next-tag }}"
          echo "next-version: ${{ steps.analyze.outputs.next-version }}"
          echo "${{ steps.analyze.outputs.change-log }}"
```
