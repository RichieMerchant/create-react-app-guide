# Create React App - Getting Started

## Creating a new project

- `npx create-react-app my-app --template typescript`
- Remove eject npm script
  - Delete this line in the scripts section of the `package.json` file
    - `"eject": "react-scripts eject"`
- `npm start`
- Visit: http://localhost:3000

#### Understanding CRA

- Folder structure: https://create-react-app.dev/docs/folder-structure
- Available scripts: https://create-react-app.dev/docs/available-scripts
- Browser support: https://create-react-app.dev/docs/supported-browsers-features

## Add project to GitHub

- Create a new Repo

  - Add and and description
  - Make it public or private
  - DO NOT initialise with .gitignore, README file or License

- Push project to Github
  - Git should already be initialse in you project, but to initialse a project with git you can run the following command. `git init`
  - Most project initialse with a default branch called master. But for Github and some other version control platform. The master branch has been discontinued and renamed to main. So lets rename our branch if its not called main already.
    - `git branch -M main`
  - Add the remote origin. This tells the project where the git repository is for push and pull
    - `git remote add origin git@github.com:<Github username>/<repository name>.git`
  - Push the project to your newly created repository
    - `git push -u origin main`

### Conclusion

This consludes this section of the guide. Next we will be looking at [setting up Eslint](https://github.com/RichieMerchant/create-react-app-guide/blob/main/docs/eslint-setup.md).
