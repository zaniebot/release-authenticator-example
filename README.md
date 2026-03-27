# example consumer repository

This directory is a stand-in for a separate repository that uses `release-authenticator-action`.
Copy these files into the repository that should be allowed to create releases with your GitHub App installation.

## What this example shows

- a `workflow_dispatch` release workflow on `main`
- a single human-reviewed `release-gate` job
- exchange of the GitHub Actions OIDC token for a short-lived installation token
- creation of a GitHub release with that token
- a separate `release` environment for the actual release job

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
3. Create a GitHub Actions environment named `release-gate` with the required human reviewers.
4. Create a GitHub Actions environment named `release` for the actual release job.
5. Ensure the workflow runs from the `main` branch.

## Running the example

From the target repository, dispatch the workflow on `main` with a version string that does not include the leading `v`:

```bash
gh workflow run release.yml -f version=1.2.3 --ref main
```

The workflow will pause once for the `release-gate` approval, then create a release named `v1.2.3`. The action revokes the installation token in its post step.
