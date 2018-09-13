# Wiki Development Recommendations

I want to be clear that this is **not** an introduction to web development. We will **not** be going over the basics of HTML, CSS or JavaScript, how a website project should be structured, how a webserver works, how MediaWiki works, etc. etc.

We will however provide resources that will let you learn these things on your own in the resources section. These are the same resources we used to develop our wiki for [Virginia iGEM 2018](http://2018.igem.org/Team:Virginia).

## General Recommendations

### Our Golden Rule

And, really, a golden rule that anyone writing code every should adopt:

**Don't**

**Repeat**

**Yourself.**

**Don't repeat yourself.**

Every time you copy and paste a piece of code unnecessarily, every time you just copy-and-paste the same HTML file over and over again, modifying each one by hand, somewhere, somebody spills a coffee on their new laptop.

By splitting your code up into reusable, templatable chunks, which you automatically insert at various locations in your code, you break your project down into small, palatable bites that are easy for your brain (and perhaps more importantly, other teammates' brains) to understand. In the event that you have to make a change that reaches across many files (maybe your team name changed, for example?), instead of having to go in by hand and make that change every time, you can just modify a single variable, and be done with it.

It makes it easier for your teammates to collaborate on the code.

It introduces necessary automations that simplify your work, saving you hours.

It makes your code more maintainable.

It saves you days of man-hours in the long run.

**Don't repeat yourself.** You'll note that almost every recommendation we give below is just a different version of this; either not repeating your actions (automating away the hard stuff) or not repeating your code (abstractions and templates).

### Use a tool to automate your building and uploading
[**Okay yeah we're biased because we made our own tool and it's awesome.**](https://github.com/Virginia-iGEM/igem-wikibrick)

But, in all seriousness, as much time as it took to build our tool, we're glad we make it, and more importantly _you don't have to make one_. It took hours of mundane clicking and repeatedly entering the same command into a terminal out of our workflow, and let us focus more on the actual web development than on all of the legwork.

Using a tool that automates your _build_ (taking some source files and producing built project files on your local machine) lets you do many extremely valuable things that are just not possible if you don't use a build tool. There are so many things that these things have their own section; in fact half of this guide is only possible with a build tool. 

Using a tool also automates your _upload_ (taking your built project files and putting them on the actual `igem.org` server). While this isn't a strictly necessary automation, it is approximately 1024% (according to our modeling team) less painful to have a computer do it than to manually copy-and-paste all of your source files by hand, one-by-one, any time there is a change, or to upload your images one-by-one (because `igem.org` doesn't have a batch-upload images button for some reason?).

### Use Preprocessors and Postprocessors

We used a ton of these (and of course you get all of them for free when you use `igem-wikibrick`):

#### For our Stylesheets (CSS)

[SASS](https://sass-lang.com/), a superset of CSS, which makes it far easier to reuse code and create complex styles specific to certain elements in your website. To quote the headline on their website: "CSS with Superpowers." You won't understand why it's valuable until you start using it, then you'll never be able to go back to plain CSS.

Our favorite part of SASS is what are called mixins; a practical example:

``` SASS
@mixin center-image { // Define the center-image mixin which centers images within their containers
  display: block;
  margin-left: auto;
  margin-right: auto;
}

main article#landing-content {
  img {
    @include center-image;
  }
}

navline li ul li {
  img {
    @include center-image;
  }
}
```

This is how you reuse code in SASS. It's equivalent to functions in imperative programming; wrap up your reusable code and pass it around to be used by everyone everywhere.

We also used the [autoprefixer](https://github.com/postcss/autoprefixer) CSS postprocessor to add all of our browser-specific tags. These are CSS properties that begin with `--webkit` or `--edge` that are necessary to make certain styling work with certain browsers (namely Safari and Microsoft browsers). We didn't even have to think about these tags because we had a tool that added them for us.

#### For our Code (JavaScript)

Use `require()` statements in your JavaScript with [Browserify](http://browserify.org/). If you don't use JavaScript and Node.js, you have no idea what this means, but it's pretty magical. It simplifies dependency management of your clientside JavaScript significantly.

#### For our Markup (HTML)

Liberal use of [Handlebars Templating](https://handlebarsjs.com/) gave us all the advantages of reusing our HTML without any of the slowdowns that might occur due to clientside loading. HTML Templating systems (like Handlebars) are effectively scriptable copy-and-paste tools. Handlebars lets us have a common `<head>` - all of the metadata for every HTML file we upload (there are 40 something of them) was written one time, is contained in one file, and is automatically transplanted into every HTML file.

The same goes for our navbar, footer, and many of the other reused elements throughout our wiki. Of note is that the tooltips on our wiki are powered by Handlebars; when you hover over them, they give you a definition. This was done without JavaScript using pure CSS and HTML.

## Recommendations specific to the iGEM Wiki

These aren't things that you should do in any web development project, but ones which we found it made much easier to work with the iGEM wiki.

### Don't be afraid to `!important`

`!important` is the magic wand of wiki development.

You will quickly find out that, regardless of whether you are working with our without a build automation tool, sometimes you'll write a style for a wiki page and it just won't take hold. It appears to be doing nothing at all.

This is because _the iGEM wiki is out to get you._ Every time a wiki page loads up, it first loads a stylesheet contained in the file `load.php`, hosted on the iGEM servers. You cannot stop this stylesheet from loading, and it has priority over all your stylesheets. As a result, your font won't have the right size, your colors won't be right, and your padding/margins may be wrong...

Unless you use `!important`:

``` css
article {
  color: red !important;
}
```

A bit of a history lesson: `!important` is a decorator from the very first version of CSS, a standard written way back in the 90s. It was originally intended to let developers debug their CSS easily, before web browsers had inspectors and before syntax highlighting and code linting was a thing. That's all it was meant for; debugging.

But to circumvent `load.php`, sometimes you have to use `!important`. Don't be afraid to do it, you aren't gonna break anyone's browsers. You should just... Never do it outside of working on the iGEM wiki. This is a problem with the way the iGEM wiki is set up, and absolutely should change in the future. Email your iGEM representative and let your voice be heard.

### Use and abuse the Inspector

Your web browser has an inspector. You can open it by right clicking on any element on your wiki page (be it hosted locally or live), and click Inspect Element. This will tell you everything about how the element is loaded, styled, rendered, and let you play with it in JavaScript and see how all of your scripts are behaving.

It is like a debugger on steroids.

It is invaluable. If something is ever not working as inspected, use the Inspector.

The Inspetor additionally lets you play around with styles and delete or add whole chunks of markdown in your browser, in the event that you have to try a bunch of things and you don't want to reload the page every time. This can also help in rooting out problems.

Do not comment out. Do not `console.log`. Inspect.

### Don't accidentally upload your own version of JQuery.

This is fairly specific to our team, but we figured we'd share it.

When we first built `igem-wikibrick`, we designed it to upload any packages we'd installed via `bower`, and this included our own copy of JQuery (Version 3.3.1 specifically). This payload would be injected to every one of our pages, giving us access to whatever JavaScript and CSS packages we needed.

This system was originally built for Bootstrap, but proved useful for other things.

It turned out that when we loaded in both JQuery 3.3.1 and the version of JQuery iGEM autoloads (forcefully) onto every page (JQuery 1.11.1), it would result in half of our JQuery flat-out not working.

So, don't do this. `igem-wikibrick` has a setting that explicitly disables uploading of local versions of JQuery, but enables building locally with it (specifically version 1.11.1), as to emulate the iGEM wiki's behaviour as closely as possible.

## Resources

- www.w3schools.com for all basic HTML/CSS/JavaScript lessons
- www.Codepen.io for any time you need functional CSS or JavaScript that you don't know how to write. *We strongly recommend reading through snippets and understanding them instead of blindly copy-and-pasting, as you may grab some properties or code you really don't want in your wiki.*
- Team Peshawar 2016 [has their own list of reccommendations](https://2016.igem.org/Team:Peshawar/Wiki). You'll notice many of them are the same as ours, they're just using different tools. Their guide inspired many of the tools we built.
- iGEM University of Toronto 2016 has a relevant description of how Mediawiki works on their [Recipes page](https://github.com/igemuoftATG/igemwiki-api/blob/master/recipes/README.md) for the `igemwiki-api` tool, which our own tool makes use of.
- iGEM-Copenhagen 2018 has a list of [awesome iGEM resources](https://igem-copenhagen.github.io/awesome-iGEM/), including wiki resources (including links to our toolset ¯\\_(ツ)_/¯)

## Contributing

If you have anything you'd like to put on this page (for example, if your iGEM team has written any guides of their own), feel free to submit a pull request or just email us with links to your content at virginia.igem@gmail.com.
