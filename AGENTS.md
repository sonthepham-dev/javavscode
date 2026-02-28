# AGENTS.md

## Cursor Cloud specific instructions

This is Oracle's Java Platform Extension for VS Code — a TypeScript VS Code extension backed by a Java (Apache NetBeans) LSP server.

### Project structure
- `vscode/` — TypeScript extension source (package.json, src/, tests)
- `netbeans/` — Git submodule pointing to Apache NetBeans
- `netbeans-l10n/` — Git submodule for localization bundles
- `nbcode/` — Custom NetBeans modules for the LSP server
- `build.xml` — Ant build orchestrator
- `patches/` — Patches applied on top of the NetBeans submodule

### Prerequisites (system)
- JDK 17+ (21 recommended; pre-installed as `java-21-openjdk-amd64`)
- Apache Ant (`sudo apt-get install -y ant`)
- Apache Maven (`sudo apt-get install -y maven`)
- Node.js LTS + npm (pre-installed)
- Xvfb (`xvfb-run`; pre-installed)

### Build workflow (see BUILD.md for full details)
1. `git submodule update --init --recursive`
2. `ant apply-patches`
3. `ant build-netbeans` (~6 min; builds the full NetBeans platform)
4. `ant build-lsp-server` (~12 sec; builds LSP server into `vscode/nbcode/`)
5. `cd vscode && npm install && npm run compile`

### Running tests
- **Unit tests:** `cd vscode && npm run test:unit` (Mocha, fast, no display needed)
- **Integration tests:** `cd vscode && xvfb-run npm run test` (launches VS Code electron; ~10 min; one test "Refactor actions executing" may timeout — this is a known pre-existing issue)
- **LSP server tests (Java):** `ant test-lsp-server`

### Lint / type checking
- The project does not have an `npm run lint` script. Use `tsc --noEmit` in `vscode/` for TypeScript type checking.
- An `.eslintrc.json` exists but ESLint is not a direct dependency; `tsc` is the primary check.

### Gotchas
- The `netbeans` submodule is large (~1 GB). `git submodule update --init --recursive` may take 30+ seconds.
- `ant apply-patches` must be run after initializing submodules and before building NetBeans. If patches fail, the submodule may already be patched; use `ant unapply-patches` first.
- `ant build-netbeans` is the slowest step. Subsequent incremental builds of just the LSP server (`ant build-lsp-server`) are much faster.
- The LSP server binary is at `vscode/nbcode/bin/nbcode.sh`. It can be tested standalone: `vscode/nbcode/bin/nbcode.sh -J-Dnetbeans.close=true --modules --list`
- Integration tests require `xvfb-run` on headless Linux since they launch a VS Code electron instance.
- Package manager is **npm** (lockfile: `package-lock.json`), not pnpm or yarn.
