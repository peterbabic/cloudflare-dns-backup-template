# Cloudflare DNS backup template

Backups will be created in folder `dns-backups/domain.tld.jsom`.

## Instructions

1. Obtain Cloudflare API token
1. Add this token to your Github repository as a secret named
   `CLOUDFLARE_API_TOKEN`
1. Enable Github actions write permissions, as this creates automatic commits
1. The backup will run manually every Sunday early in the morning, adjust
   `schedule` in `.github/workflow/main.yml` if needed

### Cloudflare API token

Here's how to create a Cloudflare API token with minimal permissions for
read-only DNS access:

1. Log in to your Cloudflare dashboard at https://dash.cloudflare.com/
2. Click on your profile icon in the top-right corner and select "My Profile"
3. In the left sidebar, click on "API Tokens"
4. Click the "Create Token" button
5. Select "Create Custom Token"
6. Set a descriptive name like "DNS Backup Read-Only"
7. Under "Permissions" section:

- Select "Zone" from the first dropdown
- Select "DNS" from the second dropdown
- Select "Read" from the third dropdown

8. Under "Zone Resources" section:

- Select "Include" from the first dropdown
- Select "All zones" from the second dropdown
- (Alternatively, you can select "Specific zone" if you want to limit to certain
  domains)

9. Set an expiration date if desired (optional, for added security)
10. Click "Continue to summary"
11. Review the permissions and click "Create Token"
12. Copy the generated token immediately (it will only be shown once)

This token has the absolute minimum permissions needed - it can only read DNS
records and cannot make any changes to your configuration. It follows the
principle of least privilege, which is a security best practice.

### Adding Github Secrets

Github Secrets allow you to store sensitive information like API tokens
securely. Follow these steps to add your Cloudflare API token as a secret.

1. Go to your Github repository in your browser Click on the "Settings" tab in
   the top navigation bar (near the right side)
2. In the left sidebar, scroll down to the "Security" section Click on "Secrets
   and variables" Select "Actions" from the dropdown menu
3. Click the "New repository secret" button Enter the secret name exactly as
   referenced in your workflow:

- Enter CLOUDFLARE_API_TOKEN in the "Name" field
- Paste your Cloudflare API token in the "Secret" field Click "Add secret" to
  save

4. You should now see CLOUDFLARE_API_TOKEN listed in your repository secrets The
   value will be hidden and displayed as "●●●●●●●●" The secret is now available
   to your Github Actions workflows
5. Using the Secret in Workflows Your workflow is already configured to use this
   secret with:

### Enabling Github Actions Write Permissions

For your DNS backup workflow to successfully commit and push changes to your
repository, you need to grant it proper write permissions. Follow these steps:

1. Navigate to your Github repository Click on the "Settings" tab (near the top
   right of the repository page)
1. In the left sidebar, click on "Actions" under "Code and automation" Scroll
   down to the "Workflow permissions" section
1. Select the "Read and write permissions" option
1. Click "Save" to apply these settings

This is sufficient for running directly in Github.

## Local development

Follow all the steps from above. Then additionally install `nektos/act` on your
machine, for me on Mac M3 Pro Homebrew version worked:

```bash
brew install act
```

Obtain fine-grained Personal Access Token.

```bash
cp .secrets.example .secrets
```

In `.screts` file update both `CLOUDFLARE_API_TOKEN` and `GITHUB_TOKEN`. Put
your just obtained fine-grained token into this variable. Tweak anything else
needed in your `main.yml`. Then run locally:

```bash
act workflow_dispatch
```

If it is your first time running `act`, preferably choose **Medium** Docker
image (500 MB). Should work with **Large** (~75GB) and was not tested with
**Micro**.

### Creating a Fine-Grained Personal Access Token

This guide will walk you through creating a Github fine-grained personal access
token (PAT) with write permissions for a specific repository.

1. Log in to your Github account Click on your profile picture in the top-right
   corner Select "Settings" from the dropdown menu Scroll down to the bottom of
   the left sidebar and click on "Developer settings"
2. In the left sidebar, click on "Personal access tokens" Select "Fine-grained
   tokens" Click the "Generate new token" button
3. Token name: Enter a descriptive name (e.g., "DNS Backup Workflow")
   Description: Add an optional description of what this token will be used for
   Expiration: Select when the token should expire (recommended: choose a
   reasonable timeframe like 90 days) Resource owner: Select your Github
   username (or organization if applicable)
4. Choose "Only select repositories" Click the repository dropdown Select the
   specific repository you want to grant access to
5. Scroll down to the "Repository permissions" section Find "Contents" in the
   list Change the access level from "No access" to "Read and write" Leave all
   other permissions as "No access" to follow the principle of least privilege
6. Scroll to the bottom of the page Click "Generate token" IMPORTANT: Copy the
   generated token immediately and store it securely (this is your only chance
   to see the full token)

Note that `GITHUB_TOKEN` is automatically injected for Actions running via
Github. Filling in `GITHUB_TOKEN` secret is only needed when action is being run
via `act`.

## MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the “Software”), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
