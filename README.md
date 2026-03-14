# Plugin Store / Database

**This repository exists solely for developers to submit plugins.**

Use the built-in Plugin Store (or your store frontend) to download and install plugins.

## Submit your plugin

To submit a plugin to this store, open a pull request adding your plugin as a **submodule** (see [example PR #189](https://github.com/SteamDeckHomebrew/decky-plugin-database/pull/189) on the official Decky database).

- Don't forget to bump your version number in `package.json`.
- If you are not sure how to use submodules, see the [Git submodules documentation](https://git-scm.com/book/en/v2/Git-Tools-Submodules).

### Adding your plugin as a submodule (first time)

1. **Have your plugin in its own Git repository** (e.g. on GitHub/GitLab). The plugin must be a separate repo, not just a folder in this repo.

2. **Fork this repository** (if it’s not yours) and clone it:
   ```bash
   git clone --recurse-submodules https://github.com/YOUR_USER/store.git
   cd store
   ```

3. **Add your plugin as a submodule** (use the folder name you want under `plugins/`, usually the plugin name):
   ```bash
   git submodule add https://github.com/YOUR_USER/your-plugin-repo.git plugins/your-plugin-name
   ```

4. **Commit and push**, then open a Pull Request:
   ```bash
   git add plugins/your-plugin-name .gitmodules
   git commit -m "Add your-plugin-name to the store"
   git push origin main
   ```

### Updating an existing plugin (submodule) to a newer version

Once your plugin is in the store as a submodule, to publish an update:

1. `git checkout main` (sync with upstream: `git pull`).
2. `git checkout -b update/your-plugin` (or any branch name).
3. `cd plugins/your-plugin-name`
4. `git pull` (or `git fetch && git checkout <tag>` to pin a release).
5. `cd ../..`
6. `git add plugins/your-plugin-name`
7. `git commit -m "Update your-plugin-name to latest"`
8. `git push`

---

## Building plugin zips (official method)

The repo includes a **GitHub Actions** workflow that builds plugin zips the same way as the [official Decky plugin database](https://github.com/SteamDeckHomebrew/decky-plugin-database):

1. **In CI (this repo):** On push or PR that touches `plugins/**`, the workflow checks out submodules, then for each plugin runs:
   - `pnpm i --frozen-lockfile` (or `pnpm i` if no lockfile)
   - `pnpm run build`
   - Creates a zip excluding `src/`, `node_modules/`, `__pycache__/` (and similar), named from `plugin.json` → `name` (e.g. `zipline-uploader.zip`).
   - Uploads the zip as a workflow artifact.

2. **Optional – Docker builder:** The `builder/` folder contains the same Dockerfile and `entrypoint.sh` as the official database. To build a plugin locally with it:
   - Mount the plugin dir at `/plugin` and a writeable dir at `/out`.
   - The container runs `pnpm i --frozen-lockfile`, `pnpm run build`, then rsyncs the built plugin (excluding src, node_modules, __pycache__) to `/out`. You can then zip the contents of `/out` and use the **plugin name from `plugin.json`** as the zip filename so the store/loader installs it correctly.

Plugins must have a `plugin.json` with a `name` field and a `package.json` with a `build` script (e.g. Rollup). Use **pnpm** and a **pnpm-lock.yaml** for reliable CI builds.

---

## Repository layout

- **`plugins/`** — Each entry is a **git submodule** pointing to a plugin’s repository. Do not add plugin source code directly here; add submodules only.

### If your plugin is currently in this repo (not its own repo)

To match the decky-plugin-database model, each plugin must live in its **own** Git repository. To convert an existing plugin folder (e.g. `plugins/zipline-auto-uploader`) into a submodule:

1. **Create a new repository** (e.g. on GitHub) for that plugin.
2. **Move the plugin code** into the new repo (copy the plugin folder contents, init git there, push to the new repo).
3. **Remove the plugin folder from this repo** (don’t delete the folder yet if you need to copy from it):
   ```bash
   git rm -r plugins/your-plugin-name   # or move/copy first, then remove
   ```
4. **Add it back as a submodule** pointing at your new repo:
   ```bash
   git submodule add https://github.com/YOUR_USER/your-plugin-repo.git plugins/your-plugin-name
   git add .gitmodules plugins/your-plugin-name
   git commit -m "Add your-plugin-name as submodule"
   ```

---

## Reference

This structure follows the same model as the [Steam Deck Homebrew Decky Plugin Database](https://github.com/SteamDeckHomebrew/decky-plugin-database): plugins are submitted and updated via pull requests that add or update submodules in the `plugins/` directory.
