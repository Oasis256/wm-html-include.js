# wm-html-include.js

is a small *script* to include external HTML page(s) into comprising HTML page.  
A part of  [Wamer project](http://www.wamer.net/)


## When to use it

If you need to compose a Web page on client-side, but either iframes or [HTML Imports]( http://www.w3.org/TR/2013/WD-html-imports-20130514) are not suitable to the case.  
One most likely may need it to:  
quickly sew Web page from some existing or newly developed small parts (components);  
patch existing Web page not changing significantly its content.


## How to use it

### Static links

```html
<!-- Any tag with reference to another HTML, may be another... -->
<div data-wi-src="http://another.domain/component-1.html"> </div>
<!-- ... -->
<!-- ... or the same domain -->
<span data-wi-src="component-2.html"> </span>
<!-- ... -->
<!-- It may even looks almost like W3C "import" -->
<link data-wi-src="component-3.html" />
<!-- ... -->
<!-- Somewhere below -->
<script src="wm-html-include.js"> </script>
```

*The script* includes all the pages referenced by **data-wi-src** attribute into the comprising HTML page.  
**Note!**: *the script* entirely removes the target element replacing it with referenced content.
So if you need any additional styles for included part you should wrap target with some container.
Example:

```html
<div style="width: 300px; height: 200px; background: silver;">
    <link data-wi-src="component.html" />
</div>
```

### Dynamic links

Use ***wmHtmlInclude()*** function of *the script* interface.

```html
<div id="place-here">
</div>
<!-- ... -->
<script src="wm-html-include.js"> </script>
<script>
    var link = document.createElement ('link');
    link.setAttribute ('data-wi-src', 'new-component.html');
    document.getElementById ('place-here').appendChild (link);

    // now do the thing
    window.wmHtmlInclude();
</script>
```


## How it works

*The script* finds all the tags with **data-wi-src** attribute.  
Then, for each such tag it gets the referred HTML page,  
merges all it's *style, link[rel="stylesheet"]* to the target page,  
replaces target element with source's *body*,  
loads and runs source's *script* elements.  
  
It circumvents the same-origin restrictions by [YQL](https://developer.yahoo.com/yql/)
and works with any [CORS enabled browser](http://caniuse.com/#feat=cors).
  
There are a few cases when the *css* or *script* is NOT loaded from the include:
 1. *link* or *script* with *src* attribute. *The script* checks if corresponding
 resource with the same *url* value already exists in the target document. In this case the resource
 is not loaded;
 2. You can define the loading condition explicitly by *data-wi-loadchk* attribute in included resources
   

```html
<link href="https://code.jquery.com/ui/1.11.2/themes/redmond/jquery-ui.min.css" rel="stylesheet"
        data-wi-loadchk="jQuery.ui" />
<link href="http://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet"
        data-wi-loadchk="jQuery.fn.scrollspy.noConflict" />
<!-- ... -->
<script src="https://code.jquery.com/ui/1.11.2/jquery-ui.min.js"
        data-wi-loadchk="jQuery.ui">
</script>
<script src="http://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"
        data-wi-loadchk="jQuery.fn.scrollspy.noConflict">
</script>
```
   
*The script* evaluates the condition pointed out by *data-wi-loadchk* and does NOT load
 the resource if the condition is *true*.    
 In the example above, the *jQuery.ui* and *Bootstrap* libraries are checked to prevent the second loading.

## Warning: (X)HTML compliance assumed

In order to properly display the page, and due to the loading and processing strategy, it's desired that included pages are (X)HTML compliant.  
It means:
* *head* and *body* tags are required;
* tag and attribute names must be in lower case;
* for non-empty elements, end tags are required
```html
<!-- BAD -->
    <p>paragraph 1<p>paragraph 2
<!-- GOOD -->
    <p>paragraph 1</p><p>paragraph 2</p>
```
* attribute values must always be quoted
```html
<!-- BAD -->
    <td rowspan=3>
<!-- GOOD -->
    <td rowspan="3">
```
* attribute-value pairs must be written in full
```html
<!-- BAD -->
    <dl compact>
<!-- GOOD -->
    <dl compact="compact">
```
* tag for empty element must end with " />"
```html
<!-- BAD -->
    <hr> <br>
<!-- GOOD -->
    <hr /> <br />
```
* Include a space before the trailing / and > of empty elements, e.g. `<br />`, `<hr />` and `<img src="karen.jpg" alt="Karen" />`
* do not use empty instance of an element whose content model is not EMPTY (for example, an empty div or paragraph)
```html
<!-- BAD -->
    <div></div>
<!-- GOOD -->
    <div> </div>  <!-- white space inside! -->
<!-- GOOD -->
    <div>
    </div>      <!-- line-break between start and end tags -->
```
* only five "standardised" named entities are allowed: `&amp; &quot; &lt; &gt; &apos;`  
  Use numeric entities in all other cases (i.e. `&#160;` in place of `&nbsp;`, `&#169;` in place of `&copy;`, etc.)
* internal scripts content should be wrapped by `//<![CDATA[` and `//]]>`, i.e.
```html
<script>
//<![CDATA[
    // my scipt here
    do_something();
    // ...
//]]>
</script>
```


## Examples
* [Combine dialog panel with jQuery UI Button, jQuery UI menubar plugin and Bootstrap carousel, each in separate HTML file, into one target page](http://wamer.net/examples/wm-html-include/1/index.html)


## License
[MIT License](http://opensource.org/licenses/MIT)


## Alternatives
* [html-imports-content](https://github.com/adjohnson916/html-imports-content)
* [Matthew-Dove/Inject](https://github.com/Matthew-Dove/Inject)
