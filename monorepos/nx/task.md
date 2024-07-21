# Tasks(Targets)

A task(Target) is a command that can be run on a project. It can be a build, test, lint, etc.

## 1. Run task(s)

- Run single task

```text
Syntax: nx <target_name> <project_name> <option overrides>

# Run the `test` command for the `myapp` project
Example: nx test myapp --watch
```

- Run multiple tasks

```text
Syntax: nx run-many -t <target_name(s)>

# Run the `build` task for `all projects` in the repository
Example: nx run-many -t build

# Run the `lint/test/build` tasks for `all projects` in the repository
Example: nx run-many -t lint test build

# Run the `lint/test/build` tasks for `project-a` and `project-b` in the repository
Example: nx run-many -t lint test build -p project-a project-b
```

- Run task for affected projects

```text
Syntax: nx affected -t <target_name(s)>
```

## 2. Task pipeline global configuration

This defines the dependencies of each task.

For example, you might want to run the `build` target on the `ui` project before running the `build` target on the `myapp` project.

We define these global task pipelines(dependencies) in the nx.json.

In the following example, we are telling Nx that before running the build target,
it needs to run the build target on all the projects the current project depends on:

```json
{
  "targetDefaults": {
    "build": {
      "dependsOn": ["^build"]
    }
  }
}
```

## 3. Task Pipeline Root-Level Configuration

Similar to the global nx configuration, but this configuration can be defined in the package.json.

To invoke a task using `nx docs`

```json
{
  "name": "myshared",
  "scripts": {
    "docs": "node ./generateDocsSite.js"
  },
  "nx": {}
}
```

Note:
If you prefer to use npm/yarn/pnpm to run the script and also want Nx to cache the task result, use `nx exec`

To invoke a task using `pnpm run docs`

```json
{
  "name": "myshared",
  "scripts": {
    "docs": "nx exec -- node ./generateDocsSite.js"
  },
  "nx": {}
}
```

## 4. Task Cache

Nx caches the result of each task.
If a task has been run before, enabling the task cache option will prevent the same task from being executed again.

### Nx version greater than 17

To enable task cache, edit the `targetDefaults.[task].cache` property in nx.json:

```json
{
  "targetDefaults": {
    "build": {
      "cache": true
    },
    "test": {
      "cache": true
    }
  }
}
```

### Nx version less than 17

To enable task cache, edit the `cacheableOperations` property in nx.json:

```json
{
  "tasksRunnerOptions": {
    "default": {
      "runner": "nx/tasks-runners/default",
      "options": {
        "cacheableOperations": ["build", "test"]
      }
    }
  }
}
```

### Customizing the task cache result use `inputs` and `outputs`

Global definition in nx.json

```json
{
  "targetDefaults": {
    "build": {
      "inputs": ["{projectRoot}/**/*", "!{projectRoot}/**/*.md"],
      "outputs": ["{workspaceRoot}/dist/{projectName}"]
    }
  }
}
```

Project level definition in project.json

```json
{
  "name": "some-project",
  "targets": {
    "build": {
      "inputs": ["!{projectRoot}/**/*.md"],
      "outputs": ["{workspaceRoot}/dist/apps/some-project"]
    }
  }
}
```

### Run task force skip cache use `--skip-nx-cache`

```sh
  nx build ui --skip-nx-cache
```

### Clear the local cache use `reset`

```sh
  nx reset
```
