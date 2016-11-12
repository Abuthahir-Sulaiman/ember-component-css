# ember-component-css [![Build Status](https://travis-ci.org/ebryn/ember-component-css.svg?branch=master)](https://travis-ci.org/ebryn/ember-component-css) [![Ember Observer Score](https://emberobserver.com/badges/ember-component-css.svg)](https://emberobserver.com/addons/ember-component-css)

An Ember CLI addon which allows you to specify a component-specific style sheet inside of component pod directories in an app, addon, engine, or in-repo addon.

Contributions are welcome! Feel free to open up a pull request or issue, and join the [#e-component-css](https://embercommunity.slack.com/messages/e-component-css/) channel in the [Ember Slack community](https://ember-community-slackin.herokuapp.com/) if you have further questions, concerns, or ideas. Thanks! :smile:


## Installation

`ember install ember-component-css`

## Usage

This addon allows you to specify a style sheet inside of a component's pod folder.

Rules defined in the style-sheet will automatically be namespaced with an autogenerated class. That autogenerated class will also be injected into your component's `classNames` property. This enables you to worry less about rules clashing across component styles.

For example, given this `app/my-component/styles.scss` file:

```scss
& {  // ampersand refers to the component itself (parent selector)
  padding: 2px;
}
.urgent {
  color: red;

  span {
    text-decoration: underline;
  }
}
```

Your generated CSS output will look something like:

```css
.__my-component__a34fba {
  padding: 2px;
}
.__my-component__a34fba .urgent {
  color: red;
}
.__my-component__a34fba .urgent span {
  text-decoration: underline;
}
```

A typical component invocation that looks like this:

`{{my-component}}`

will generated markup like:

`<div class="__my-component__a34fba"></div>`

### Inclusion

To use this addon you *MUST*, import `pod-styles` into your base stylesheet.

```scss
// app/styles/app.scss
@import "pod-styles";
```

```scss
// app/styles/app.less
@import "pod-styles";
```

```scss
// app/styles/app.styl
@import 'pod-styles'
```

```scss
// app/styles/app.css
@import "pod-styles";
```

And that is it! The `pod-styles` file is generated during the build and will then be pulled into your other stylesheet to be processed like normal.

Note: If you are using more than one type of component style files (ie a .less file and a .scss file) then you will need to add the extension to the @import. Otherwise the extension can be left off.

### Use in addons
In order to use this inside of an addon, you need to add your style files inside of the components in the
addon directory. You will then be able to import the 'pod-styles' file inside of your addon style file which
is in the `/addon/styles` directory. These styles will then be added to the `vendor.css` file like normal.

If you are extending the `include` method in your addon, please make sure you call the super like this
```js
  included: function(app) {
    this._super.included.apply(this, arguments);
    ...
  }
```

Finally, be sure "ember-component-css" is listed under the "dependencies" key of your addon's `package.json` file, rather than "devDependencies".

### Plain css usage
In order to use this with plain css files, you need to install [`ember-cli-postcss`](https://github.com/jeffjewiss/ember-cli-postcss) and configure it with [`postcss-import`](https://github.com/postcss/postcss-import).

```
ember install ember-component-css
npm install postcss-import --save-dev
```
Then in your `ember-cli-build.js` you can configure it as such.
```js
var EmberAddon = require('ember-cli/lib/broccoli/ember-addon');
var CssImport = require('postcss-import');

module.exports = function(defaults) {
  var app = new EmberAddon(defaults, {
    postcssOptions: {
      filter: {
        enabled: true,
        plugins: [{
          module: CssImport,
        }]
      }
    }
  });

  return app.toTree();
};
```

You can also add in [`postcss-cssnext`](https://github.com/MoOx/postcss-cssnext) or any other
postcss plugins in this way too.

*Things like [`ember-cli-autoprefixer`](https://github.com/kimroen/ember-cli-autoprefixer) will work out of the box and do not need to be added in as a postcss plugin.*

### Getting the generated class name

You also have access to the generated class name to use in your templates. There is a computed property `componentCssClassName` This can be used to pass the class name to things like [`ember-wormhole`](https://github.com/yapplabs/ember-wormhole) or for use in BEM style classnames.
An example of BEM usage would be

`my-component/template.hbs`
```handlebars
<button class="{{componentCssClassName}}__button">
  Normal button
</button>
<button class="{{componentCssClassName}}__button {{componentCssClassName}}__button--state-success">
	Success button
</button>
<button class="{{componentCssClassName}}__button {{componentCssClassName}}__button--state-danger">
	Danger button
</button>
```
`my-component/styles.scss`
```scss
&__button {
  display: inline-block;
  border-radius: 3px;
  padding: 7px 12px;
  border: 1px solid #D5D5D5;
  background-image: linear-gradient(#EEE, #DDD);
  font: 700 13px/18px Helvetica, arial;

  &--state-success {
    color: #FFF;
    background: #569E3D linear-gradient(#79D858, #569E3D) repeat-x;
    border-color: #4A993E;
  }

  &--state-danger {
    color: #900;
  }
}
```

### Configuration

You can set the following configuration options in your `config/environment.js` file:

```js
ENV['ember-component-css'] = {
  option: 'value'
}
```

**namespacing(_enabled_)**

Defaults to true. Set this option to `false` to disable the namespacing feature of Ember Component CSS.

```js
ENV['ember-component-css'] = {
  namespacing: false
}
```

This changes the default behavior changes in two ways:

 1. The autogenerated component class is no longer added to your component's HTML
 2. Your pod CSS files are no longer are namespaced using the autogenerated component class.


### [The announcement from EmberConf 2015](https://youtu.be/T1zxaEKeq3E)
[![CSS is hard - EmberConf 2015](http://f.cl.ly/items/1a3a3r1C1y0D060D3j3u/EmberConf%202015%20-%20CSS%20Is%20Hard%20-%20YouTube%202015-03-22%2018-33-41.jpg)](https://youtu.be/T1zxaEKeq3E)
