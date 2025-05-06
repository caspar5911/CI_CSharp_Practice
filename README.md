# Automated Version Bump Workflow

This repository uses a GitHub Actions workflow to automatically manage versioning for modules within the codebase. It detects code changes, determines the type of version bump (major, minor, build, or revision), updates version numbers in `AssemblyInfo.cs` and `.csproj` files, and optionally creates Git tags.

---

## Workflow Trigger

The workflow is triggered on every push to the `main` branch.

```yaml
on:
  push:
    branches:
      - main
```

---

## How It Works

1. **Checkout Full History**
   - Fetches the complete Git history to allow accurate diff and log checks.

2. **Configure Git Identity**
   - Uses `github-actions[bot]` for committing automated changes.

3. **Detect Changed Files**
   - Identifies changed files between the previous and current commit.
   - Searches for modules with either `AssemblyInfo.cs` or `.csproj` files.

4. **Determine Version Bump Type**
   - Analyzes commit messages:
     - `"BREAKING CHANGE:"` → Major
     - `"feat:"` → Minor
     - `"fix:"` → Build
     - Default → Revision

5. **Update Versions**
   - Bumps the version in:
     - `AssemblyInfo.cs` via `AssemblyFileVersion`
     - `.csproj` via `<FileVersion>`

6. **Commit Changes**
   - Stages and commits changes if any version bump occurred.

7. **Tag (Only for Major/Minor)**
   - Creates Git tags only for **major** and **minor** bumps.
   - Tag format: `path/to/module/vX.X.X.X`

8. **.gitignore**
   - Intermediate text files (like `changed_modules.txt`, `valid_modules.txt`, `module_versions.txt`) are generated but **excluded from version control** via `.gitignore`.

---

## Commit Message Format

To ensure correct version bump detection, follow these conventions:

| Commit Prefix       | Resulting Bump Type |
|---------------------|---------------------|
| `BREAKING CHANGE:`  | Major               |
| `feat:`             | Minor               |
| `fix:`              | Build               |
| _(anything else)_   | Revision            |

---

## Example

A `feat:` commit inside a module with `AssemblyInfo.cs` would:
- Detect a **minor** bump
- Update version from `1.2.3.4` to `1.3.0.0`
- Commit the change
- Tag the commit with `path/to/module/v1.3.0.0`

---

## File Usage

| File Name                | Purpose                           |
|--------------------------|-----------------------------------|
| `changed_modules.txt`    | List of changed files             |
| `valid_modules.txt`      | Filtered list of modules          |
| `module_versions.txt`    | Output of updated modules/versions|

These files are ignored using `.gitignore`.

---

## Notes

- `AssemblyVersion` is not bumped automatically, as it is used for binary compatibility and usually only changed for breaking changes.
- Only **major** and **minor** changes are tagged to reduce noise.
- Always **pull latest changes** before pushing if working manually, to avoid merge commits confusing the bump detection.

---

## License

MIT License
