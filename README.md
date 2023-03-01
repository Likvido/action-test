# For running all unit tests in a solution

```
jobs:
  testrunner:
    runs-on: ubuntu-latest

    steps:
      - name: Run tests
        uses: likvido/action-test@v1
```

You can optionally specify some parameters as well:

```
jobs:
  testrunner:
    runs-on: ubuntu-latest

    steps:
      - name: Run tests
        uses: likvido/action-test@v1
        with:
          dotnet-version: 7.x
          test-target: src/MySolution.sln
```

# Releasing new version

Either create the new release + new version tag directly in the Github UI, or create it like this:

1. Commit and push your changes
2. Create a new tag: `git tag -a -m "Description of this release" <version>`
3. Push the tag: `git push --follow-tags`

## Example

```
git tag -a -m "First version" v1
git push --follow-tags
```
