# he-demo-yaml (Repo 2: YAML only)

This repo contains no source code and no `pom.xml`. Each YAML file gets its code from `he-demo-source` through `sourcePayload`.

| File | Source repo host |
|---|---|
| `hyperexecuteYamlFiles/demo-github.yaml` | GitHub |
| `hyperexecuteYamlFiles/demo-bitbucket.yaml` | Bitbucket (same keys as RACV's setup) |

## Setup

1. Push `he-demo-source` to GitHub and/or Bitbucket. It can be private.
2. Push this repo to the same host.
3. Replace the placeholders `<github_org>`, `<bitbucket_username>` and `<workspace>` in the YAML files.
4. Add the git token to the HyperExecute vault as `GIT_PAT` (GitHub) or `BITBUCKET_TOKEN` (Bitbucket).

## Test matrix

Run every case with the same YAML and change only how the job is triggered.

| # | Trigger | How |
|---|---|---|
| A | CLI | `./hyperexecute --user $LT_USERNAME --key $LT_ACCESS_KEY --config hyperexecuteYamlFiles/demo-github.yaml` |
| B | Project | Create a project with App Git URL = **this YAML repo**, YAML path = `hyperexecuteYamlFiles/demo-github.yaml`, then trigger it from the dashboard |
| C | Project | Same as B, but with `demo-bitbucket.yaml` and both repos on Bitbucket (exact RACV shape) |

## Reading the result

Open the job and check the output of the first pre step, `pwd && ls -la`.

- **`pom.xml` and `src/` are listed:** the run cloned `he-demo-source`, so `sourcePayload` worked.
- **Only `hyperexecuteYamlFiles/` and this README are listed:** the run cloned the YAML repo and ignored `sourcePayload`. The next step, `mvn clean install`, then fails with "no POM in this directory". That is the RACV issue reproduced (jobs `e65925bb`, `2a287b8c`).

If A passes and B or C fails, escalate to engineering. The report: project-triggered runs ignore `sourcePayload` and clone the project's App Git URL instead.
