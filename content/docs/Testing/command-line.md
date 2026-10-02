---
title: "Command Line and CI"
weight: 3
summary: "Install chapar-cli and run test cases from a workspace or an exported file, in CI or on any machine without a screen"
---

`chapar-cli` runs the same test case files as the app, without the app. It is a single static binary for Linux, macOS and Windows on amd64 and arm64, with no GPU or window system needed, so it fits in any CI image.

## Install

On Linux and macOS:

```bash
curl -fsSL https://github.com/chapar-rest/chapar/releases/latest/download/install-cli.sh | sh
```

The script picks the archive for your machine, checks it against the release's checksums and installs `chapar-cli` into `/usr/local/bin` when that is writable, else `~/.local/bin`. Pin a version with `-v` and choose the folder with `-b`:

```bash
curl -fsSL https://github.com/chapar-rest/chapar/releases/latest/download/install-cli.sh | sh -s -- -v {{< latest-release >}} -b "$HOME/.local/bin"
```

With Homebrew:

```bash
brew install chapar-rest/chapar/chapar-cli
```

On Windows, download `chapar-cli-windows-{{< latest-release >}}-amd64.zip` (or `arm64`) from the [releases page](https://github.com/chapar-rest/chapar/releases) and put `chapar-cli.exe` on your `PATH`.

## Run test cases

There are two ways to give `chapar-cli` your tests.

**A workspace.** Commit the workspace folder to your repository (its `.state` folder, with cookies, is git-ignored) and point `--workspace` at it. Without arguments every test case in it runs, sorted by name; name cases, or pass files and folders, to run only those:

```bash
chapar-cli test --workspace api-tests --env Staging
chapar-cli test --workspace api-tests --env Staging "Todo lifecycle" "Auth checks"
chapar-cli test --workspace api-tests --tag smoke
```

`--workspace` also takes the name of a workspace in the app's data folder; without it, the app's active workspace is used, which is handy on your own machine.

**An exported file.** [Export](../test-cases#export-for-the-command-line) a test case from the app. The file holds the case, the requests it sends and optionally an environment, and runs without a workspace:

```bash
chapar-cli test todo-lifecycle.yaml
```

The bundle's environment is used unless `--env` names another. Bundles and workspace cases can't be mixed in one command.

Each step prints one line as it finishes, with failed assertions under it, and a summary at the end:

```text
$ chapar-cli test --workspace api-tests --env Production
Auth checks · env Production
  ✓ Basic auth     200  264ms
  ✓ Bearer auth    200  149ms
  ✓ API key        200  153ms
  ✓ Not found      404  220ms
  ✗ Slow response  200  1.64s
      time lt 1000: got 1644, want lt 1000
failed · 4 passed, 1 failed · 2.43s

Todo lifecycle · env Production
  ✓ Todos/Reset todos  200  147ms  (setup)
  ✓ Create todo        201  145ms
  ✓ Todos/Get todo     200  162ms
  ✓ Todos/List todos   200  2.67s
  ✓ Todos/Delete todo  204  155ms  (teardown)
passed · 5 passed · 3.28s

1 of 2 test cases passed · 5.71s
```

## Environment values

`--env` picks a workspace environment by name or ID; without it, no environment is used. Values can be set over it, in this order, each overriding the one before:

| Flag | Sets values from |
|------|------------------|
| `--env-file FILE` | A Chapar environment file, or a file of `KEY=VALUE` lines. |
| `--os-env PREFIX` | OS environment variables starting with `PREFIX`, with the prefix removed: `--os-env API_` turns `API_TOKEN` into `{{TOKEN}}`. |
| `--var key=value` | The command line. Repeat it for more values. |

Without `--env`, these values form an environment of their own. Keep secrets out of files and pass them from your CI's secret store with `--os-env`.

Secret values of a workspace environment are decrypted only when the environment has some. A key protected by a passphrase is unlocked with `CHAPAR_SECRETS_PASSPHRASE`. A secret that stays locked is left out with a warning.

## Flags

| Flag | What it does |
|------|--------------|
| `--workspace W` | Workspace folder, or the name of a workspace in the app. Default: the app's active workspace. |
| `--env E` | Environment to run with, by name or ID. Default: none. |
| `--env-file F`, `--os-env P`, `--var k=v` | Set environment values; see above. |
| `--tag T` | Run only cases with this tag. Repeat it, or separate tags with commas, to run cases with any of them. |
| `--bail` | Stop after the first test case that does not pass. |
| `--scripts` | Run request scripts even when scripting is off in the app's settings. |
| `--report junit=FILE` | Write a JUnit XML report, which most CI systems display. |
| `--report json=FILE` | Write a JSON report with every step, assertion and capture. |
| `--no-color` | Print without colors. Colors are also off when the output is not a terminal or `NO_COLOR` is set. |

Flags can come before or after the test cases. `chapar-cli test -h` prints them all.

Every case is checked before anything is sent: a case with a problem, such as an unknown operator or a step whose request is missing, stops the command.

Request scripts need the [script executor](../../scripting#setup). It starts with the first script, so runs without scripts never need it.

## Exit codes

| Code | Meaning |
|------|---------|
| `0` | Every test case passed. |
| `1` | A test case failed or had an error. |
| `2` | Bad flags, workspace, environment or test case files, or a report could not be written. |
| `130` | Interrupted. Teardown steps still run, and reports are still written. |

## GitHub Actions

```yaml
jobs:
  api-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install chapar-cli
        run: curl -fsSL https://github.com/chapar-rest/chapar/releases/latest/download/install-cli.sh | sh -s -- -b "$HOME/.local/bin"

      - name: Run API tests
        run: chapar-cli test --workspace api-tests --env Staging --os-env API_ --report junit=results.xml
        env:
          API_TOKEN: ${{ secrets.API_TOKEN }}

      - name: Publish results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: api-test-results
          path: results.xml
```

On GitHub Actions the installer adds its folder to `PATH` for the next steps. Any other CI works the same way: install the binary, run `chapar-cli test`, and read the exit code and the JUnit report.
