# Deploying your own Sweep instance on modal

## Prerequisites
Ensure you have the following:

- OpenAI API-key, preferably with access to a 32k context GPT-4 model
- Network exposed MongoDB and Redis instances
- Poetry package manager installed on your machine
- Account on modal.com (free-tier)

> This guide is set for a minimal working setup. Feel free to take a look at `sweepai/config/server.py` and adjust your steps as per requirements.

## Steps
---
1. **Clone the sweep repository and install dependencies:**

    ```bash
    git clone git@github.com:sweepai/sweep.git
    cd sweep
    ./bin/install.sh
    ```
---
2. **Install the modal client, link it to your account:**

    ```bash
    pip install modal-client
    modal token new
    ```
---
3. **Register a new GitHub App:**

    Use [this guide](https://docs.github.com/en/apps/creating-github-apps/registering-a-github-app/registering-a-github-app) to register a new GitHub App.

    Permissions:
    ```
    actions: read
    check: read
    contents: read & write
    commit statuses: read & write
    issues: read & write
    metadata: read
    pull requests: read & write
    workflows: read & write
   ```

   Webhook events:

   ```text
    issues
    pull request
    workflow run
    check run
    issue comment
    label
    pull request review
    pull request review comment
    pull request thread
    status
    workflow job
    create
    commit comment
    check suite
    push
   ```

   Disable the webhook for now. You can use any URL for the homepage (e.g. repository URL). [Install the app](https://docs.github.com/en/apps/using-github-apps/installing-your-own-github-app) on your account or organization and generate a private key as per [this guide](https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/managing-private-keys-for-github-apps).
---

4. **Setup modal environment:**

   - Navigate to the 'Secrets' tab in Modal.
   - Create a 'github' secret with the following values:
    ```cmd
    GITHUB_APP_ID: Your App ID from general tab in Application settings
    GITHUB_BOT_USERNAME: Your App name
    GITHUB_APP_PEM: Your App's private key, make sure to include whitespaces as in original file with pk
    ```

   - Create 'openai-secret' modal secret with following variables:
    ```cmd
    OPENAI_API_KEY: Your OpenAI API key
    OPENAI_DO_HAVE_32K_MODEL_ACCESS: true/false depending on if you have access to 32k model
    ```

   - Create 'mongodb', and 'redis_url' modal secrets with following variables:
    ```cmd
    MONGODB_URI: MongoDB connection URL
    REDIS_URL: Redis connection URL
    ```

   - Go to the root of previously cloned project and run the following command:
    ```bash
    ./bin/create_optional_secrets.sh
    ```
---

5. **Deploy Sweep to modal:**

    ```bash
    poetry shell
    ./bin/deploy.sh
    ```
---
6. **Setup Github webhook:**

- Head over to 'modal.com' and login.
- Proceed to 'Apps', locate your deployed API instance (by default, it is 'prod-api').
- Search for the webhook function and duplicate the webhook URL.
- In the GitHub app settings, populate the webhook URL field with this copied URL. Ensure SSL verification remains active.
---
