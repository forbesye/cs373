# Creating a React app and deploying to AWS

I'm going to show how to create a base React app using [Create React App](https://create-react-app.dev/), deploy that app to AWS using [Amplify](https://aws.amazon.com/amplify/), connect your domain name, and enable TLS/SSL (https). If you created a non-React site for this phase, the instructions for deployment work exactly the same, so just skip the React set up section.

I'm going to be creating the cat picture React app that I showed in the last guide, this time in TypeScript:
| ![Screen Shot 2021-09-24 at 10 58 57 AM](https://user-images.githubusercontent.com/8890739/134705548-f8622a9b-2002-4e01-ac85-0ba7bf7c9df5.png) | 
|:--:| 
| *The final product!* |
  
## Setting up a React app

Before starting, you need to install [Node.js](https://nodejs.org/en/) on your machine.

Then, clone your GitLab repository, and `cd` into that folder.

Run `npx create-react-app front-end`
- If using TypeScript, run `npx create-react-app front-end --template typescript`

`cd` into the new folder that was just created, `front-end`. I'm going to install some packages. I'm going to start out with React-Bootstrap, Prettier, and ESLint.
  - `yarn add react-bootstrap@next bootstrap@5.1.1`
  - `yarn add eslint --dev`
  - `yarn add --dev --exact prettier`
  - You're going to have to do some additional configuration of ESLint and Prettier to get it to automatically check and format

Start the development environment with `yarn start`. It'll open up by default at `localhost:3000` in your browser.

This is what you should see initially:

![Screen Shot 2021-09-24 at 9 22 53 AM](https://user-images.githubusercontent.com/8890739/134705262-ce75b1a1-1a38-4c79-b8c4-17e962ffe756.png)

I'm going to add my code for the project and clean up some of the starter files that I don't need. If you want to take a look you can access the repo [here](https://gitlab.com/forbesye/cat-pictures-react).

That should be good enough to deploy now, let's do that!
   
## Deploying on AWS Amplify



## Connecting domain name and enabling TLS/SSL

