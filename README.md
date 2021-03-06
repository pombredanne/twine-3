twine
-----

[![Build Status](https://secure.travis-ci.org/Shopify/twine.png)](http://travis-ci.org/Shopify/twine)

[Demonstration](http://shopify.github.io/twine/)

Twine is a minimalistic two-way binding system.

Features:
 - It's just JS - no new syntax to learn for bindings
 - Small and easy to understand codebase

Non-features:
 - No creation of new nodes (e.g. no iteration)
 - No special declaration of bindable data (i.e. bind to any JS data)

## Installation

Twine is available on bower via `bower install twine` if that is your preference.

Twine comes as `dist/twine.js` and `dist/twine.min.js` in this repo and in the bower package.

Twine is also available as a gem.  In your Gemfile, add `gem 'twine-rails'` and include it in your `application.js` manifest via `//= require twine`

## Usage

Twine can be initialized simply with the following:

```html
<script type="text/javascript">
  context = {}
  $(function() {
    Twine.reset(context).bind().refresh()
  });
</script>
```

Above, `context` will be considered the context root, and this will work until you navigate to a new page.  On a simple app, this may be all you need to do.

### With [rails/turbolinks](https://github.com/rails/turbolinks)

Turbolinks requires a bit more consideration, as the executing JS context will remain the same -- you have the same `window` object throughout operation.  When the page changes and new nodes come in, they need to be re-bound manually.  Twine leaves this to you, rather than attempting to guess.

Here's a sample snippet that you might use:

```coffee
context = {}

document.addEventListener 'page:change', ->
  Twine.reset(context).bind().refresh()
  return
```

If you're using the [jquery.turbolinks gem](https://github.com/kossnocorp/jquery.turbolinks), then you can use:

```coffee
context = {}

$ ->
  Twine.reset(context).bind().refresh()
  return
```

### With [Shopify/turbograft](https://github.com/Shopify/turbograft)

With TurboGraft, you may have cases where you want to keep parts of the page around, and thus, their bindings should continue to live.

The following snippet may help:

```coffee
context = {}

reset = (nodes) ->
  if nodes
    Twine.bind(node) for node in nodes
  else
    Twine.reset(context).bind()

  Twine.refreshImmediately()
  return

document.addEventListener 'DOMContentLoaded', -> reset()

document.addEventListener 'page:load', (event) ->
  reset(event.data)
  return

document.addEventListener 'page:before-partial-replace', (event) ->
  nodes = event.data
  Twine.unbind(node) for node in nodes
  return

$(document).ajaxComplete ->
  Twine.refresh()
```

## Contributing

1. Clone the repo: `git clone git@github.com:Shopify/twine`
2. `cd twine`
3. `npm install`
4. `npm install -g testem coffee-script`
5. Run the tests using `testem`, or `testem ci`
6. Submit a PR

## Releasing

1. Update version number in `package.json`, `bower.json`, and `lib/twine-rails/version.rb`
2. Run `bundle install` to update `Gemfile.lock`
3. Push the new tag to GitHub and the new version to rubygems with `bundle exec rake release`
