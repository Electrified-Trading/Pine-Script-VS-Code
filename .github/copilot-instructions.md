# Pine Script Language Server – Copilot Instructions

You are contributing to a TypeScript-based Pine Script v6 language server, forked from the "Pine Script VS Code" extension. This project provides language features for TradingView Pine Script, including syntax diagnostics, hover tooltips, completions, and code actions.

Your goal is to extend and improve the language server with stricter linting rules, improved diagnostics, and better integration for AI tools like Claude or GitHub Copilot.

---

## 🔧 Core Principles

- Follow idiomatic, modern TypeScript (ES2022+), using strict typing and clear control flow.
- Respect the Language Server Protocol (LSP) — all diagnostics must be compliant with `textDocument/publishDiagnostics`.
- Prefer **pure functions** for rule logic (e.g., `checkIndentation(ast: AstNode[]): Diagnostic[]`).
- Avoid hardcoded constants; create shared config/enums when possible (e.g., `DiagnosticCode.BadIndentation`).
- All linting and formatting logic should be **stateless and deterministic**.

---

## 📐 Project Architecture

- `src/language-service/`: Main engine for parsing and linting
- `src/server/`: LSP server handlers (entry point for diagnostics, hovers, etc.)
- `src/types/`: AST definitions, diagnostic models, and utility interfaces
- `src/linting/rules/`: Each rule should live in its own file and export a function that returns `Diagnostic[]`.

---

## 🧪 Lint Rule Conventions

Each linting rule should:
- Accept a parsed AST or token stream
- Return an array of `Diagnostic` objects
- Include:
  - `code: string` (e.g., `INDENTATION_CONTINUATION`)
  - `message: string`
  - `severity: 1 | 2` (1 = warning, 2 = error)
  - `range: { start, end }` for highlighting
- Be added to a central `runAllLintRules()` function

Example structure:
```ts
export function checkBadIndentation(ast: AstNode[]): Diagnostic[] {
  const diagnostics: Diagnostic[] = [];
  for (const node of ast) {
    if (isContinuationLine(node) && !isIndentedCorrectly(node)) {
      diagnostics.push({
        code: "INDENTATION_CONTINUATION",
        message: "Continuation lines must be indented at least 4 spaces beyond the previous line.",
        severity: 2,
        range: node.range,
        source: "pine-lint",
      });
    }
  }
  return diagnostics;
}
````

---

## 🤖 Integration with AI Agents (Claude / Copilot)

To improve feedback loops:

* Support a CLI command or flag like `--dump-diagnostics-json` to output diagnostics to a file
* Enable running the server headlessly or against a `.pine` file without needing a full LSP client
* Keep linting rules modular so Claude can generate and test them independently

Use consistent naming so Claude can understand the purpose of each rule:

* `checkVariableShadowing`
* `checkLiteralBooleanMisuse`
* `checkUnreachableCode`
* `checkBadIndentation`

---

## 🧼 Formatting and Style

* Use Prettier for formatting (`.prettierrc`)
* Use ESLint with strict config (`.eslintrc.json`)
* Never mix tabs and spaces — all indentation should be 2 spaces
* Use PascalCase for types and interfaces, camelCase for functions

---

## ✅ Checklist Before PR

* [ ] New rule passes all unit tests in `src/linting/rules/__tests__/`
* [ ] Diagnostic has a `code`, `message`, and `range`
* [ ] Rule is registered in `runAllLintRules()`
* [ ] Rule has a snapshot test for expected `.pine` input

---

## 🔮 Future Vision

This project aims to become:

* The canonical open-source Pine Script linter
* A CLI-friendly tool usable by Claude, Copilot, or other developer agents
* The foundation for auto-fix and refactoring support in Pine Script

