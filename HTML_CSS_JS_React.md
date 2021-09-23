# Intro to HTML, CSS, JavaScript/TypeScript, and React

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

## JavaScript/TypeScript

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


## React
