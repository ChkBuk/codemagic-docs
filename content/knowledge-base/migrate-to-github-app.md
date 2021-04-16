---
title: Migrating from GitHub OAuth to GitHub App
weight: 1
---

On April 19, 2021, Codemagic deprecated the GitHub OAuth integration due to GitHub ending support for OAuth App integrations, you can read more about it [here](https://developer.github.com/changes/2020-02-14-deprecating-oauth-app-endpoint/). If you used the GitHub OAuth integration, please switch to the GitHub App to continue building your GitHub repositories.

## Who needs to migrate?

If you signed up or used to log in to Codemagic via GitHub (All repositories, OAuth), then this means we were using the OAuth integration to access the repositories on your GitHub account. A warning is displayed on the Applicactions page and also in the team settings if any of the apps shared in a team or on your personal account used the OAuth integration. Unless you switch to GitHub App and share access to these repositories, they will remain unavailable.

Deprecating OAuth does not affect GitHub repositories that are added over SSH or HTTPS.

## GitHub OAuth vs GitHub App

The key difference between OAuth and GitHub App integrations is the scope of permissions. While OAuth requires you to grant Codemagic a very broad scope of permissions which included **write access** to all your repositories, the GitHub App integration requests only **read access** to your code and you can select which repositories you share with Codemagic.

In addition, the GitHub App integration will also make it possible to use [GitHub Checks](../building/github-checks).

### Deprecting GitHub releases in Flutter workflow editor

Without write access to the repository, Codemagic will no longer be able to push GitHub releases without additional configuration. Therefore, we will be removing the GitHub releases section from Flutter workflow editor. You can continue to publish GitHub releases by setting up a personal access token and using a custom script in the **Pre-publish** step.

1. Create a personal access token in GitHub as described [here](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token).
2. Save it as an environment variable with the name `GITHUB_TOKEN`.
3. Add the following custom script in the **pre-publish** step that publishes the artifacts with tag builds.

```bash
#!/usr/bin/env zsh

# Publish only for tag builds
if [ -z ${FCI_TAG} ]; then
   echo "Not a tag build will not publish GitHub release"
   exit 0
fi

# See more options about `gh release create` usage from GitHub CLI
# official docs at https://cli.github.com/manual/gh_release_create

gh release create "${FCI_TAG}" \
    --title "<Your Application Name> ${FCI_TAG}" \
    --notes-file changelog.md
    path/to/build-artifact.ipa \
    path/to/build-artifact.apk

# Note that you don't need to include title and changelog if you do not want to.
# Any number of artifacts can be included with the release.
```

## Switching to GitHub App

From April 19 onwards, users logging in to Codemagic with their GitHub account will be prompted to authorize and install the GitHub App if they do not already have it installed.

If you log in via email, Bitbucket or GitLab but have repositories that are accessed via the GitHub OAuth integration, you should enable the GitHub App integration in your user or team settings. Note that GitHub App must be enabled separately for your **personal account** in Codemagic user settings and each **team** in team settings. 

{{<notebox>}}
See how to connect the GitHub App integration, manage access to accounts and repositories and troubleshoot issues with repository access [here](../getting-started/signup/#connecting-the-github-app-integration).
{{</notebox>}}
