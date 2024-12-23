# CI-CD-Document

## Deploy Application with CI/CD and GitHub Pages

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
If you use react.js create a `_redirect` file in the `public` directory with the following content:
```
/*    /index.html   200
```

This ensures proper routing for single-page applications (SPA). for next.js no need to do this.

---

## Step 4: Update GitHub Repository Settings
Navigate to **Settings > Actions > General** in your GitHub repository. Under **Workflow permissions**, enable **Read and write permissions**.

---

## Step 5: Configure Node.js Actions in GitHub
1. Go to the **Actions** tab in your repository.
2. For react.js or next.js search for **Node.js** workflows and click **Configure**.

> [!TIP]
>   if you want to run other frameworks, choose your framework language like php, java, ...

---

## Step 6: Set Up CI/CD Workflow
Edit file `.github/workflows/node.js.yml` - made in previous step by configuring your action - and add the following configuration:

```yaml
on:
  push:
    branches: ["branches names that want to run ci/cd"]
  pull_request:
    branches: ["branches names that want to run ci/cd"]

jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]  # choose your os for running ci/cd
        node-version: [20.x, 22.x]           # choose node.js version you want to run ci/cd

    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: "npm"

      - name: Install dependencies
        run: npm install --force

      - name: Build project
        run: npm run build

      - name: Run tests
        run: npm test -- --watchAll=false    #  change with your test command

      #  You can add your other commands here

      - name: Deploy with gh-pages
        if: github.ref == 'refs/heads/{GITHUB BRANCH}' # change {GITHUB BRANCH}  with your branch name
        run: |
          git remote set-url origin https://x-access-token:${{ secrets.GITHUB_TOKEN }}@github.com/${{ github.repository }}.git
          npm run deploy -- -u "github-actions-bot <support+actions@github.com>"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

> [!NOTE]
> Replace `{GITHUB BRANCH}` with the name of the branch you want to use for CI/CD.
>
> when you push in your `{GITHUB BRANCH}` all commands will run automatically.
 

---

## Step 7: Make sure about GitHub Pages Configuration
Navigate to **Settings > Pages** in your GitHub repository. Under **Build and deployment**,  **Source** should be **Deploy from branch** and  **Branch** should be **gh-pages**.

Also you can visit you live site address there.

---

## Step 8: Build and Deploy for the First Time
Run the following commands locally:

```bash
npm run build
npm run deploy
```

---

Your React application is now set up for deployment to GitHub Pages with CI/CD! :heart_eyes:
Now after every push in specified branch all of your cammands in yml file will runs automatically. :+1:



## How to know CI/CD works properly?

1. On GitHub go to your branch:
   - If you see $${\color{orange} yellow \space dot}$$, it means that CI/CD is running.
   - If you see ❌, it means that CI/CD actions failed.
   - Otherwise, if you see ✅, it means that your CI/CD actions ran successfully.

2. On your Github account go to action tab and see CI/CD running proccess details.
