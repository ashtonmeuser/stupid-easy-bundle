# Stupid-Easy Bundle

A simple implementation of Pug templating and LESS styling bundled using Parcel.

## Prerequisites

Install Node. Outside of the scope of this tutorial. See [instructions](https://nodejs.org/en/download/package-manager/). Alternatives to this installation method exist.

Node is a JavaScript runtime built on Chrome's V8 engine. Allows the execution of JavaScript programs outside of the browser.

NPM will be installed alongside Node. This is the most popular package manager for Node. Allows the installation of reusable programs called *packages*.

## Creating a Project

Create the directory that will house our project.

```
mkdir my-project-folder
cd my-project-folder
```

Initialize a Git repository. We'll also ensure that Node modules and cached files are not checked into the repository.

```
git init
echo "node_modules" >> .gitignore
echo ".cache" >> .gitignore
echo "dist" >> .gitignore
```

Initialize an NPM package. Name the project, add yourself as the author, and give a short description.

`npm init`

## Dependencies

Dependencies are packages that our project, or package, depends on. All project dependencies are stored in the `package.json` file.

Maintaining dependencies in the `package.json` file will help install all required packages later and/or on another machine.

### Installing Pug

Pug, formerly known as Jade, is the templating engine we will be using. This will help is apply DRY (don't repeat yourself) concepts to our markup.

`npm install pug --save-dev`

The [Getting Started](https://pugjs.org/api/getting-started.html) page of Pug's documentation provides a great introduction to the template's semantics. Note that Pug, like HTML, is just another markup language. In order to be interpreted by the browser, Pug will be compiled into HTML by our bundler.

### Installing LESS

LESS is a superset of CSS. It adds the ability to use variables, mixin functions, style nesting, mathematical operations, and more to traditional CSS. Using LESS will avoid repeating styling.

`npm install less --save-dev`

Refer to the [documentation](http://lesscss.org) for more details.

### Installing Parcel

Add the Parcel bundler package. This will add the package to our `package.json` as a development dependency.

`npm install parcel-bundler --save-dev`.

Refer to the [Getting Started guide](https://parceljs.org/getting_started.html) for an overview of Parcel.

## Building the Project

Because source files must be converted from their Pug and LESS formats to HTML and CSS, respectively, we need to build the project. This may be a new concept as traditional web development does not require a "compilation" step. It's 2019, you'll get used to this.

### Create Source Files

Create a `source` directory and add `index.less` and `index.less` files. These files will stand in for content we'll add later.

```
mkdir source
touch source/index.less
touch source/index.pug
```

### Run the Development Server

In your `package.json` file, add a new script. This is done by adding a new key and value to the `scripts` dictionary within the JSON file. The value of this entry will be the command run when the script is called using the entry's key. Note that NPM has access to the installed packages, so the script may not behave the same way when run in the terminal.

The  `scripts` dictionary should be edited to match the following.

```
scripts: {
  "dev": "parcel source/index.pug"
},
```

This allows us to execute the command `parcel index.pug` via NPM by running `npm run dev`. Give it a try. You can access the development server by navigating to [localhost:1234](localhost:1234) in your web browser. If the command produces no errors, and a blank page appears, you've done everything correctly!

You can add any number of keys and associated commands to the `scripts` dictionary, and run them via `npm run [your command key]`.

### Building the Project

Add a directory for the raw HTML and CSS files to inhabit. If you've run the development server, this directory may exist and be populated already.

`mkdir dist`

We're adding another script to the `scripts` dictionary in `package.json`. Change it to look like the following.

```
"scripts": {
  "dev": "parcel source/index.pug",
  "build": "parcel build source/index.pug -d dist"
},
```

The *build* command converts our Pug and LESS files and converts them to HTML and CSS, respectively.

Give this new script a shot by running `npm run build`. You should see a raw HTML file appear in the `dist` directory. That's all there is to it! Time to add content to our site.

## Adding Content

First, let's add a place to store partials. Create a `partials` directory within the `source` directory.

`mkdir source/partials`

### Extend the Index Page

Edit the `index.pug` file to look like the following. If you've done your homework and explored the Pug documentation, you'll recognize this is simply adding a title, including a stylesheet, and displaying a simple body element.

```
doctype html
html(lang='en')
  head
    title My Bundled Project
    link(rel='stylesheet', href='index.less')
  body
    p(class='hello') hello!
```

Note that we are now including the `index.less` asset. This will apply styles the same way as traditional CSS applies styles to HTML. We are applying the `hello` class to our simple paragraph in order to associate styles within `index.less`.

The `doctype html` and `html(lang='en')` lines are the boilerplate equivalent to the following traditional HTML.

```
<!DOCTYPE html>
<html lang="en">
</html>
```

Because you've read the Pug documentation, you know that whitespace is important, and therefore closing tags are not required.

Edit the `index.less` file to look like the following.

```
@hello-color: #F00;
@hello-padding: 5px + 5px;

.hello {
  color: @hello-color;
  padding: @hello-padding;
}
```

Note the use of the variables `hello-color` and `hello-padding`. A mathematical operation is also used. Targetting the `hello` CSS class is done the same was as in traditional CSS. In fact, the contents of `index.less` can be written entirely in traditional CSS with no impact, as LESS is a superset of the CSS language.

### Adding Reusable Partials

Separating markup into smaller, more easily digestible portions helps keep a project organized. It also helps to avoid repeating boring, frequently used components. Partials seek to solve these problems.

Let's create partials to display a header and footer that we could reuse on any page. Create the partials by running the following.

```
echo "p(class='header') I'm a header" >> source/partials/header.pug
echo "p(class='footer') I'm a footer" >> source/partials/footer.pug
```

Edit the `body` element of the `index.pug` file to reflect the following.

```
include partials/header.pug
p(class='hello') hello!
include partials/footer.pug
```

Finally, add the following to the `index.less` file to apply styles to the partials.

```
.header {
  color: #F0F;
}

.footer {
  color: #0F0;
}
```

### Adding Anchors

Create a new page with some dummy content.

```
echo "p(class='hello') I'm page 2" >> source/page2.pug
echo "p(class='hello') I'm page 3" >> source/page3.pug
```

Change the `body` element of the `index.pug` file to the following.

```
include partials/header.pug
p(class='hello') hello!
p
  a(href='/page2.pug') Page 2
p
  - var pageNum = '3';
  a(href='/page' + pageNum + '.pug') Page #{pageNum}
include partials/footer.pug
```

In the above, We've included an example of how to declare and use a variable in Pug. Note that the anchor linking to Page 2 is static while the link to Page 3 depends on the `pageNum` variable.

### Iterate Over Some Data

Using Pug allows us to iterate over data rather than rewriting verbose HTML. Let's create a list withing our main page.

Add the following to the end of the `footer.pug` file.

```
p(class='footer') I'm a footer
ul
  each val, index in ['https://google.com', 'https://bing.com', 'https://duckduckgo.com']
    li
      p
        | link #{index}:
        a(href=val) #{val}
```

The above list is formed of the elements of an array. The object to iterate over is a plain JavaScript object, and can therefore be an array variable, the result of a function call, or any other JavaScript structure that can be iterated over.

Within the iteration, you can include any Pug markup you like. In the case above, we've linked to some popular search engines.

## Next Steps

Read through the Pug documentation to take advantage of it's many features. Break your styles up if the files become unruly. Apply the DRY principle. Experiment.
