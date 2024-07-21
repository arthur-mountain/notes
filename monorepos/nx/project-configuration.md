# Project Configuration

A project's configuration is constructed by Nx from three sources:

1. `Tasks inferred by Nx plugins` from tooling configuration
2. `Workspace targetDefaults` defined in the nx.json file
3. Individual project level configuration files (package.json and project.json)

Each source will overwrite the previous source.
Project level configuration will overwrite both targetDefaults and inferred tasks.

## 1. Basic

The following configuration creates build and test targets for Nx.

In `package.json`:

```json
{
  "name": "mylib",
  "scripts": {
    "test": "jest",
    "build": "tsc -p tsconfig.lib.json" // the actual command here is arbitrary
  }
}
```

In `project.json`:

```json
{
  "root": "libs/mylib/",
  "targets": {
    "test": {
      "executor": "@nx/jest:jest",
      "options": {
        /* ... */
      }
    },
    "build": {
      "executor": "@nx/js:tsc",
      "options": {
        /* ... */
      }
    }
  }
}
```

## 2. Task pipeline and Task Cache

[Task pipeline and cache](./task.md)

## 3. Inputs and Named Inputs

## 4. Outputs

[As same as nx configuration named inputs](./nx-configuration.md)

## 5. Project Metadata

- Tags

Enforce Module Boundaries using @nx/enforce-module-boundaries rule in eslint,
that check the import Boundaries is valid

In `project.json`:

```json
{
  "root": "libs/mylib",
  "tags": ["scope:myteam"]
}
```

## 6. ImplicitDependencies

Some dependencies cannot be deduced statically, so you can set them manually.

```json
{
  "root": "libs/mylib",
  "implicitDependencies": ["anotherlib", "!someignorelib", "common-*"]
}
```

## 7. includedScripts

Nx will merges all of scritps in package.json if it define in nx config or project config

We cloud limit script that nx will run by defining includedScripts

In `package.json`:

```json
{
  "name": "my-library",
  "version": "0.0.1",
  "scripts": {
    "build": "tsc",
    "postinstall": "node ./tasks/postinstall"
  },
  "nx": {
    "includedScripts": ["build"]
  }
}
```
