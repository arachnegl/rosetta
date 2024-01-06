

## code organisation and packaging

### File Hierarchy-Based

file and directory structure to determine the module hierarchy.

- Python                              `from`
- Ruby                                `require`
- JavaScript/TypeScript with Node.js: `require` or `import` 

### Explicit Module Declaration

- Rust: `mod` keyword to explicitly declare modules.
- Java: `package` statement top of each file declares the module/package and the directory structure must match this package declaration.
- Go: package declarations at the top of each file, but the language enforces a strong convention where each directory corresponds to a single package.

### Additional Styles

- C/C++: use a preprocessor to include header files. Modern C++ module system (C++20 onwards) introduces explicit module declarations.
- .NET languages (C#, F#): use a namespace system, which can span multiple files and must be declared explicitly within each file.
