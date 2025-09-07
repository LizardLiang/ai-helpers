---
name: /git-commit-helper
description: Generate Git commit messages following conventional commit standards. Analyzes staged changes and outputs human-like commit messages with no AI attribution.
---

---

# 🚨 CRITICAL RULE: NO AI ATTRIBUTION 🚨

Commit messages must **never** contain:

- `Co-authored-by:` tags
- AI or Claude signatures
- Assistant credits
- Metadata about AI or automation
- Automated generation notices

Messages must appear 100% human-written.

---

## ✅ Conventional Commit Types

- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation changes
- **style**: Formatting only (no logic change)
- **refactor**: Code restructuring without behavior change
- **test**: Add or modify tests
- **chore**: Maintenance, tooling, dependencies
- **perf**: Performance improvements
- **ci**: CI/CD configuration changes
- **revert**: Revert a previous commit

---

## Language

- when a project has existing commit messages, match their language (e.g., English, Spanish, etc.)
- when a project has no existing commits, default to English

---

## 📝 Commit Message Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Examples

```
feat(auth): add OAuth2 integration with Google and GitHub
fix(api): resolve memory leak in session cleanup
docs(readme): add Docker setup instructions
test(user): add unit tests for password validation
```

---

## 🔍 Analysis Process

1. **Check staged changes** with `git diff --cached` (fallback: `git diff`).
2. **File-by-file review**:
   - Identify change type and scope
   - Note affected functions, modules, or features
   - Assess technical or business reason

3. **Generate commit message**:
   - Imperative mood (“add”, “fix”, “update”)
   - Be specific (what changed + why if relevant)
   - Include scope when useful

---

## 📦 Multi-file Strategy

- **Single commit**: When changes are logically related (feature + tests + docs).
- **Multiple commits**: When unrelated changes are mixed (e.g., bugfix + dependency upgrade).

---

## ❌ Prohibited

- Co-author tags
- AI/Claude/assistant signatures
- Any automated attribution

---

## Usage

Run inside Claude Code when you have staged changes. It will:

- Inspect diffs
- Categorize changes
- Suggest a **conventional commit** message

```bash
/git-commit-helper
```
