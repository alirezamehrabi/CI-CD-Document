# CI-CD-Document

# Deploy React Application with CI/CD and GitHub Pages

Follow the steps below to set up CI/CD for deploying your React application to GitHub Pages:

---

## Step 1: Install `gh-pages` as a Dev Dependency
Run the following command to add `gh-pages` to your project:
```bash
npm install gh-pages --save-dev
```

---

## Step 2: Update `package.json`
Add the following lines to your `package.json`:

- Set the `homepage` field:
  ```json
  "homepage": "https://{YOUR GITHUB ACCOUNT USERNAME}.github.io/{GITHUB REPOSITORY}/"
  ```
  Replace the URL with your GitHub Pages URL.

- Add a deployment script under the `scripts` section:
  ```json
  "scripts": {
      "deploy": "gh-pages -d build"
  }
  ```

---

## Step 3: Configure Redirects
Create a `_redirect` file in the `public` directory with the following content:
```
/*    /index.html   200
```
This ensures proper routing for single-page applications (SPA).

---

## Step 4: Update GitHub Repository Settings
Navigate to **Settings > Actions > General** in your GitHub repository. Under **Workflow permissions**, enable **Read and write permissions**.

---

## Step 5: Configure Node.js Actions in GitHub
1. Go to the **Actions** tab in your repository.
2. Search for **Node.js** workflows and click **Configure**.

---

## Step 6: Set Up CI/CD Workflow
Create a new file `.github/workflows/node.js.yml` and add the following configuration:

```yaml
on:
  push:
    branches: ["branches names that want to run ci/cd"]
  pull_request:
    branches: ["branches names that want to run ci/cd"]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '16'

      - name: Install dependencies
        run: npm install

      - name: Build project
        run: npm run build

      - name: Deploy with gh-pages
        if: github.ref == 'refs/heads/{GITHUB REPOSITORY}' # change {GITHUB REPOSITORY}  with your branch name
        run: |
          git remote set-url origin https://x-access-token:${{ secrets.GITHUB_TOKEN }}@github.com/${{ github.repository }}.git
          npm run deploy -- -u "github-actions-bot <support+actions@github.com>"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Replace `testbranch` with the name of the branch you want to use for CI/CD.

---

## Step 7: Build and Deploy for the First Time
Run the following commands locally:

```bash
npm run build
npm run deploy
```

---

Your React application is now set up for deployment to GitHub Pages with CI/CD!
