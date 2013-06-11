jqfactory
=========

> A jQuery Plugin Factory that provides a consistent foundation and API for building stateful jQuery widgets.  

##Notable Features
 - Supports jQuery prototype namespacing and event namespacing
 - Includes an elegant, promises-based, solution for plugins that rely on asynchronous behavior on initialization
 - Implements a simple inheritance paradigm that allows you to reuse methods/properties from other jqfactory plugins using mixins and prototypal inheritance to reuse jqfactory common methods
 - Supports privately scoping instance properties to easily construct a public API
 - Provides over 10 public/private convenience methods/properties that are useful for plugin development
 - Supports automatic event binding/delegation and event removal
 - Prevents multiple plugin initializations per element
 - Provides a jQuery plugin pseudo selector to query for all DOM elements that have called the plugin

##Requirements
jQuery 1.7+

##Browser support
IE8+, Modern Browsers

##Quick Start
1.  Download **jQuery 1.7+** and **jqfactory**, create your plugin file, and include them all as `script` tags on an HTML page
    ```html
    <script src="http://code.jquery.com/jquery-1.9.1.js"></script>

    <script src="./jqfactory.js"></script>

    <script src="./myawesomeplugin.js"></script>
    ```

2.  Start building your jQuery plugin with **jqfactory**!

##Developer Guide

**Note:** All methods used in this section are referenced in the _API_ documentation (further down)

1.  Wrap your entire plugin in an immediately invoked function expression (IIFE)

    ```javascript
    (function($, window, document, undefined) {
        // Your plugin will go here
    }(jQuery, window, document));
    ```

2.  Use the `$.jqfactory()` method to start creating your jQuery plugin.

    **Note:** The first argument of the `$.jqfactory()` method requires a namespace **and** base name.
    
    ```javascript
    (function($, window, document, undefined) {
        // Your plugin will go here
        // namespace - person
        // name - greg
        $.jqfactory('person.greg', {
            // Your plugin instance properties will go here
        });
    }(jQuery, window, document));
    ```

3.  Include an `options` object that will store your default plugin options

    ```javascript
    (function($, window, document, undefined) {
        // Your plugin will go here
        // namespace - person
        // name - greg
        $.jqfactory('person.greg', {
            // Your plugin instance properties will go here
            // Default plugin options
            option: {
                occupation: 'JavaScript Engineer',
                age: 24
            }
        });
    }(jQuery, window, document));
    ```

4.  Include a `_create` method that will act as your plugin constructor.  This method is only called once per element.

    ```javascript
    (function($, window, document, undefined) {
        // Your plugin will go here
        // namespace - person
        // name - greg
        $.jqfactory('person.greg', {
            // Your plugin instance properties will go here
            // Default plugin options
            option: {
                occupation: 'JavaScript Engineer',
                age: 24
            },
            // Plugin Constructor
            _create: function() {
                // This is where you can set plugin instance properties
                this.fullname = 'Greg Franko';
            }
        });
    }(jQuery, window, document));
    ```

5.  Include a `_render` method that will handle all of your plugin dom manipulation on initialization (e.g. creating, appending, etc).  The `_render()` method is called after the `_create()` method and only called once per element.

    **Note:** If the previous `_create()` method had returned a jQuery Deferred object, then the `_render()` method would wait to be called until after the Deferred object was resolved.

    ```javascript
    (function($, window, document, undefined) {
        // Your plugin will go here
        // namespace - person
        // name - greg
        $.jqfactory('person.greg', {
            // Your plugin instance properties will go here
            // Default plugin options
            option: {
                occupation: 'JavaScript Engineer',
                age: 24
            },
            // Plugin Constructor
            _create: function() {
                // This is where you can set plugin instance properties
                this.fullname = 'Greg Franko';
            },
            // Dom manipulation goes here
            _render: function() {
                // This is a perfect spot for creating and appending your plugin dom elements to the page
                var greg = $({
                    'class': 'developer'
                });
                greg.appendTo('body');
            }
        });
    }(jQuery, window, document));
    ```

