# Dependabot

## Configuration

In order to configure your dependabot, you need to create `dependabot.yml` under `.github` folder of the root directory. 

`dependabot.yml` includes two top-level keys: `version` and `updates`. You can optionally include `registries` key.

The file must start with `version: 2`.

### Update

Update **must** include:
- package ecosystem : Package manager to use
- directory: Location of package manifests
- schedule.interval: How often to check for updates

Options to control which dependencies to update: 
- allow : Customize which updates are allowed
- ignore : Ignore certain dependencies or versions
- vendor : Update vendored or cached dependencies

Options to add metadata to pull requests: 
- reviewers : Reviewers to set on pull requests
- assignees : Assignees to set on pull requests
- labels : Labels to set on pull requests
- milestone : Milestone to set on pull requests

Options to change the behavior of the pull requests:
- target-branch: 
- versioning-strategy:
- commit-message:
- rebase-strategy:
- pull-request-branch-name.separator: 

Options to change the maximum pull request limit:
- `open-pull-requests-limit` : changes the maximum number of pull requests for version updates that Dependabot can open.