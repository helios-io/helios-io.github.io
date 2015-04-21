Helios Project Official Website
===========================

#Getting started

## Local usage

### Install Ruby
If you don't already have Ruby installed, we recommend using [RailsInstaller](http://railsinstaller.org/en) to get it up and running on your system.

### Install Node.JS

Follow the instructions on this page https://github.com/joyent/node/wiki/Installation

### Clone the repository.
```
> git clone https://github.com/akkadotnet/getakka.net.git getakkanet
> cd getakkanet
```

### Make sure `grunt-cli` is installed
```
> npm install -g grunt-cli
```

### Build the site

Run Jekyll locally via

```
> bundle exec jekyll serve --watch
```

or

```
> jekyll serve
```

Your website should be viewable by going to [localhost:4000](http://localhost:4000/)

### Live reloads
When running the site locally, the grunt script will be watching the `/src/` folder for changes.
If any change is detected, this will trigger a regeneration of the content and your browser will be live reloaded.

## Making style changes
Edit the appropriate file in `/_scss`. The SCSS files then get compiled automatically by `grunt-contrib-compass` into the `screen.css` file.

#### Rebuild CSS from SASS

Run grunt to compile css from sass by simply run 

```
> grunt
```

from your terminal. It will launch watch. <code>control-c</code> to stop.

## Adding data
In short, to add sitewide data you can add it to `src/_data/site.yml` and it will then be accessible via handlebars in all templates. For example, `site.yml` defines `url`, which is then accessed in the templates like so: `{{site.url}}`.

For more details / advanced usage, read the docs on [`options.data`](http://assemble.io/docs/options-data.html).

## Contributing to the documentation

Contributions to the wiki documentation can be done in a few different ways;

Fork and Clone this repository, then apply your modifications to the files inside `src/wiki/`.
`Commit` and `Push` your changes back to your Github Fork and create a pull request.
See `Local usage` for more info on how to preview your local changes.

Or.
You can use the `Edit on Github` link on each Wikipage on the live site, this will take you to an edit mode version of the page here on github.
If you do not have commit rights for this repository, Github will allow you to do an ad hoc pull request right there.