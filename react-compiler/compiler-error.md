# React Compiler Error

A series of error classes and interfaces used by the React compiler to report errors.

The hierarchy of the error classes is as follows:

`CompilerErrorDetailOptions` → `CompilerErrorDetail` → `CompilerError`

The `CompilerError` class is the main class used to report errors in the React compiler.
It is a subclass of the JavaScript `Error` class.

## CompilerErrorDetailOptions

A type representing the static data managed by `CompilerErrorDetail`, including:

- **reason**: The reason for the error.

- **description**: A description of the error.

- **severity**: The level of the error.

- **loc**: The location in the code where the error occurred.

- **suggestions**: Suggestions (e.g., for ESLint) to fix the error.

## CompilerErrorDetail

A class that manages `CompilerErrorDetailOptions`, including:

- Getters for `reason`, `description`, `severity`, `loc`, and `suggestions`.

- A method to print the error message.

## CompilerError Class

A class that manages all `CompilerErrorDetail` instances, including:

- Static methods for creating a new `CompilerErrorDetail` instance and throwing a `CompilerError` with a specified severity level, such as:

  - **invariant**: An unexpected internal error in the compiler that indicates critical issues and may cause the compiler to panic.

  - **throwTodo**: Unhandled syntax that is not yet supported.

  - **throwInvalidJS**: Invalid JavaScript syntax, or valid syntax that is semantically invalid, indicating possible misunderstanding by the user.

  - **throwInvalidReact**: Code that breaks the rules of React.

  - **throwInvalidConfig**: Incorrect configuration of the compiler.

These methods help categorize and handle different types of errors consistently within the compiler.

## References

[Github Source Code](https://github.com/facebook/react/blob/main/compiler/packages/babel-plugin-react-compiler/src/CompilerError.ts)
