# Creating a React app and deploying to AWS

I'm going to show how to:
- Create a base React app using [Create React App](https://create-react-app.dev/)
- Deploy that app to AWS using [Amplify](https://aws.amazon.com/amplify/)
- Connect your domain name, and enable TLS/SSL (https)

If you created a non-React site for this phase, the instructions for deployment work exactly the same, so just skip the React set up section.

I'm going to be creating the cat picture React app that I showed in the last guide, this time in TypeScript, and I want to make it accessable at [https://cat-pictures-react.texasvotes.me](https://cat-pictures-react.texasvotes.me):
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

Make an AWS account if you haven't yet and go to [AWS Amplify](https://aws.amazon.com/amplify/). 

Then click on "New app" > "Host web app", choose GitLab as your Git host, and then continue.

![Screen Shot 2021-09-24 at 9 01 43 AM](https://user-images.githubusercontent.com/8890739/134707228-2dd22b75-e90b-4316-b62f-82894d825469.png)

Choose your repo for the project and keep main as the default branch. Then check that your repo is a monorepo (since we'll be adding a back-end in phase 2), and type the name of the folder that your front-end code is in. It should be `front-end` if you followed the first part of this guide.

![Screen Shot 2021-09-24 at 9 02 17 AM](https://user-images.githubusercontent.com/8890739/134707311-2ac826ed-0c9e-4772-a46f-0a18a12d1e2d.png)

Click through the rest of the screens, and then your site should deploy! Any time you push changes to your main branch, the site will automatically redeploy.

Next, we want to make a seperate development site that is connected to your develop branch.

Go to the general page of your app on AWS Amplify, and then click the "Connect Branch" button. Select your develop branch, then save and deploy. Now you have a another deployment that is independent from your production deployment. This allows your group to keep developing and seeing changes online by pushing to the develop branch, and leaving the main branch alone.

This is what you should see when you're done:

![image](https://user-images.githubusercontent.com/8890739/134708968-1840c13c-ecb8-4c3b-a97c-1b48ec401ebc.png)

## Connecting domain name and enabling TLS/SSL

Now we want to set up your domain name so that when you type it in your browser, you actually go to the site that you just deployed. AWS Amplify makes this process easier.

Go to the "Domain Management" tab in the App settings, and then click on "Add domain". Type the your domain name, then click "Configure domain". Leave everything as is, then hit save.

![image](https://user-images.githubusercontent.com/8890739/134710500-4ca4ea97-0547-4e0d-95bb-21f9eae7f904.png)

Now AWS needs to confirm that you own your domain, so they can issue you a TLS/SSL certificate to enable https. This should pop up in your Domain Management tab:

![Screen Shot 2021-09-23 at 5 28 10 AM](https://user-images.githubusercontent.com/8890739/134710743-c63fffd3-a074-4c39-8767-5a9344b5c85d.png)

Go to your domain name registrar, and add the CNAME record that AWS asks you to:

![Screen Shot 2021-09-24 at 11 43 18 AM](https://user-images.githubusercontent.com/8890739/134711384-0bc3e054-0e70-4e23-9a8b-9ddd5054b57f.png)

**NOTE**: Some domain name registrars automically include your domain name at the end of the CNAME record. For example, on Google domains, if you copy and paste `abc123.website.com` as the host name, Google will automatically add `website.com` to the end, making it `abc123.website.com.website.com`, which is obviously not what you want.

After a couple of minutes to upwards of an hour, depending on your domain registar and DNS propagation, AWS will verify your domain name ownership and grant you a TLS/SSL certificate.

Right after, AWS will provide another CNAME and alias record to add. The destination of these DNS records should be a `cloudfront.net` domain. Add those, wait a bit for the new records to propogate, and you should now be able to visit your website at its domain.

This is what my DNS records looked like when I was done with everything. Google domains doesn't support alias records, so I had to set up a domain forward so when anyone goes to `texasvotes.me`, it redirects to `www.texasvotes.me`.

<img width="677" alt="Screen Shot 2021-09-24 at 12 57 24 PM" src="https://user-images.githubusercontent.com/8890739/134720227-b1fce32d-21dd-4664-87c2-9a4978fb26fb.png">

The site at [https://cat-pictures-react.texasvotes.me](https://cat-pictures-react.texasvotes.me) is now available to the entire world!
