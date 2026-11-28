# Development environment setup (Windows)

The local toolchain used to build RegLens, and how to reproduce it. These versions were recorded
on 2026-11-28 on Windows 11 (x86-64).

## Installed versions

| Tool | Version | Installed with | Check |
|---|---|---|---|
| Git for Windows | 2.52.0 | winget `Git.Git` | `git --version` |
| uv | 0.13.10 | Standalone installer; updated with `uv self update` | `uv --version` |
| Python | 3.12.13 | `uv python install 3.12` (uv-managed) | `uv run --no-project --python 3.12 python -V` |
| Node.js | 24.16.0 (LTS) | winget `OpenJS.NodeJS.24` | `node -v` |
| Corepack | 0.35.0 | Bundled with Node 24 | `corepack --version` |
| pnpm | 10.34.9 | Corepack (see below) | `pnpm -v` |
| Docker Desktop | 4.99.0 (Engine 29.9.3) | winget `Docker.DockerDesktop`, WSL2 backend | `docker run --rm hello-world` |
| WSL | 2.7.8.0 (kernel 6.18.33.1-1) | `wsl --install`; kept current with `wsl --update` | `wsl --version` |
| GitHub CLI | 2.102.0 | winget `GitHub.cli` | `gh auth status` |
| k6 | 2.4.0 | winget `GrafanaLabs.k6` | `k6 version` |
| IDE | PyCharm 2026.2.3 | winget `JetBrains.PyCharm` | — |

Keep the tools current with `winget upgrade --id <id> -e`. Re-record this table when a version
changes.

Python 3.12 is the project's target version, even though newer interpreters may be installed side
by side. uv selects it from `requires-python` once the workspace exists.

## Notes

### pnpm through Corepack without administrator rights

Node is installed under `C:\Program Files\nodejs`. A plain `corepack enable` writes shims there,
which needs an elevated shell. Writing the shim to a user directory that is already on `PATH`
avoids that:

```powershell
corepack enable --install-directory "$env:USERPROFILE\.local\bin" pnpm
corepack install --global pnpm@10
pnpm -v
```

Once the web app exists, its `package.json` pins the exact pnpm version in the `packageManager`
field, and Corepack uses that version inside the repository.

### Line endings

Git for Windows ships with `core.autocrlf=true` in its system config. The repository manages line
endings through `.gitattributes` (`* text=auto eol=lf`), so turn the conversion off for this clone:

```powershell
git config core.autocrlf false
```

After cloning, `git ls-files --eol` should show `i/lf w/lf` for every text file.

### Git identity

Commits use the owner's name and GitHub **noreply** email, so a personal address never reaches
public history:

```powershell
git config user.name "<owner name>"
git config user.email "<id>+<username>@users.noreply.github.com"
```

### Local-only files

Editor and local tooling folders (for example `.idea/` and `.vscode/`) are listed in
`.git/info/exclude`, which is never committed, rather than in `.gitignore`.

## Verification

All of these succeed on the machine described above:

```powershell
uv --version
node -v
pnpm -v
docker run --rm hello-world
gh auth status
```
