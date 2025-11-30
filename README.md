# 🚀 GitFlow Actions – Ross Robotics

Reusable GitHub Actions that implement GitFlow operations across multiple repositories.

This repo provides standardized, safe automation for creating and finishing:

- **Release branches**
- **Hotfix branches**
- **Release merges into main and devel**
- **Hotfix merges into main and devel**
- **Semantic version tagging**
- **Optional branch cleanup**

These actions allow any Ross Robotics repository to use a consistent GitFlow lifecycle, powered entirely by GitHub Actions.

# 📁 Repository Structure

```bash
gitflow-actions/
│
├─ create-release/      # Create release/x.y.z from devel
│   └─ action.yml
│
├─ create-hotfix/       # Create hotfix/x.y.z from main
│   └─ action.yml
│
├─ finish-release/      # Merge release → main & devel + tag + delete branch
│   └─ action.yml
│
├─ finish-hotfix/       # Merge hotfix → main & devel + tag + delete branch
│   └─ action.yml
│
└─ .github/workflows/
    └─ test.yml         # Linting & workflow validation for this repo
```

All actions are composite actions and can be reused in any repository via:

```
uses: ross-robotics/gitflow-actions/<action-folder>@v1
```

#📦 Installation / Usage

These actions are intended to be called from other repositories.
To use any action, add it to a workflow:

```yml
jobs:
  finish-release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: ross-robotics/gitflow-actions/finish-release@v1
        with:
          version: "1.4.0"
          release_branch: "release/1.4.0"

```

# 🛠 Available Actions


## 1. Create Release Branch

📂 `create-release/`

Creates a branch:

```pgsql
release/<version>
```

from the develop branch (`devel` by default).

Example

```yml
steps:
  - uses: ross-robotics/gitflow-actions/create-release@v1
    with:
      version: "1.4.0"
```

Equivalent to:

```bash
git checkout devel
git checkout -b release/1.4.0
git push origin release/1.4.0
```

## 2. Finish Release

📂 `finish-release/`

Merges a release branch into:

`main` (production)

`devel` (integration)

Creates git tag:
```pgsql
v<version>
```

Deletes the release branch (optional).

Example

```yml
steps:
  - uses: ross-robotics/gitflow-actions/finish-release@v1
    with:
      release_branch: "release/1.4.0"
      version: "1.4.0"
      delete_branch: "true"
```


Equivalent to:

```bash
git checkout main
git merge release/1.4.0 --no-ff
git tag v1.4.0
git push main --tags

git checkout devel
git merge release/1.4.0 --no-ff
git push devel

git push origin --delete release/1.4.0
```

## 3. Create Hotfix Branch

📂 `create-hotfix/`

Creates:

```pgsql
hotfix/<version>
```

from the `main` branch (default).

Example

```yml
steps:
  - uses: ross-robotics/gitflow-actions/create-hotfix@v1
    with:
      version: "1.3.1"
```

## 4. Finish Hotfix

📂 `finish-hotfix/`

Merges a hotfix branch into:

`main`

`devel`

Tags `v<version>`
Deletes the hotfix branch (optional).

Example

```yml
steps:
  - uses: ross-robotics/gitflow-actions/finish-hotfix@v1
    with:
      hotfix_branch: "hotfix/1.3.1"
      version: "1.3.1"
      delete_branch: "true"
```