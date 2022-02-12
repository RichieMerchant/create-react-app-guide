# Eslint

### Do I need a custom ESLint configuration?

Probably not. Because Create React App comes with ESLint already integrated. They use their own [sharable ESLint configuration](https://github.com/facebook/create-react-app/tree/v4.0.3/packages/eslint-config-react-app) and this can be found under the **eslintConfig** object in `package.json`.

```json
"eslintConfig": {
	"extends": [
		"react-app",
		"react-app/jest"
    ]
},
```

For this project we will be utilising the config that comes with Create React App. We will then extend the config to add our own rules.

### Custom configuration

#### Remove the existing config

Go to `package.json` at the root of the project, and remove the **eslintConfig** object. The

```json
"eslintConfig": {
	"extends": [
		"react-app",
		"react-app/jest"
    ]
},
```

#### Create Eslint config

Create the eslint config `eslintrc.json` at the root of the project with the following content.

```json
{
  "extends": ["react-app", "react-app/jest"]
}
```

The reson that I have moved the Eslint config from the `package.json` file into a file called `eslintrc.json`, is because when we add our own rules, this section will become much larger and makes the `package.json` file a bit messy.

#### Create Eslint Ignore file

Create a file called `.eslintignore` and add the following content

```
node_modules
public
build
```

#### Create tsconfig for Eslint

Create a file calles `tsconfig.eslint.json` at the root of the project and add the following:

```json
{
  "extends": "./tsconfig.json",
  "include": ["src/**/*.ts?(x)", ".eslintrc.js"]
}
```

And add `project: './tsconfig.eslint.json',` to `parserOptions` in `eslintrc.js`.

```json
parserOptions: {
    project: './tsconfig.eslint.json',
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
```

#### Add Prettier code formatter

Install Prettier and the Prettier Eslint config and plugin:
`npm install prettier eslint-config-prettier eslint-plugin-prettier --save-dev`

Update the the .eslintrc.json file with the following:

```json
{
  "extends": ["react-app", "react-app/jest", "plugin:prettier/recommended"]
}
```

Create a `.prettierrc` file at the root of the project and add the following content.

```json
{
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "quoteProps": "as-needed",
  "jsxSingleQuote": false,
  "trailingComma": "es5",
  "bracketSpacing": true,
  "bracketSameLine": false,
  "arrowParens": "always"
}
```

_Feel free to adjust these setting to your liking._

Create a `.prettierignore` file at the root. This should contain the same content as the `.eslintignore` file.

```
node_modules
public
build
```

#### Add lint script to NPM package.json

```json
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "lint": "eslint \"**/*.{ts,tsx,js,jsx}\"",
    "lint:fix": "eslint \"**/*.{ts,tsx,js,jsx}\" --fix",
    "format": "prettier --write './**/*.{js,jsx,ts,tsx,css,md,json}' --config ./.prettierrc"
},
```

If you run the following command `npm run lint` you should see a few errors. You can auto fix them by running the following command: `npm run lint:fix`.

#### Configure VSCode

Install the [Eslint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) and [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) extensions

Create a folder called `.vscode` at the root of your project. Inside this folder, create a file and add the content below.

```json
{
  "editor.formatOnPaste": true,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.fixAll.format": true
  }
}
```

This enables Eslint for the project and formats your code when you save.

### Conclusion

This completes the Eslint setup. You should now commit these changes to your git repository.
Next we will be looking as setting up Husky and Lint Staged.
