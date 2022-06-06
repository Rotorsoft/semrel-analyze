# semrel-analyze

GitHub action to analyze conventional commits in a mono-repo and suggest next semantic version and tag

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
          git-url: "${{ github.server_url }}/${{ github.repository }}"
          
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
