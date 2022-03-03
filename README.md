# Semantic Versioning

In this guild, I will walk you through setting up semantic versioning.

### What is semantic versioning

> To keep the JavaScript ecosystem healthy, reliable, and secure, every time you make significant updates to an npm package you own, we recommend publishing a new version of the package with an updated version number in the [`package.json` file](https://docs.npmjs.com/creating-a-package-json-file) that follows the [semantic versioning spec](http://semver.org/). Following the semantic versioning spec helps other developers who depend on your code understand the extent of changes in a given version, and adjust their own code if necessary.
> https://docs.npmjs.com/about-semantic-versioning

#### How does this relate to this project?

This will allow you to follow a workflow that performs fully automated releases for Github and enforces the [Semantic Versioning](https://semver.org/) specification based on your commit messages.

### Semantic Release

To achive this for our project, we will be using an NPM package called [semantic-release](https://github.com/semantic-release/semantic-release).

> **semantic-release** automates the whole package release workflow including: determining the next version number, generating the release
> notes, and publishing the package.
>
> This removes the immediate connection between human emotions and
> version numbers, strictly following the [Semantic
> Versioning](http://semver.org/) specification and communicating the
> **impact** of changes to consumers.

The table below shows which commit message gets you which release type when `semantic-release` runs (using the default configuration):

| Commit message                                                                                                                                                                                   | Release type                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| `fix(pencil): stop graphite breaking when too much pressure applied`                                                                                                                             | ~~Patch~~ Fix Release                                                                                           |
| `feat(pencil): add 'graphiteWidth' option`                                                                                                                                                       | ~~Minor~~ Feature Release                                                                                       |
| `perf(pencil): remove graphiteWidth option`<br><br>`BREAKING CHANGE: The graphiteWidth option has been removed.`<br>`The default graphite width of 10mm is always used for performance reasons.` | ~~Major~~ Breaking Release <br /> (Note that the `BREAKING CHANGE: ` token must be in the footer of the commit) |

We'll be using tools such as [commitizen](https://github.com/commitizen/cz-cli) and [commitlint](https://github.com/conventional-changelog/commitlint) to enforce valid commit messages.

## Installing Semantic Release

```shell
npm install --save-dev @semantic-release/git @semantic-release/changelog
```

## Github token

A Github token must be created in order for Semantic Release to be able to publish a new release to the Github repository.

You can read [here](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) how to create a token for Github. You need to give the token repo scope permissions.

Once you have the token, you have to save it in your repository secrets config:

If you are using an different code respository other than GitHub please refer to [this guide](https://github.com/semantic-release/semantic-release/blob/master/docs/usage/ci-configuration.md#ci-configuration) on seeting up an access token

```
https://github.com/<username>/<repositoryname>/settings/secrets
```

Use `GH_TOKEN` as the secret name.

## Conifiguring Semantic Release

```shell
# Create config file

touch .releaserc
```

```
# Add commit analiser preset. This tool is used to analise the commit messages since to last tag.

npm install --save-dev conventional-changelog-conventionalcommits
```

Add the following content to the `.releaserc` file.

```json
{
  "branches": ["main"],
  "plugins": [
    [
      "@semantic-release/commit-analyzer",
      {
        "preset": "conventionalcommits"
      }
    ],
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/npm",
    "@semantic-release/github",
    [
      "@semantic-release/git",
      {
        "assets": ["CHANGELOG.md", "package.json", "package-lock.json"],
        "message": "chore(release): ${nextRelease.version} [skip ci]\n\n${nextRelease.notes}"
      }
    ]
  ]
}
```

Let go over what is happening in `.releaserc` file.

- branches: This defines what branches will trigger the semantic release command that will create a release tag.
- Plugins
  - `@semantic-release/commit-analyzer`: plugin analises all commits since the last tag was created. It uses the `conventionalcommits` plugin that expects the commit messages to be formatted in a certain way.
  - `@semantic-release/release-notes-generator`: plugin to generate changelog content
  - `@semantic-release/changelog`: plugin to create or update a changelog file
  - `@semantic-release/npm`: plugin to publish a [npm](https://www.npmjs.com/) package. Is this instance, we are using it to update the version on the `package.json` file.
  - `@semantic-release/github`: plugin to publish a [GitHub release](https://help.github.com/articles/about-releases) and comment on released Pull Requests/Issues
  - `@semantic-release/git`: plugin to commit release assets to the project's [git](https://git-scm.com/) repository

## Setup Commitizen

When you commit with Commitizen, you'll be prompted to fill out any required commit fields at commit time.

```shell
# Install Commitizen

npm install commitizen
```

```shell
# Initialize your project to use the cz-conventional-changelog adapter by typing

commitizen init cz-conventional-changelog --save-dev --save-exact
```

The above command does three things for you:

1.  Installs the cz-conventional-changelog adapter npm module
2.  Saves it to `package.json`'s `dependencies` or `devDependencies`
3.  Adds the `config.commitizen` key to the root of your `package.json` file as shown here:

```json
"config": {
    "commitizen": {
      "path": "./node_modules/cz-conventional-changelog"
    }
  }
```

Running `git commit` will be presented with an interactive Commitizen session that helps them write useful commit messages.

## Setup Commitlint

### What is commitlint

Commitlint checks if your commit messages meet the conventional commit format.

In general the pattern mostly looks like this:

```shell
type(scope?): subject  #scope is optional; multiple scopes are supported (current delimiter options: "/", "\" and ",")
```

Real world examples can look like this:

```shell
chore: run tests on travis ci
```

```shell
fix(server): send cors headers
```

```shell
feat(blog): add comment section
```

Common types according to [commitlint-config-conventional (based on the Angular convention)](https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-conventional#type-enum) can be:

- build
- chore
- ci
- docs
- feat
- fix
- perf
- refactor
- revert
- style
- test

```shell
# Install commitlint cli and conventional config

npm install --save-dev @commitlint/{config-conventional,cli}
```

```shell
# Add hook

npx husky add .husky/commit-msg "npx --no-install commitlint --edit $1"
```

Sometimes above command doesn't work in some command interpreters

ensure the file .husky/commit-msg has the follwing content:

```js
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx --no-install commitlint --edit $1
```

```shell
# Configure commitlint to use conventional config

echo  "module.exports = { extends: ['@commitlint/config-conventional'] };"  > commitlint.config.js
```

This will enforce commit messages to have the correct fields.
