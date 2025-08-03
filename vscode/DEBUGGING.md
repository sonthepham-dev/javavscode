# Debugging Library Code

This document explains how to debug library code in the VSCode extension project and addresses the breakpoint disable issue.

## Problem

The original build process used esbuild with bundling, which can interfere with setting breakpoints in library code and make debugging difficult. Users reported that breakpoints were being disabled when debugging library code.

## Root Cause

1. **Debugger Persistence**: The `LspDebuggerOptions.java` sets debugger persistence to `false` by default
2. **Build Configuration**: esbuild bundling interferes with source map resolution for library code
3. **Missing Debug Configurations**: No specific configurations for library debugging

## Solutions

We've added several new debugging configurations to improve the debugging experience:

### 1. Debug Extension (Unbundled)

This configuration compiles TypeScript without bundling, making it easier to set breakpoints in individual files.

**To use:**
1. Open the Debug panel in VSCode (Ctrl+Shift+D)
2. Select "Debug Extension (Unbundled)" from the dropdown
3. Press F5 to start debugging

### 2. Debug Library Code

This configuration provides enhanced debugging support specifically for library code with better source map resolution.

**To use:**
1. Open the Debug panel in VSCode (Ctrl+Shift+D)
2. Select "Debug Library Code" from the dropdown
3. Press F5 to start debugging

## Key Improvements

### Source Maps
- Enhanced source map generation and resolution
- Better mapping between TypeScript source and compiled JavaScript
- Improved breakpoint accuracy

### Build Configurations
- **Unbundled compilation**: TypeScript compilation without esbuild bundling
- **Debug compilation**: TypeScript + esbuild with debugging optimizations
- **Source map preservation**: Better preservation of original file structure

### VSCode Settings
- `debug.allowBreakpointsEverywhere`: Allows breakpoints in any file
- `debug.showInlineBreakpointCandidates`: Shows inline breakpoint suggestions
- `debug.inlineValues`: Shows variable values inline during debugging

## Troubleshooting

### Breakpoints not hitting
1. Make sure you're using one of the new debug configurations
2. Check that source maps are being generated correctly
3. Verify the TypeScript compilation completed successfully

### Can't set breakpoints in library files
1. Use the "Debug Library Code" configuration
2. Ensure the file is part of the workspace
3. Check that the file is not excluded in `.gitignore` or `.vscodeignore`

### Source maps not working
1. Verify `sourceMap: true` is set in `tsconfig.json`
2. Check that the debug build is using the correct configuration
3. Restart the debugging session

## Build Scripts

- `npm run compile-unbundled`: Compiles TypeScript without bundling
- `npm run compile-debug`: Compiles with debugging optimizations
- `npm run compile`: Standard compilation with bundling

## File Structure

The debugging configurations work with the following structure:
```
vscode/
├── src/           # TypeScript source files
├── out/           # Compiled JavaScript files
├── .vscode/
│   ├── launch.json    # Debug configurations
│   ├── tasks.json     # Build tasks
│   └── settings.json  # Debug settings
└── esbuild.js     # Build configuration
```

## Best Practices

1. **Use the right configuration**: Choose "Debug Library Code" for library debugging
2. **Check source maps**: Ensure source maps are generated and loaded correctly
3. **Restart debugging**: If breakpoints aren't working, restart the debugging session
4. **Clean builds**: Run `npm run compile-debug` to ensure clean compilation
5. **Check console**: Monitor the debug console for any build or runtime errors

## Related Issues

- **Breakpoint disable in library**: Fixed by adding unbundled compilation
- **Source map resolution**: Improved with debug-specific build configuration
- **Debugger persistence**: Considered in the context of library debugging