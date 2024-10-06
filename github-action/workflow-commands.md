# Workflow commands

Workflow commands when running shell commands in a workflow or in an action's code.

## Example of a workflow command

```bash
  echo "::workflow-command parameter1={data},parameter2={data}::{command value}"
```

> [!NOTE]
> Workflow command and parameter names are `case insensitive`.

> [!WARNING]
> If you are using Command Prompt, `omit double quote characters (")` when using workflow commands.

## [Action Toolkit Functions](https://github.com/actions/toolkit)

The `actions/toolkit` includes a number of functions that can be executed as workflow commands.

For example the error annotation, as below is same

- action/toolkit

  ```javascript
  core.error("Missing semicolon", { file: "app.js", startLine: 1 });
  ```

- workflow yaml

  Use the `::` syntax to run the workflow commands within your YAML file;
  these commands are then sent to the runner over `stdout`.

  ```yaml
  - name: Create annotation for build error
    run: echo "::error file=app.js,line=1::Missing semicolon"
  ```

## More examples of workflow commands

1. **notice message**

   **Syntax:**

   ```text
   ::notice file={name},line={line},endLine={endLine},title={title}::{message}
   ```

   **Example:**

   ```yaml
   - name: Create annotation for build error
     run: echo "::notice file=app.js,line=1,col=5,endColumn=7::Missing semicolon"
   ```

2. **warning message**

   **Syntax:**

   ```text
   ::warning file={name},line={line},endLine={endLine},title={title}::{message}
   ```

   **Example:**

   ```yaml
   - name: Create annotation for build error
     run: echo "::warning file=app.js,line=1,col=5,endColumn=7,title=YOUR-TITLE::Missing semicolon"
   ```

3. **group message**

   **Syntax:**

   ```text
   ::group::{title}
   ::endgroup::
   ```

   **Example:**

   ```yaml
   - name: Group of log lines
     run: |
       echo "::group::My title"
       echo "Inside group"
       echo "::endgroup::"
   ```

4. **Masking message**

   Each masked word `separated by whitespace is replaced with the * character`.
   When you mask a value, you won't be able to set that value as an output.
   It is `treated as a secret` and will be redacted on the runner.

   **Syntax:**

   ```text
   ::add-mask::{value}
   ```

   **Example:**

   ```yaml
   jobs:
     bash-example:
       runs-on: ubuntu-latest
       env:
         MY_NAME: "One Two Three"
       steps:
         - name: bash-version
           # 👇This will print "***" instead of "One Two Three" in a log
           run: echo "::add-mask::$MY_NAME"
   ```

   **Example: Generate a masked value as an output to share with other `steps`**

   ```yaml
   jobs:
     generate-a-secret-output:
       runs-on: ubuntu-latest
       steps:
         - id: sets-a-secret
           name: Generate, mask, and output a secret
           run: |
             the_secret=$((RANDOM))

             # Generate a masked value
             echo "::add-mask::$the_secret"

             # Set the masked value to output for other steps
             echo "secret-number=$the_secret" >> "$GITHUB_OUTPUT"

         - name: Use that secret output (protected by a mask)
           run: |
             echo "the secret number is ${{ steps.sets-a-secret.outputs.secret-number }}"
   ```

5. **Stopping and Starting workflow commands**

   Stops processing any workflow commands.

   **Syntax:**

   ```text
   ::stop-commands::{endtoken}

   ...the any of the following workflow commands will be stopped processing

   ::{endtoken}::

   ...the any of the following workflow commands will be started processing
   ```

   **Example:**

   ```yaml
   jobs:
     workflow-command-job:
       runs-on: ubuntu-latest
       steps:
         - name: Disable workflow commands
           run: |
             echo '::warning:: This is a warning message, to demonstrate that commands are being processed.'

             stopMarker=$(uuidgen) # Generate a unique endtoken

             echo "::stop-commands::$stopMarker" # Stop processing commands after this line

             echo '::warning:: This will NOT be rendered as a warning, because stop-commands has been invoked.'

             echo "::$stopMarker::" # Start processing commands after this line

             echo '::warning:: This is a warning again, because stop-commands has been turned off.'
   ```

## Setting an environment variable

1. **Single line string**

   **Syntax:**

   ```text
   echo "{environment_variable_name}={value}" >> $GITHUB_ENV
   ```

   **Example:**

   ```yaml
   - name: Store build timestamp in an environment variable
     run: echo "BUILD_TIME=$(date +%T)" >> $GITHUB_ENV

   - name: Deploy using stored timestamp in environment variable
     run: echo "Deploying at $BUILD_TIME"
   ```

2. **Multiline string**

   **Syntax:**

   ```text
   {name}<<{delimiter}
   {value}
   {delimiter}
   ```

   **Example:** uses `EOF` as the delimiter, and sets the `JSON_RESPONSE` environment variable to the value of the curl response.

   ```yaml
   - name: Set the value in bash
     id: step_one
     run: |
       {
         echo 'JSON_RESPONSE<<EOF'
         curl https://example.com
         echo EOF
       } >> "$GITHUB_ENV"
   ```

## Setting an output parameter

**Syntax:**

```text
echo "{output_name}={output_value}" >> $GITHUB_OUTPUT
```

**Example:**

```yaml
- name: Set color
  id: color-selector
  run: echo "SELECTED_COLOR=green" >> $GITHUB_OUTPUT

- name: Get color
  run: echo "The selected color is ${{ steps.color-selector.outputs.SELECTED_COLOR }}"
```

## Appending/Overwriting/Deleting a job summary

**Syntax:**

```text
echo "{markdown content}" >> $GITHUB_STEP_SUMMARY
```

**Example:**

```yaml
- name: (Append) single line summary
  run: echo "### Hello world! :rocket:" >> $GITHUB_STEP_SUMMARY

- name: (Append) Generate list(Multiline) using Markdown
  run: |
    echo "This is the lead in sentence for the list" >> $GITHUB_STEP_SUMMARY

    echo "" >> $GITHUB_STEP_SUMMARY # this is a blank line

    echo "- Lets add a bullet point" >> $GITHUB_STEP_SUMMARY
    echo "- Lets add a second bullet point" >> $GITHUB_STEP_SUMMARY
    echo "- How about a third one?" >> $GITHUB_STEP_SUMMARY

- name: (Overwrite) Markdown
  run: |
    echo "Appending some Markdown content" >> $GITHUB_STEP_SUMMARY

    # This will overwrite the previous content
    echo "There was an error, we need to clear the previous Markdown with some new content." > $GITHUB_STEP_SUMMARY

- name: (Delete) all summary content
  run: |
    echo "Append Markdown content that we want to remove before the step ends" >> $GITHUB_STEP_SUMMARY

    rm $GITHUB_STEP_SUMMARY
```

## Appending a system path

Prepends a directory to the system `PATH` variable and automatically makes it available to all subsequent actions in the current job;

the currently running action cannot access the updated path variable.

**Syntax:**

```text
echo "{excutable_path}" >> $GITHUB_PATH
```

**Example:**

```yaml
- name: (Append) executable path
  run: echo "$HOME/xxx/xxx/bin" >> $GITHUB_PATH
```

## 資料來源

[workflow commands](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/workflow-commands-for-github-actions#example-masking-and-passing-a-secret-between-jobs-or-workflows)
