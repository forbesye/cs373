# Intro to HTML, CSS, JavaScript/TypeScript, and React

## HTML
HTML is the markup language that is the building block of the web. It defines the content and structure of web pages. An HTML document is comprised of **tags** that are nested in a tree-like structure. For example, the following HTML:
```
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
- `body` - Used to define the main body of the web page
- `h1`, `h2`, ..., `h6` - Heading, typically to signify the title of some section
- `p` - Paragraph, used for general text
- `a` - Anchor tag, used to link one page to another page. Uses the `href` attribute to describe the link
  - e.g. `<a href="https://google.com">Google</a>` renders as <a href="https://google.com">Google</a>
- `img` - Used to display images. Uses `src` tag to define path to the image, and `alt` should often be included for accessability to describe the image
  - e.g. `<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cat_poster_1.jpg/640px-Cat_poster_1.jpg" alt="cute cats" />` renders as:
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cat_poster_1.jpg/640px-Cat_poster_1.jpg" alt="cute cats" />

## CSS

## JavaScript/TypeScript

## React
