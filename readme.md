# Dependabot

## Manually Trigger

Go to Repo -> `Insights` -> `Dependency Graph` -> `Dependabot` -> Trigger dpendabot

## Configuration

[Documentation Link](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file#allow)

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

#### package-ecosystem (Required)

You add one package-ecosystem element for each package manager that you want Dependabot to monitor for new versions.

Supported package managers :

- npm
- pip
- maven
- terraform
- nuget
- gradle
- gitsubmodule
- elm
- mix
- github actions

##### Example

```yml
# Basic set up for three package managers

version: 2
updates:
  # Maintain dependencies for GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"

  # Maintain dependencies for npm
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"

  # Maintain dependencies for Composer
  - package-ecosystem: "composer"
    directory: "/"
    schedule:
      interval: "weekly"
```

#### Directory (Required)

You must define the location of the package manifests for each package manager.

Example

```yml
# Specify location of manifest files for each package manager

version: 2
updates:
  - package-ecosystem: "composer"
    # Files stored in repository root
    directory: "/"
    schedule:
      interval: "weekly"

  - package-ecosystem: "npm"
    # Files stored in `app` directory
    directory: "/app"
    schedule:
      interval: "weekly"

  - package-ecosystem: "github-actions"
    # Workflow files stored in the
    # default location of `.github/workflows`
    directory: "/"
    schedule:
      interval: "weekly"
```

#### schedule.interval (Required)

Defines how often to check for new versions for each package manager.

Options :

- daily : Runs on every weekday, Monday to Friday.
- weekly : Runs once each week. By default, this is on Monday. To modify this, use schedule.day.
- monthly : Runs once each month. This is on the first day of the month.

```yml
# Set update schedule for each package manager

version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      # Check for updates to GitHub Actions every weekday
      interval: "daily"

  - package-ecosystem: "composer"
    directory: "/"
    schedule:
      # Check for updates managed by Composer once a week
      interval: "weekly"
```

##### Weekly

When you set a weekly update schedule, by default, Dependabot checks for new versions on Monday at a random set time for the repository. Use schedule.day to specify an alternative day to check for updates.

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      # Check for npm updates on Sundays
      day: "sunday"
```

##### Time

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      # Check for npm updates at 9am UTC
      time: "09:00"
```

##### Timezone

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      time: "09:00"
      # Use Japan Standard Time (UTC +09:00)
      timezone: "Asia/Tokyo"
```

[List of timezone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

#### Allow

Use the allow option to customize which dependencies are updated. This applies to both version and security updates. By default all dependencies that are explicitly defined in a manifest are kept up to date by Dependabot version updates.

Parameters:

- `dependency-name` — use to allow updates for dependencies with matching names, optionally using \* to match zero or more characters.
- `dependency-type` — use to allow updates for dependencies of specific types.

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    allow:
      # Allow updates for Lodash
      - dependency-name: "lodash"
      # Allow updates for React and any packages starting "react"
      - dependency-name: "react*"

  - package-ecosystem: "composer"
    directory: "/"
    schedule:
      interval: "weekly"
    allow:
      # Allow both direct and indirect updates for all packages
      - dependency-type: "all"

  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
    allow:
      # Allow only direct updates for
      # Django and any packages starting "django"
      - dependency-name: "django*"
        dependency-type: "direct"
      # Allow only production updates for Sphinx
      - dependency-name: "sphinx"
        dependency-type: "production"
```

#### assignees

Use assignees to specify individual assignees for all pull requests raised for a package manager.

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    # Add assignees
    assignees:
      - "octocat"
```

#### commit-message

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    commit-message:
      # Prefix all commit messages with "npm: "
      prefix: "npm"

  - package-ecosystem: "docker"
    directory: "/"
    schedule:
      interval: "weekly"
    commit-message:
      # Prefix all commit messages with "[docker] " (no colon, but a trailing whitespace)
      prefix: "[docker] "
```

#### ignore

By default all dependencies that are explicitly defined in a manifest are kept up to date by Dependabot version updates. So a dependency that is matched by both an allow and an ignore will be ignored.

Options:

- dependency-name: use to ignore updates for dependencies with matching names, optionally using \* to match zero or more characters.
- versions: use to ignore specific versions or ranges of versions. If
- update-types: use to ignore types of updates, such as semver major, minor, or patch updates on version updates

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    ignore:
      - dependency-name: "express"
        # For Express, ignore all updates for version 4 and 5
        versions: ["4.x", "5.x"]
        # For Lodash, ignore all updates
      - dependency-name: "lodash"
        # For AWS SDK, ignore all patch updates
      - dependency-name: "aws-sdk"
        update-types: ["version-update:semver-patch"]
```

#### labels

By default, Dependabot raises all pull requests with the dependencies label. If more than one package manager is defined, Dependabot includes an additional label on each pull request.

Use labels to override the default labels and specify alternative labels for all pull requests raised for a package manager.

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    # Specify labels for npm pull requests
    labels:
      - "npm"
      - "dependencies"
```

#### open-pull-requests-limit

By default, Dependabot opens a maximum of five pull requests for version updates. Use open-pull-requests-limit to change this limit.

```yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    # Disable version updates for npm dependencies
    open-pull-requests-limit: 0

  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
    # Allow up to 10 open pull requests for pip dependencies
    open-pull-requests-limit: 10
```
