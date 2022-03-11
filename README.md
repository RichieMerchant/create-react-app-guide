# Github Actions

GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

## Creating a workflow

1.  Create a `.github/workflows` directory in your repository on GitHub if this directory does not already exist.
2.  In the `.github/workflows` directory, create a file named `semantic-release.yml`.
3.  Copy the following YAML contents into the `semantic-release.yml` file:

```yaml
name: Semantic release

on:
  push:
    branches:
      - main

jobs:
  tag:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout project
        uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 14
      - name: Install dependencies
        run: npm ci
      - name: Semantic release
        env:
          GITHUB_TOKEN: ${{ secrets.GH_TOKEN }}
        run: npx semantic-release
```

Lets go through the Github actions yaml to understand what its doing.

- `name: Semantic release`: This is the name displaying within Githubs Actions UI.
- `branches: - main`: This tells the action when to run. This action will run when a git push occurs on the main branch.
- `runs-on: ubuntu-latest`: The OS that the container will use ti run the action.
- `uses: actions/checkout@v3`: Use the Github checkout actions to checkout the project into the container
- `uses: actions/setup-node@v2`: Installs Node.js on the container. `node-version: 14` is used to install a Node.js version 14.
- `run: npm ci`: Install node packages
- `run: npx semantic-release`: Run the semantic release config to determin if a new tag should be created.

### Conclusion

Once you merge this into the main branch, any push to main will trigger this Github action. Next we will be looking at introducing a [Tailwind CSS](https://github.com/RichieMerchant/create-react-app-guide/blob/main/docs/tailwind-css-setup.md).
