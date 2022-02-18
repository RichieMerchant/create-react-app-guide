# Husky and Lint Staged

Lint staged helps you keep commited code clean and free from linting issues.

> Linting makes more sense when run before committing your code. By doing so you can ensure no errors go into the repository and enforce code style. But running a lint process on a whole project is slow, and linting results can be irrelevant. Ultimately you only want to lint files that will be committed.

Husky makes it really easy to tap into Git hooks to run scripts during various git commands. Read more about git hooks [here](https://githooks.com/).

## Installation and setup

Because Create React App has Eslint preinstalled, we are unable to use the helper command `npx mrm@2 lint-staged` that would install Husky and Lint Staged for us. Instead, we will install and set them up manually.

### Install and setup Lint Staged

```
npm install lint-staged tsc-files --save-dev
```

_We use `tsc-files` to run `tsc` on specific files without ignoring `tsconfig.json`_

#### Add a lint staged configuration to package.json

```json
// package.json
{
  "name": "create-react-app-guide",
  "version": "0.1.0",
  ...
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "lint": "eslint \"**/*.{ts,tsx,js,jsx}\"",
    "lint:fix": "eslint \"**/*.{ts,tsx,js,jsx}\" --fix",
    "format": "prettier --write './**/*.{js,jsx,ts,tsx,css,md,json}' --config ./.prettierrc"
  },
  "lint-staged": {
	  "**/*.ts?(x)": "tsc-files --pretty --noEmit",
	  "*.{ts,tsx,js,jsx}": "eslint --fix",
	  "*.{js,jsx,ts,tsx,md,json}": "prettier --write"
  }
}
```

**Whats are these 3 `lint-staged` scripts doing?**

1. `"**/*.ts?(x)": "tsc-files --pretty --noEmit"`: This script runs a Typescript check
2. `"*.{ts,tsx,js,jsx}": "eslint --fix"`: Fixes any eslint issues.
3. `"*.{js,jsx,ts,tsx,md,json}": "prettier --write"`: Formats the files using the prettier config

**_A couple things worth mentioning:_**

1.  If any of these steps fail, the git process fails. In this case I will be running these scripts using the git commit hook so the `git commit ...` command will fail.
2.  It doesn't matter what order you run these scripts but I prefer to run them in a logical order with pretter running last, it just makes sense to me :)
3.  These scripts will only be run on files that have been added to the commit. If you have made changes to a file but it is not part of the commit, it will be ignored.

### Install and setup Husky

Husky has is a one-time command to quickly initialize a project with husky. `npx husky-init && npm install`. For the purpose of this guide, I will be using the manual setup so that it clear what Husky is doing.

1.  Install `husky`
    `npm install husky --save-dev`

2.  Enable Git hooks
    `npx husky install`

3.  To automatically have Git hooks enabled after install, edit `package.json`
    `npm set-script prepare "husky install"`

You should have:

```json
// package.json
{
	"scripts": {
		...
		"prepare": "husky install"
	}
}
```

#### Create a hook

```
npx husky add .husky/pre-commit "npx lint-staged"
```

Adding this pre commit script tells Husky to run the `lint-staged` scripts in the `package.json` file.

#### Commit files to repository

```shell
git add .
git commit -m "add lint-staged and husky"
```

You should see Husky run `lint-staged`. Because there are no files that match the scripts in the` lint-staged` scripts, it should just pass successfully without actually running any of the scripts.

Lets make a change to a file that will trigger the `lint-staged` scripts. Update the `App.tsx` file with the following code.

```js
import React from "react";
import "./App.css";

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <p>
          Install <code>Husky</code> and <code>Lint Staged</code>.
        </p>
      </header>
    </div>
  );
}

export default App;
```

commit `App.tsx`

```shell
git add src/App.tsx
git commit -m "Update App.tsx"
```

#### Test staged files

Lets add an extra step to lint-stages so that its runs test on changed files.

1.  Install cross-env
    `npm install cross-env --save-dev`

2.  Add a new npm script to run tests on staged files.
    `"test:staged": "cross-env CI=true react-scripts test --env=jsdom --findRelatedTests",`
    Because Create React App runs test in watch mode, we add the environment variable `CI=true` to stop tests running in watch mode. The flag `--findRelatedTests` ensures tests that match the staged files are all run. For example, `App.test.tsx` will run because the file `App.tsx` was matched.

3.  Update the `lint-staged` scripts

```json
// package.json
{
  "lint-staged": {
    "**/!(*test).ts?(x)": "tsc-files --pretty --noEmit",
    "*.{ts,tsx,js,jsx}": ["eslint --fix", "npm run test:staged --"],
    "*.{js,jsx,ts,tsx,md,json}": "prettier --write"
  }
}
```

There are a couple changes to mention.

- `"**/!(*test).ts?(x)"`: tsc-files glob has been updated to exclude test files
- `npm run test:staged --` has been added to the `"*.{ts,tsx,js,jsx}"` glob in an array. Lint staged will run the commands in sequence.

4. Update App.test.tsx with the following:

```js
import React from "react";
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders learn react link", () => {
  render(<App />);
  const linkElement = screen.getByText(/lint staged/i);
  expect(linkElement).toBeInTheDocument();
});
```

#### Commit files to repository

```shell
git add .
git commit -m "run test on staged files"
```

Jest should run when you commit your files.

### Conclusion

This completes the Husky and Lint Stahed setup. You should now commit these changes to your git repository.
Next we will be looking as setting up semantic versioning.
