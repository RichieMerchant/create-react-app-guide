# Tailwind CSS

## Install Tailwind CSS

**Install Tailwind CSS**
Install `tailwindcss` and its peer dependencies via npm, and then run the init command to generate both `tailwind.config.js` and `postcss.config.js`.

```shell
npm install --save-dev tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**Configure your template paths**
Add the paths to all of your template files in your `tailwind.config.js` file.

```shell
module.exports = {
  content: [
    "./src/**/*.tsx",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

### Including Tailwind in your CSS

Inside your `src` folder create a folder named `styles`. This is where all your styles will be stored.

Inside that folder, create a `tailwind.css` and an `index.css` file.

The `index.css` file is where we’ll import tailwind’s base styles and configurations. `tailwind.css` will contain the compiled output of the `index.css`.

Add the following to the `.gitignore` file:

```
# styles
tailwind.css
```

#### Tailwind CSS components, utilities, and base styles

add the following to your `index.css` file.

```css
//index.css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

`@tailwind` is a tailwind directive that is used to inject default `base styles`, `components`, `utilities` and custom configurations.

`@tailwind base` \*\*injects Tailwind’s [base styles](https://unpkg.com/tailwindcss@2.0.3/dist/base.css), which is a combination of `[Normalize.css](https://necolas.github.io/normalize.css/)` and some additional base styles.

`@tailwind components` injects any component (small reusable styles such as buttons, form elements, etc.) classes registered by plugins defined in your tailwind config file.

`@tailwind utilities` injects all of Tailwind’s utility classes (including the default and your utilities), which are generated based on your config file.

#### Configure your app to build your CSS file

To configure your app to build your styles every time you run the `npm start` command, open your `package.json` file and replace the content of `"scripts"` with:

```json
 "scripts": {
    "start": "concurrently --kill-others \"npm run build:css -- --watch\" \"react-scripts start\"",
    "build": "npm run build:css && react-scripts build",
    "build:css": "tailwind build -i ./src/styles/index.css -o ./src/styles/tailwind.css",
    "test": "react-scripts test",
    "test:staged": "cross-env CI=true react-scripts test --env=jsdom --findRelatedTests",
    "lint": "eslint \"**/*.{ts,tsx,js,jsx}\"",
    "lint:fix": "eslint \"**/*.{ts,tsx,js,jsx}\" --fix",
    "format": "prettier --write './**/*.{js,jsx,ts,tsx,css,md,json}' --config ./.prettierrc",
    "prepare": "husky install"
  },
```

Install a package called [concurrently](http://npmjs.org/package/concurrently).

```shell
npm install --save-dev concurrently
```

To import your CSS to the app, open your `index.tsx` file and import your Tailwind styles:

```jsx
import './styles/tailwind.css';
```

Your `index.tsx` file should now look similar to this:

```jsx
//index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './styles/tailwind.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
...
```

Delete the `App.css` from `App.tsx`.

```jsx
import './App.css';
```

Your `App.tsx` file should now look similar to this:

```jsx
function App() {
  return <h1 className="text-3xl font-bold underline">Hello world!</h1>;
}

export default App;
```

Delete the following files:

```shell
rm ./src/App.css ./src/index.css ./src/logo.svg
```

### Conclusion

There was a lot covered in the Tailwind CSS setup and it ony covers a fraction of what Tailwind CSS can do. I've included some resources that I think its worth going through before moving on to the next guide, [setting up Craco](https://github.com/RichieMerchant/create-react-app-guide/blob/main/docs/craco-setup.md).

[Tailwind CSS - Create react App docs](https://tailwindcss.com/docs/guides/create-react-app)
[Tailwind CSS - Adding custom styles](https://tailwindcss.com/docs/adding-custom-styles)
[Tailwind CSS - official docs](https://tailwindcss.com/docs/installation)

PS. Don't forget to update the test in `App.test.tsx` 😉