6.  Include an `_events` object that will hold all of the event bindings for your plugin.  These events are bound after the `_render()` method is called and only called bound once per element.

    **Note:** If the previous `_render()` method had returned a jQuery Deferred object, then the `_events` object would have bound all events after the Deferred object was resolved.

    ```javascript
    (function($, window, document, undefined) {
        // Your plugin will go here
        // namespace - person
        // name - greg
        $.jqfactory('person.greg', {
            // Your plugin instance properties will go here
            // Default plugin options
            option: {
                occupation: 'JavaScript Engineer',
                age: 24
            },
            // Plugin Constructor
            _create: function() {
                // This is where you can set plugin instance properties
            },
            // Dom manipulation goes here
            _render: function() {
                // This is a perfect spot for creating and appending your plugin dom elements to the page
                var greg = $({
                    'class': 'developer'
                });
                greg.appendTo('body');
            },
            // Plugin event bindings
            _events: {
                // Delegated event that assumes the .face element is inside of the element that called the plugin
                '.face click': function (ev) {
                },
                // The exclamation point tells jqfactory that this event should be directly bound (not delegated)
                '!.developer click': function() {
                },
                // Special event
                'disable': function() {
                }
            }
        });
    }(jQuery, window, document));
    ```

7.  Include a `_postevents` method that will be called after all events within the `_events` object are bound.  Within the `_postevents()` method you are now free to trigger any events that are directly bound to elements.  The `_postevents()` method is only called once per element.

    ```javascript
    (function($, window, document, undefined) {
        // Your plugin will go here
        // namespace - person
        // name - greg
        $.jqfactory('person.greg', {
            // Your plugin instance properties will go here
            // Default plugin options
            option: {
                occupation: 'JavaScript Engineer',
                age: 24
            },
            // Plugin Constructor
            _create: function() {
                // This is where you can set plugin instance properties
            },
            // Dom manipulation goes here
            _render: function() {
                // This is a perfect spot for creating and appending your plugin dom elements to the page
                var greg = $({
                    'class': 'developer'
                });
                greg.appendTo('body');
            },
            // Plugin event bindings
            _events: {
                // Delegated event that assumes the .face element is inside of the element that called the plugin
                '.face click': function (ev) {
                },
                // The exclamation point tells jqfactory that this event should be directly bound (not delegated)
                '!.developer click': function() {
                },
                // Special event
                'disable': function() {
                }
            },
            // All event listeners are now bound
            _postevents: function() {
                // You can now manually trigger directly bound events using the jqfactory _trigger() method
                this._trigger('.developer', 'click');
            }
        });
    }(jQuery, window, document));
    ```

8.  Include any custom methods/properties that you like to complete your plugin!  Remember that jqfactory provides you a bunch of helper methods including `_on()`, `_off()`, `_trigger()`, `_superMethod()`, `delay()`, `disable()`, `enable()`, `destroy()`, and `option()` that you can use throughout your plugin.

    **Note:** If you override a default jqfactory method, you can still use the jqfactory method by using `_superMethod()` 

    ```javascript
    (function($, window, document, undefined) {
        // Your plugin will go here
        // namespace - person
        // name - greg
        $.jqfactory('person.greg', {
            // Your plugin instance properties will go here
            // Default plugin options
            option: {
                occupation: 'JavaScript Engineer',
                age: 24
            },
            // Plugin Constructor
            _create: function() {
                // This is where you can set plugin instance properties
            },
            // Dom manipulation goes here
            _render: function() {
                // This is a perfect spot for creating and appending your plugin dom elements to the page
                var greg = $({
                    'class': 'developer'
                });
                greg.appendTo('body');
            },
            // Plugin event bindings
            _events: {
                // Delegated event that assumes the .face element is inside of the element that called the plugin
                '.face click': function (ev) {
                },
                // The exclamation point tells jqfactory that this event should be directly bound (not delegated)
                '!.developer click': function() {
                },
                // Special event
                'disable': function() {
                }
            },
            // All event listeners are now bound
            _postevents: function() {
                // You can now manually trigger directly bound events
                this._trigger('.developer', 'click');
            },
            // Overriding the destroy method
            destroy: function() {
                // Calling the jqfactory `destroy()` method
                this._superMethod('destroy');
            },
            // Example of a custom method that I added to my plugin
            customMethod: function() {
                // Binding another event manually
                this._on('.hand click', function(ev) {
                    console.log('hand clicked!');
                });
            }
        });
    }(jQuery, window, document));
    ```

9.  Make cool things!

##API
 **$.jqfactory(String namespace.name, Object properties, Boolean enforceNamespace)**

 - The jqfactory is a simple function on the global jQuery object - jQuery.jqfactory - that accepts 2 or 3 arguments.  The first argument to jqfactory is a string containing a namespace and the widget name, separated by a dot.

 - The namespace is mandatory, and it refers to the location on the global jQuery object where the widget instance properties will be stored. If the namespace does not exist, jqfactory will create it for you.

 - The second argument is an object that is used to set the plugin instance properties.

 - The third argument is a boolean that determines if the namespace, provided in the first argument, is included on the jQuery prototype along with the plugin name.  (eg: $('.example').bootstrap().tooltip())

