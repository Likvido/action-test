# For running all unit tests in a solution

v2 runs `dotnet test` in [Microsoft.Testing.Platform](https://learn.microsoft.com/dotnet/core/testing/microsoft-testing-platform-intro) (MTP) mode. v1 runs it in VSTest mode; `v1.5` stays available for repositories that have not moved yet.

## Requirements

- A `global.json` in the repository root that turns on the MTP mode of `dotnet test` (.NET 10 SDK or later). The action fails with an error saying so when this is missing.

  ```json
  {
    "test": {
      "runner": "Microsoft.Testing.Platform"
    }
  }
  ```

- Test projects that run on MTP v2, for example `xunit.v3` 4.x with `<OutputType>Exe</OutputType>`. `xunit.v3` 3.x is built on MTP v1, which `GitHubActionsTestLogger` 3.x does not support.
- The `GitHubActionsTestLogger` NuGet package (3.x) in each test project, for failed tests to show as annotations and in the job summary. It turns itself on when it runs in GitHub Actions, so no command-line option is needed.

## Usage

```yaml
jobs:
  testrunner:
    runs-on: ubuntu-latest

    steps:
      - name: Setup .NET
        uses: actions/setup-dotnet@v6
        with:
          dotnet-version: 10.x

      - name: Run tests
        uses: likvido/action-test@v2.0
        with:
          test-target: src/MySolution.sln
```

The action does not install the .NET SDK. It checks out the repository itself and runs `dotnet test --configuration Release --results-directory TestResults`. It then uploads `TestResults` as the `test-results` artifact when the test run wrote anything there; a test project with only the packages above writes nothing there, so no artifact is uploaded.

## Inputs

| Input | Description |
|---|---|
| `test-target` | A solution (`.sln`, `.slnx`, `.slnf`), passed as `--solution`, or a project file or a directory containing one, passed as `--project`. Default is empty, which tests the solution or project in the repository root. |
| `framework` | The target framework to test, passed as `--framework`, such as `net10.0`. Default is empty, which tests every target framework of the test projects. |

The `logger` input of v1 is gone.

# Releasing new version

1. Merge the change into `main`.
2. Tag the merge commit with the next `vX.Y` version and push the tag: `git tag v2.1 <merge commit> && git push origin v2.1`.
3. Create a GitHub release for the tag, with notes that say what changed.

Never move an existing tag: a repository pinned to it would silently run different code.
