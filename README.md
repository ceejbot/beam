## Beam

Beam is [ICanHaz](http://icanhazjs.com/) rewritten for [mote.js](http://satchmorun.github.com/mote/). It reads moustache-style templates from script tags in an html page and compiles them for later use by other script elements in the page. Beam caches the compiled templates instead of the string source. Also, it is more agnostic than ICanHaz about its optional libraries. It will pick up anything claiming that it is __$__ in the global namespace and assume it works like jquery/zepto. This allows you to use Ender instead of jquery by adding some more packages to your ender build:

`ender add bonzo qwery domready`

Also has a dependency on [lscache](https://github.com/pamelafox/lscache) for template caching in local storage.

## Usage

Beam imitates ICanHaz's API. It creates a function for every template it finds in the DOM marked as type "text/html" or "text/x-icanhaz". That method has the following signature:

`beam.templatename(data [, raw])`

`data` must contain a valid context for a mote template. Pass "true" for `raw` to receive a string in return. Otherwise Beam will wrap up the result as a dom element.

For example, given the following html:

```html
<script type="text/html" id="slartibartfast">
<div><i>Greetings,</i> {{last}} {{first}} {{last}}.</div>
</script>
```

beam creates a function named `slartibartfast` that can be called like this:

```javascript
var rendered = beam.slartibartfast({ first: 'Arthur', last: 'Dent' });
$('#display_it_here').append(rendered);
```

Beam also provides the following functions. Their names are reserved words; you cannot create templates with the same names.

`beam.addTemplate(name, string)`

Add a template with the given name and source. Compiles the template and caches the result in `beam.templates[name]`. Creates a function to render the template in `beam[name]`.

`beam.clearAll()` 

Clear the templates and partials cache.

`beam.grabTemplates()` 

Search the DOM for `<script type="text/html">` tags to make templates out of, then remove those elements from the DOM. This function is called automatically when the document is ready.

`beam.refresh()`

Clear the cache and re-examine the DOM for new templates.

### Rendering templates

`beam.render(tmpl, data, callback)`

Render the given template name with the passed-in data hash, and fire the callback with the rendering as the sole parameter.

`beam.renderInto(templ, data, element)`

Render into the specified css element. Fire and forget.

### Fetching templates from a server

Set `beam.templateURL` to a string with the url for templates.

`beam.fetch(tmpl, callback)`

Fetch `beam.templateURL/tmpl` using ajax, compile the result, and cache it in local storage.

Beam will check local storage to see if it has a cached version of the template already. If it does, it will send an if-modified-since header along with the ajax request for the template.

`beam.preload(tmpllist)`

Pass a list of template names. Beam will fetch, compile, and cache whatever is returned by a fetch of `beam.templateURL/tmplname`, falling back to any locally cached versions if the server does not have anything newer.

## License

MIT.
