# IDB Tips Phase 1

These are some tips for approaching and implementing phase 1 of the IDB project. Some of these are opinionated, and we will <ins>**not**</ins> take off points for not following these tips (unless they’re defined in the rubric). If you have prior experience with a different method or approach than what’s stated, and feel that it would be more effective, feel free to use that instead.

If there are any errors or if there is something you want to add, create a pull request to this repo.

## General
- Start early! A lot of the work for this project is not conceptually hard, but it requires a solid amount of time to implement.
- Google is your friend. I’m sure we’re all aware of this as CS majors, but this holds especially true for this course.
- Look at other/past groups' repos. There is a lot of valuable information in how to approach certain problems.
- Phase 2 is a *lot* of work, so setting up a good foundation in phase 1 will help you immensely going forward.

## Front-end
- We recommend using [React](https://reactjs.org/) in phase 1 for the following reasons:
    - You will have to redo your entire front-end code base for phase 2 if you don't. A lot of groups when I took the class said they wished that they used React from the start.
    - You will get more time getting comfortable with React. React has a bit of a learning curve to it, so greater time exposed will definitely help.
    - You can set up automatic front-end deployment in GitLab in phase 1, which will make deploying much, much simpler.
- Utilize [TypeScript](https://www.typescriptlang.org/) when making your React app. TypeScript is just JavaScript, but with you guessed it: types! The static typing and intellisense that TypeScript provides is well worth the additional learning curve as your project grows in complexity. Furthermore, much of industry is switching from JavaScript to TypeScript.
- Use a linter like [ESLint](https://eslint.org/) and a code formatter like [Prettier](https://prettier.io/). This is like mypy and black for Python respectively. ESLint catches errors and Prettier ensures consistent formatting, regardless of the conventions of each individual developer. I found the auto-indentation feature in Prettier to be very helpful with nested HTML tags that you will deal with.
- Async/await/promises in JavaScript is a concept that you (most likely) haven't been exposed to in Java, Python, C, or C++. It's much more common in JavaScript. In phase 1, you'll use this when calling GitLab's API. Here's a video that I think does a great job at explaining it: https://www.youtube.com/watch?v=vn3tm0quoqE
- If your group has little/no prior CSS experience, a framework like [React Bootstrap](https://react-bootstrap.github.io/), [Material-UI](https://next.material-ui.com/), or [Ant Design](https://ant.design/) provides a lot of built in utility and will make putting together your website a lot quicker, albeit at the cost of original design.
- Get familiar with the CSS [box model](https://www.w3schools.com/css/css_boxmodel.asp), [flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/), and [grid](https://css-tricks.com/snippets/css/complete-guide-grid/).

## Project Management
- Follow some form of [Git flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow). What you *absolutely don't* want to do is have everyone pushing to the same branch at the same time. Git flow typically entails:
    
    ![image](https://user-images.githubusercontent.com/8890739/134418139-624e8ae1-8534-4c28-bfc6-69ef9e373ccb.png)
    
    - Having feature branches
        - For each feature/issue you want to implement, you make a separate branch that branches off from develop.
        - E.g. I want to implement the about page, which is issue #12 on GitLab. I make a branch called “feature/12-about” based off the branch “develop”. You would then commit your changes to the feature branch, and when you’re done implementing the feature you make a merge request back to develop.
    - Having a develop branch
        - This where all of the feature branch merges into. Consider this your beta code base where you can see if new things you implemented cause any issues.
    - Having a production (master) branch
        - When you're ready to deploy, you merge develop into master. All code that is on this branch should be considered ready to be used by the general public.
        - If you add automatic deployment (I will mention this below), your deployment process will be as simple as merging develop into production!
- If you're going to use Git flow, I would also recommending setting your GitLab repo settings so that:
    - No one is allowed to push to develop or master directly
    - Developers are allowed to merge to develop
    - No one is allowed to merge into master except for one trusted individual. The group must come to an agreement before this person merges to master.
    ![image](https://user-images.githubusercontent.com/8890739/134420218-b0a8797c-5d30-4e90-9461-d0906f495f54.png)

- Set up a card view of your GitLab issues for the phase.
    ![image](https://user-images.githubusercontent.com/8890739/134420492-354e10e5-448e-4726-81c0-d779677eba38.png)
    - I would create a milestone for each phase, and then create a seperate board for each phase. This is so you can see only the issues you need to implement for a given phase.
    - Create labels like "To Do", "In Progress", and "QA", which indicate the status of each issue, and then create lists for those labels. You can drag and drop issues into these lists as they change status. This gives a clear view of what needs to be done, who's currently working on what, and what is already finished.
- Do code reviews through merge requests
    - When someone in your group wants to merge a feature branch into develop. Create a merge request and have another person take a look at the code for any obvious errors. A second set of eyes always helps.
    ![image](https://user-images.githubusercontent.com/8890739/134427225-d3117223-3cef-4376-b5c4-affda6c7a0e5.png)

## Deployment
- Learn which AWS services you will need, and what each of them does. The ones I recommmend first looking at are:
    - Amplify for front-end deployment
    - Certificate manager for TLS/SSL certificate (in order to get `https`)
    - EC2 for back-end deployment (phase 2)
    - RDS for database hosting (phase 2)
- Set up automatic deployment on AWS through GitLab pipelines
    - AWS Amplify has useful GitLab integration with React apps
- Have a separate development and production deployment
    - We require there to be no changes to production for 7 days after submission to allow for grading. Having two seperate deployments (e.g. website.com and dev.website.com) allows you to keep developing while we grade your project.
    - If you're using AWS Amplify, it's very easy to set this up. Just connect your develop branch, and it will automatically deploy every time you update that branch!
    ![image](https://user-images.githubusercontent.com/8890739/134426838-7bfdd431-89ca-4828-b21d-de336d91a4ef.png)

## Helpful Links
- [Create React App - Getting Started](https://create-react-app.dev/docs/getting-started) - You'll need to use Create React App to bootstrap your React project. (Next.js is a great framework, but its complexity compared to CRA, as well as the fact that we want a SPA w/ client-side rendering necesitates the use of CRA).
- [React Router](https://reactrouter.com/) - You'll need this to implement routing between pages in your React app.
- [axios](https://axios-http.com/) - HTTP client that provides more configuration than JavaScript's `fetch` function. Will be particularly useful in phase 2 and 3.
- [Fireship](https://www.youtube.com/c/Fireship/videos) is a great YouTube channel that has a lot of concise, information packed videos. Here are some good ones to watch:
    - [React in 100 seconds](https://www.youtube.com/watch?v=Tn6-PIqc4UM)
    - [JavaScript Promise in 100 seconds](https://www.youtube.com/watch?v=RvYYCGs45L4)
    - [JavaScript Survival Guide](https://www.youtube.com/watch?v=9emXNzqCKyg)
    - [Async/Await in JavaScript](https://www.youtube.com/watch?v=vn3tm0quoqE)
    - [React Hooks Explained](https://www.youtube.com/watch?v=TNhaISOUy6Q)
    - [JavaScript under the hood](https://www.youtube.com/watch?v=FSs_JYwnAdI)
    - [Why to use TypeScript in React](https://www.youtube.com/watch?v=ydkQlJhodio)
    - [AWS Services Explained](https://www.youtube.com/watch?v=JIbIYCM48to)
