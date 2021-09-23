# Intro to HTML, CSS, JavaScript, and React

<ins>Contents</ins>
- [HTML](#html)
- [CSS](#css)
- [JavaScript](#javascript)
- [React](#react)
- [Useful Links](#useful-links)

## HTML
HTML is the markup language that is the building block of the web. It defines the content and structure of web pages. An HTML document is comprised of **tags** that are nested in a tree-like structure. For example, the following HTML:
```html
<html>
  <head>
    <title>This is a title</title>
  </head>
  <body>
    <div>
        <p>Hello world!</p>
        <p>Ayy lmao</p>
    </div>
  </body>
</html>
```
Yields the following when I open it in a browser:

![image](https://user-images.githubusercontent.com/8890739/134520620-1b727c1d-1a9e-4459-bb14-4e4bfa071c08.png)

Looks a bit plain doesn't it? We'll spice things up with CSS soon!

There are a [lot of HTML tags](https://developer.mozilla.org/en-US/docs/Web/HTML/Element), but the main ones you should know/use are:
- `head` - Contains information that isn't displayed on the browser directly, these include the `title`, `style`, and `meta` tags. If you're using React, you typically won't have to deal with this directly
- `div` - You're gonna use this bad boy a lot. It's a general container
- `body` - Used to define the main body of the web page
- `h1`, `h2`, ..., `h6` - Heading, typically to signify the title of some section
- `p` - Paragraph, used for general text
- `a` - Anchor tag, used to link one page to another page. Uses the `href` attribute to describe the link
  - e.g. `<a href="https://google.com">Google</a>` renders as <a href="https://google.com">Google</a>
- `img` - Used to display images. Uses `src` tag to define path to the image, and `alt` should often be included for accessability to describe the image
  - e.g. `<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cat_poster_1.jpg/640px-Cat_poster_1.jpg" alt="cute cats" />` renders as:
  
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cat_poster_1.jpg/640px-Cat_poster_1.jpg" alt="cute cats" />

## CSS

Now we want to actually make the website look not so plain. This is where CSS comes in. I'm going to make a file called `styles.css`, which is just these few lines:
```css
.blueText {
  color: blue;
  font-size: 24px;
}
```
As you can see, it has the attributes of making something blue, and having the font size of 24px. I can then bring it into my HTML file through the following tag:
```html
<html>
  <head>
    <title>This is a title</title>
    <link rel="stylesheet" href="./styles.css">
  </head>
  <body>
    <div>
        <p class="blueText">Hello world!</p>
        <p>Ayy lmao</p>
    </div>
  </body>
</html>
```
Here you can see that the `link` tag imports the CSS file and I applied the class `blueText` to the Hello world! paragraph. So it ends up looking like this:

<img width="562" alt="image" src="https://user-images.githubusercontent.com/8890739/134543207-1c35ad2e-c4a8-4c9f-9b5d-8914e24b7737.png">

There is a whole lot of possibilities with CSS, but it probably seems pretty daunting to actually end up with something that looks decent. That's where we can use libraries like [Bootstrap](https://getbootstrap.com/) to provide us with premade CSS classes. Bootstrap is essentially just a [big CSS file](https://cdn.jsdelivr.net/npm/bootstrap@5.1.1/dist/css/bootstrap.min.css) (and JavaScript file, but don't worry about that for now). So we can import it the exact same way we imported our small `styles.css` file, except this time `href` is a link to the CSS file on the internet:

```html
<html>
  <head>
    <title>This is a title</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-F3w7mX95PdgyTmZZMECAngseQB83DfGTowi0iMjiWaeVhAn4FJkqJByhZMI3AhiU" crossorigin="anonymous">
  </head>
  <body>
    <div>
        <p>Hello world!</p>
        <p>Ayy lmao</p>
        <button type="button" class="btn btn-primary">Primary</button>
        <div class="alert alert-primary" role="alert">
          A simple primary alert—check it out!
        </div>
    </div>
  </body>
</html>
```

And we can see that the output here is:

<img width="562" alt="image" src="https://user-images.githubusercontent.com/8890739/134545142-1bbf0037-2cf3-4734-840b-1dfaf6e419ca.png">

I added a button and alert element as examples of what Bootstrap provides, but you can see everything by looking at its [documentation](https://getbootstrap.com/docs/5.1).

There's a whole lot more to CSS that I think is important like [cascade](https://developer.mozilla.org/en-US/docs/Web/CSS/Cascade), [specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity#how_is_specificity_calculated), [the box model](https://www.w3schools.com/css/css_boxmodel.asp), [flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/), and [grid](https://css-tricks.com/snippets/css/complete-guide-grid/). My goal here was to give you a brief introduction about the role of CSS and its relationship to HTML.

## JavaScript

While HTML and CSS define the layout, content, and style of the web page, we often still need logic. This is where JavaScript comes in.

I'm going to make another file called `logic.js` and add this one function to it:

```js
const increment = () => {
    let numVal = parseInt(document.getElementById('number').value, 10);
    numVal++;
    document.getElementById('number').value = numVal;
}
```

I declared the above function in the [arrow function syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions), which I typically prefer, but you can't use it in every situation (such as writing it as a method in a class). The traditional way is to declare it like this:

```js
function increment() {
    let numVal = parseInt(document.getElementById('number').value, 10);
    numVal++;
    document.getElementById('number').value = numVal;
}
```

I can then import the JavaScript into the HTML file like so:

```html
<html>
  <head>
    <title>This is a title</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-F3w7mX95PdgyTmZZMECAngseQB83DfGTowi0iMjiWaeVhAn4FJkqJByhZMI3AhiU" crossorigin="anonymous">
    <script type="text/javascript" src="./logic.js"></script>
  </head>
  <body>
    <div>
        <form>
          <input type="text" id="number" value="0"/>
          <button type="button" class="btn btn-primary" onclick="increment()">Increment Value</button>
        </form>
    </div>
  </body>
</html>
```

Observe at how `id`, `value`, and `onclick` are connected in the HTML and JavaScript file. The JavaScript is able to read and modify the HTML that you see. Here's what happens when I click the button 10 times:

https://user-images.githubusercontent.com/8890739/134548544-29895615-0111-47fe-9866-5155fbddcce5.mov

### Async/Await to call APIs

Now I want to use this knowledge so far to use the internet for what it's meant for: looking at cats. But in order to get these pictures of cats, I'm going to call a RESTful API. In this case it's https://cataas.com/ (Cats as a Service).

In my `logic.js` file, I add this one function:

```js
const getCat = async () => {
  const response = await fetch("https://cataas.com/cat?json=true");
  const cat = await response.json()
  document.getElementById('catImg').src = "https://cataas.com" + cat.url;
}
```

In the first line, I use the native [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/fetch) in JavaScript to make a `GET` request to CaaS, asking for a JSON file. Since it will take time for my request to go through, and for the API to respond back, I need to put the `await` keyword in front. Also note that I need to specify that this function is `async` when I declare it. This allows us to write code that runs asynchronously in a declarative synchronous manner.

Here's a video that I think does a good job explaining it: https://www.youtube.com/watch?v=vn3tm0quoqE

The response returned by the API is in a [JSON](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/JSON) format, and features a random cat every time you call it. Here's an example of the JSON in one call from the CaaS API:

```json
{
  "id":"595f280c557291a9750ebf81",
  "created_at":"2016-04-13T14:21:31.699Z",
  "tags":["down"],
  "url":"/cat/595f280c557291a9750ebf81"
}
```

I then call the `json()` method on the response, which converts the response (which has a lot of other info that we don't need), into an object I can interface with in JavaScript. In this object, I only care about the `url` field. So I can reference it by calling `cat.url`. In the last line, I'll change the `src` attribute in the `img` tag to be that of the cat image. 

The HTML for this page looks like this:

```html
<html>
  <head>
    <title>All Cats are Cute</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-F3w7mX95PdgyTmZZMECAngseQB83DfGTowi0iMjiWaeVhAn4FJkqJByhZMI3AhiU" crossorigin="anonymous">
    <script type="text/javascript" src="./logic.js"></script>
  </head>
  <body>
    <div>
        <img src="" id="catImg" alt="cute cat" width="400px" height="200px" />
        <form>
          <button type="button" class="btn btn-primary" onclick="getCat()">Get Random Cat</button>
        </form>
    </div>
  </body>
</html>
```

Since I have this `getCat()` function binded to the button, I end up changing `src` in the `img` tag with each click with a new cat pic. This is the final product:

https://user-images.githubusercontent.com/8890739/134554752-5ea4b7cc-5854-4078-b8a3-4ab14e3df089.mov

## React

Manually linking the HTML and JavaScript is relatively straightforward as we have seen, but it can quickly get much more complex as the scope of a project increases. This is where React comes in. Under the hood, React updates the HTML and CSS on our page (the [DOM](https://en.wikipedia.org/wiki/Document_Object_Model)) in a performant manner as data and user input comes in. Furthermore, React has a massive amount of external packages/modules that we can use, reducing the amount of time that we need to spend creating things from scratch. Some of these are:

- [React Router](https://reactrouter.com/) - For routing between pages in a single-page application
- [React Bootstrap](https://react-bootstrap.github.io/) - The Bootstrap library that we saw earlier, but in declarative React components
- [Axios](https://axios-http.com/docs/intro) - An HTTP client with more configuation options than the native fetch API that we used earlier
- [left-pad](https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code/) - For exposing the weakness of all these package dependencies

React is made of components, which are similar to HTML tags. It is also written in an extended version of JavaScript called JSX. It's sort of a combination of HTML and JavaScript. For example, let's remake the increment example from earlier as a single React component:

```jsx
const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <form>
      <input type="text" value={count} readOnly />
      <button type="button" onClick={() => setCount(count + 1)}>
        Increment Value
      </button>
    </form>
  );
};

```

I can then create an instance of this Counter component by writing `<Counter />` anywhere I would put HTML. You can end up having React components within React components, which is a powerful tool. As well, each instance of Counter has its own variable `count`, so we don't have to mess with different `id`s and bindings to said `id`s like we would with vanilla JavaScript.

The curly braces for `{count}` and `{() => setCount(oldCount => oldCount + 1)}` allows you to "jump" between HTML and JavaScript. Being able to place these dynamic variables that auto-update the DOM when its values are modified is a key feature of React.

The `useState` function returns `count` and `setCount`, a variable and a function respectively. The `count` has an initial value of 0, as it was the argument that was passed into `useState`. To update count, you need to call the function `setCount` and pass in the new value as an argument. You need to call `setCount` instead of simply doing `count++` or `count = count + 1`, as invoking `setCount` lets React know to update all places where `count` is referenced in the DOM.

I made a [Code Sandbox](https://codesandbox.io/s/react-playground-forked-uqxve) with this component, so you can see it work and mess around with the code.

I'll show how to create a basic React app in an upcoming guide, and how to deploy it to AWS.

## Useful Links
- HTML
  - [Mozilla HTML Docs](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/HTML_basics)
- CSS
  - [Mozilla CSS Docs](https://developer.mozilla.org/en-US/docs/Web/CSS)
  - [Cascade](https://developer.mozilla.org/en-US/docs/Web/CSS/Cascade)
  - [Specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity#how_is_specificity_calculated)
  - [Box model](https://www.w3schools.com/css/css_boxmodel.asp)
  - [Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
    - [Flexbox in 100 seconds](https://www.youtube.com/watch?v=K74l26pE4YA)
    - [Flexbox Froggy](https://flexboxfroggy.com/)
  - [Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)
    - [Grid in 100 seconds](https://www.youtube.com/watch?v=uuOXPWCh-6o)
    - [Grid Garden](https://codepip.com/games/grid-garden/)
- JavaScript
  - [Mozilla JavaScript Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
  - [Array Map in 100 seconds](https://www.youtube.com/watch?v=DC471a9qrU4)
  - [Array Reduce in 100 seconds](https://www.youtube.com/watch?v=tVCYa_bnITg)
  - [? in NaN seconds](https://www.youtube.com/watch?v=O0gmXbN7lVE)
  - [Destructuring in 100 seconds](https://www.youtube.com/watch?v=UgEaJBz3bjY)
  - [JavaScript Modules in 100 seconds](https://www.youtube.com/watch?v=qgRUr-YUk1Q)
  - [JavaScript under the hood](https://www.youtube.com/watch?v=FSs_JYwnAdI)
  - [JavaScript functions explained](https://www.youtube.com/watch?v=gigtS_5KOqo)
  - [JavaScript tutorial](https://javascript.info/)
- React
  - [React in 100 seconds](https://www.youtube.com/watch?v=Tn6-PIqc4UM)
  - [React tutorial](https://reactjs.org/tutorial/tutorial.html)