**_create()** - Function
_Description:_
- The first method called when your jQuery plugin is initialized.  Acts as your plugin constructor function.

**_render()** - Function
_Description:_
- Called after the `_create()` method.  Meant to be where all of your plugin dom manipulation initialization happens.

**_events** - Object
_Description:_
- All events within this object are bound after the `_render()` method is called.

_Examples:_
- Supports event delegation:
```javascript
'.test click': function(){}
```
- Supports direct event binding:
```javascript
'!.test click': function(){}
```
- Supports special events: 
```javascript
'superfantastic': function(){}
```
- Supports automatic binding if there is a this.element property:
```javascript
'click': function(){}
```

**_postevents()** - Function
_Description:_
- Called after all events from the `_events` object are bound.

**_on(String selector or Object, Function)** - Function
_Description:_
- Binds/Delegates event handlers using the correct event namespace and binds the correct this context within the callback function

_Examples:_
- Single event binding :
```javascript
this._on('.test click', function(){});
```
- Multiple event binding:
```javascript
this.on({
        '.test click': function(){},
        '.test mouseenter': function(){}
    });
```

**_off(String selector or Array)** - Function
_Description:_
- Unbinds/Undelegates event handlers using the correct event namespace

_Examples:_
- Single event unbinding :
```javascript
this._off('.test click');
```
- Multiple event unbinding:
```javascript
this.off(['.test click', '.test mouseenter']);
```

**_trigger(String selector or Array)** - Function
_Description:_
- Triggers an event using the correct event namespace

_Examples:_
- Element Event Trigger:
```javascript
this._trigger('.test', 'click');
```
- This.element Event Trigger:
```javascript
this._trigger('click');
```

**_superMethod(String methodName, arguments)** - Function
_Description:_
- Call's a jqfactory method and correctly sets the context to the plugin instance

_Examples:_
- Method with no arguments:
```javascript
this._superMethod('destroy);
```
- Method with one argument:
```javascript
this._superMethod('option', 'exampleOption');
```
- Method with two arguments:
```javascript
this._superMethod('option', 'exampleOption', true);
```

**delay(String methodName or Function method, Number delay)** - Function
_Description:_
Delays the execution of a method by the number of milliseconds specified in the second argument (essentially setTimeout).Defaults to 0.

_Examples:_
- Specifying a method with a string:
```javascript
this.delay('destroy', 2000);
```
- Specifying a method with a function:
```javascript
this.delay(this.destroy, 2000);
```

**disable()** - Function
_Description:_
Set's the `disabled` option to to `true` and triggers a special `disable` event on the element that called the plugin.  This method is meant to be overriden and then called within the overriden method.  Like this:

`_Example:_`
```javascript
disable: function() {
    // Your custom disable logic goes here
    this._superMethod('disable');
}
```

**enable()** - Function
_Description:_
Set's the `disabled` option to to `false` and triggers a special `enable` event on the element that called the plugin.  This method is meant to be overriden and then called within the overriden method.  Like this:

`_Example:_`
```javascript
enable: function() {
    // Your custom disable logic goes here
    this._superMethod('enable');
}
```

**destroy()** - Function
_Description:_
Helps with memory clean-up of your plugin by unbinding all events with your plugin's event namespace and removing all event handlers bound in the `_events` object.  Also triggers a special `destroy` event on the element that called the plugin.  This method is meant to be overriden and then called within the overriden method.  Like this:

`_Example:_`
```javascript
destroy: function() {
    // Your custom disable logic goes here
    this._superMethod('destroy');
}
```

**option()** - Function
_Description:_
This  method combines the functionality of `setOption(String key, String val)`, `setOptions(Object props)`, and `getOption(String key)` methods.  Depending on which functionality is used, a `getOption`, `setOption`, or `setOptions` event is triggered on the element that called the plugin.

_Examples:_
- Get a single option:
```javascript
this.option('someOption');
```
- Set a single option:
```javascript
this.option('someOption', 'example');
```

- Set multiple options:
```javascript
this.option({ 'someOption': 'example', 'someOtherOption': 'anotherExample' });
```

 ##Changelog
 > 0.1.0 - June 10, 2013
  - Initial release!

 ##Contributors
 Greg Franko

 ##License
 Copyright (c) 2013 Greg Franko Licensed under the MIT license.


