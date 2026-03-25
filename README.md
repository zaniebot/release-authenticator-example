# example consumer repository

This directory is a stand-in for a separate repository that uses `release-authenticator-action`.
Copy these files into the repository that should be allowed to create releases with your GitHub App installation.

## What this example shows

- a `workflow_dispatch` release workflow on `main`
- exchange of the GitHub Actions OIDC token for a short-lived installation token
- creation of a GitHub release with that token
- use of a `release` environment so the Worker can optionally require environment approval

## Expected Worker configuration

This example lines up with the default hardening described in `../app/README.md`:

```toml
EXPECTED_AUDIENCE = "https://release-authenticator.<subdomain>.workers.dev"
ALLOWED_REF = "refs/heads/main"
ALLOWED_WORKFLOW_PATH = ".github/workflows/release.yml"
ALLOWED_ENVIRONMENT = "release"
```

Because `ALLOWED_REF` is pinned to `refs/heads/main`, this example triggers from `main` and creates the tag during the release step instead of running from a tag push.

## Repository setup

In the target repository:

1. Install the GitHub App on that repository.
2. Add a repository variable named `RELEASE_AUTHENTICATOR_URL` with the full exchange URL, for example:
   - `https://release-authenticator.<subdomain>.workers.dev/exchange`
3. Create a GitHub Actions environment named `release` if you want approval gates.
4. Ensure the workflow runs from the `main` branch.

## Running the example

From the target repository, dispatch the workflow on `main` with a version string that does not include the leading `v`:

```bash
gh workflow run release.yml -f version=1.2.3 --ref main
```

The workflow will create a release named `v1.2.3` and the action will revoke the installation token in its post step.
