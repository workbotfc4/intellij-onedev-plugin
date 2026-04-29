# Dev notes

This plugin was built using https://github.com/JetBrains/intellij-platform-plugin-template,
so familiarize yourself with the [README.md](README.md) first

## Build locally
`./gradlew buildPlugin`

## Test
Install the plugin using the `intellij-onedev-plugin-0.0.1.zip` located under `build/distributions` folder

## Server Configuration
- Server URL
- Access Token
- TODO: username/password?

## Integration testing
In addition to default testing routines coming with the plugin template, there an end-to-end integration test
running against the OneDev Docker image integrated into thr `build.yml` pipeline. How to run locally:
- Run `onedev.sh` to launch the Docker image
- Run `OneDevRepositoryTest.java`

## Releasing to GitHub / JetBrains Marketplace

Releases are semi-automated via two GitHub Actions workflows (`build.yml` → `release.yml`).

### Step-by-step

1. **Bump the version** in `gradle.properties`:
   ```
   pluginVersion = 0.0.10
   ```

2. **Update `CHANGELOG.md`** — rename the `[Unreleased]` section to the new version number:
   ```markdown
   ## [0.0.10]
   ### Added
   - ...
   ```
   Add a fresh empty `## [Unreleased]` section above it.

3. **Merge to `main`** (via PR as usual). The `build.yml` workflow runs automatically and, when all checks pass (build, test, Qodana, plugin verifier), it creates a **draft GitHub Release** tagged `v0.0.10` with the `[Unreleased]` changelog content as release notes.

4. **Publish the draft release** on GitHub (`Releases → v0.0.10 → Edit → Publish release`). Publishing triggers the `release.yml` workflow, which:
   - Builds and signs the plugin
   - Publishes it to the [JetBrains Marketplace](https://plugins.jetbrains.com/) via `./gradlew publishPlugin`
   - Uploads the built `.zip` as a release asset

### Required GitHub secrets

The following secrets must be set in the repository settings (`Settings → Secrets → Actions`) for the release workflow to work:

| Secret | Purpose |
|--------|---------|
| `PUBLISH_TOKEN` | JetBrains Marketplace API token |
| `CERTIFICATE_CHAIN` | Plugin signing certificate chain (PEM) |
| `PRIVATE_KEY` | Plugin signing private key (PEM) |
| `PRIVATE_KEY_PASSWORD` | Password for the private key |

See [Plugin Signing](https://plugins.jetbrains.com/docs/intellij/plugin-signing.html) for how to generate the signing keys.
