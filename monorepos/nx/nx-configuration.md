# Nx Configuration

## 1. Plugins

- Plugins supports array of `string` or `object with options/arguments`.

- Specific projects use `include` or `exclude` that allowed glob patterns

```json
{
  "plugins": [
    "@my-org/graph-plugin",
    {
      "plugin": "@nx/eslint/plugin",
      "options": {
        "targetName": "lint"
      }
    },
    {
      "plugin": "@nx/jest/plugin",
      "include": ["packages/**/*"], // include any projects in the packages folder
      "exclude": ["**/*-e2e/**/*"] // exclude any projects in a *-e2e folder
    }
  ]
}
```

## 2. Inputs and Named Inputs

- Define which input files to include in the task.

The inputs option defined as:

```javascript
  {...namedInputsFromNxJson, ...namedInputsFromProjectsProjectJson}
```

Redefine named inputs in nx json. For example use production inputs,

```json
{
  "targetDefaults": {
    "test": {
      "inputs": ["default", "^production"]
    }
  }
}
```

And projects can define their production inputs

```json
{
  "namedInputs": {
    "production": ["default", "!{projectRoot}/**/*.test.js"]
  }
}
```

## 3. Outputs

- Define the location(s) for the task output
- The `{workspaceRoot}` and `{projectRoot}` tokens is useful to use.

```json
{
  "targetDefaults": {
    "@nx/js:tsc": {
      "options": { "main": "{projectRoot}/src/index.ts" },
      "configurations": {
        "prod": { "tsconfig": "{projectRoot}/tsconfig.prod.json" }
      },
      "inputs": ["prod"],
      "outputs": ["{workspaceRoot}/{projectRoot}"]
    },
    "build": {
      "inputs": ["prod"],
      "outputs": ["{workspaceRoot}/{projectRoot}"],
      "cache": true
    }
  }
}
```

## 4. Task Pipeline and Task Cache

[task pipeline](./task.md)

## 5. Release(optional)

- Basic command `nx release`
- Useful for versioning/Changelog/Publishing

- Define release projects,  
  the projects allows a string, or an array of strings.
  The strings can be `project names`, `glob patterns`, `directories`, `tag references`

  ```json
  {
    "release": {
      // Here we are configuring nx release to target all projects
      // except the one called "ignore-me"
      "projects": ["*", "!ignore-me"]
    }
  }
  ```

- Release Tag Pattern

  ```json
  {
    "release": {
      // Here we are configuring nx release to use a custom release
      // tag pattern (we have dropped the v prefix from the default)
      "releaseTagPattern": "{version}"
    }
  }
  ```

- Version

  ```json
  {
    "release": {
      "version": {
        "generatorOptions": {
          // Here we are configuring the generator to use git tags as the
          // source of truth for a project's current version
          "currentVersionResolver": "git-tag",
          // Here we are configuring the generator to use conventional
          // commits as the source of truth for how to determine the
          // relevant version bump for the next version
          "specifierSource": "conventional-commits"
        }
      }
    }
  }
  ```

- Changelog

  - Workspace Changelog:
    A changelog that contains all changes across all projects in your workspace.
    This is not applicable when releasing projects independently.

    ```json
    {
      "release": {
        "changelog": {
          // This disables the workspace changelog
          "workspaceChangelog": false
        }
      }
    }
    ```

    ```json
    {
      "release": {
        "changelog": {
          "workspaceChangelog": {
            // This will create a GitHub release containing the workspace
            // changelog contents
            "createRelease": "github",
            // This will disable creating a workspace CHANGELOG.md file
            "file": false
          }
        }
      }
    }
    ```

  - Project Changelogs:
    A changelog that contains all changes for a given project.

    ```json
    {
      "release": {
        "changelog": {
          // This enables project changelogs with the default options
          "projectChangelogs": true
        }
      }
    }
    ```

    ```json
    {
      "release": {
        "changelog": {
          "projectChangelogs": {
            // This will create one GitHub release per project containing
            // the project changelog contents
            "createRelease": "github",
            // This will disable creating any project level CHANGELOG.md
            // files
            "file": false
          }
        }
      }
    }
    ```
