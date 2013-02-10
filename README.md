CoffeeScript Autocomplete Plus
==============================

CoffeeScript Autocomplete Plus (CA+) is a [Sublime Text 2](http://www.sublimetext.com/2) plugin that scans your CoffeeScript files on demand and makes autocomplete suggestions for you.

In addition, this plugin adds the "Coffee: Goto Definition" command, which will look up the class, function or variable definition of the selected token. 

Do you miss the old days? When, at the control-tap of a spacebar, your IDE joyfully came running to you, list in hand, asking, "what would you like, sir?" And that list actually made sense? And suggested items you were interested in? I missed those days, so I decided to do something about it. I learned about [Sublime Text 2](http://www.sublimetext.com/2). Best decision of my life. Then, I learned how to make plugins. My first order of business was to make this plugin&mdash;and now it's available for you to use.

Autocompletions. Zooming to definitions.

Let start coding again like the old days. With *style*.

Installation
------------

### Package Control

It is recommended that you use [Sublime Package Control](http://wbond.net/sublime_packages/package_control) to install CoffeeScript Autocomplete Plus. If you have Package Control installed, use the Package Control: Install Package command and search for CoffeeScript Autocomplete Plus. Ouila. Start coding easier.

### Manual Installation

In order to manually install CoffeeScript Autocomplete Plus, clone the repository into your Sublime Text 2 `Packages` directory, which can be located by navigating to Preferences -> Browse Packages. Name the directory `CoffeeScript Autocomplete Plus`.

For your convenience:

```
git clone https://github.com/justinmahar/SublimeCSAutocompletePlus.git "CoffeeScript Autocomplete Plus"
```

Features
--------

### Autocomplete

Autocomplete will make suggestions when you trigger autocomplete after a dot operator. It starts with the current view, then branches out to other coffee files. Because of this, most lookups are blazingly fast. You can configure CA+ to exclude certain directories and to only look in others. This will further increase speed as less searching will be needed.

Autocomplete suggestions alphabetically show properties first, as indicated by a &#x25CB; symbol before each property name, followed by alphabetically sorted methods, as indicated by a &#x25CF; symbol before each method name. Inherited properties and methods will be preceded by a &#x2C75; symbol. This creates a nice visual breakdown of what you can do at any given time, and where things are coming from. Badass.

For example:

	○ property1
	○ propertyTwo
	○ someOtherProperty
	● methodOne()
	● methodTwo(arg1, arg2)
	● lastMethod(arg1, arg2, arg3)
	Ⱶ○ inheritedProperty1
	Ⱶ○ inheritedPropertyTwo
	Ⱶ● inheritedMethod1()
	Ⱶ● inheritedMethod2(arg1, arg2)

Here are the main features. In the examples, `[autocomplete]` represents the user action of triggering autocomplete, which is typically `ctrl+space`.

* Suggests instance properties and methods when operating on an instance.

		myLamp = new LavaLamp()
		# Suggests all instance properties and methods of LavaLamp.
		myLamp.[autocomplete]

* Suggests static properties and methods when operating on a class.

		# Suggests all static properties and methods of LavaLamp.
		LavaLamp.[autocomplete]

* Supports "this" keyword and any defined aliases.

		class LavaLamp
			heatUp: ->
				console.log "Heating up!"
			coolDown: ->
				console.log "Cooling down!"
			moveBlobs: ->
				# Suggests heatUp() and coolDown() methods
				this.[autocomplete]

* Any variable assigned to `this` in the constructor will be considered an instance property.

		class LavaLamp
			constructor: (@color, size) ->
				this.size = size
			moveBlobs: ->
				# Suggests color, size and moveBlobs()
				this.[autocomplete]

* Suggests super class properties and functions. This applies to both instance and static suggestions.

		# In the following example, we have a LavaLamp that extends Appliance.
		# Each class has static and non-static properties and methods.
		# Given the context, CA+ will suggest either static or non-static,
		# and will walk up the class hierarchy.

		class Appliance
			# Static
			@WARRANTY_YEARS = 10
			@calculateWarrantyExpiration: (currentYear) ->
				console.log "Expires: ", currentYear + Appliance.WARRANTY_YEARS
			# Non-static
			constructor: ->
				this.isSwitchedOn = False
			toggle: ->
				this.isSwitchedOn = !this.isSwitchedOn

		class LavaLamp extends Appliance
			# Static
			@BEST_COLOR = "Red"
			@isLampAwesome: (lamp) ->
				if lamp.color == LavaLamp.BEST_COLOR
					console.log "Definitely"
				else
					console.log "Probably"
			# Non-static
			constructor: (@color, size) ->
				this.size = size

			moveBlobs: ->
				# Suggests color, isSwitchedOn, size, moveBlobs(), toggle()
				this.[autocomplete]

				# Suggests WARRANTY_YEARS and calculateWarrantyExpiration(), which are static.
				Appliance.[autocomplete]

				# Suggests BEST_COLOR, WARRANTY_YEARS, calculateWarrantyExpiration(), and isLampAwesome()
				# These are all static properties from the complete class heirarchy.
				LavaLamp.[autocomplete]

* After autocompleting a method, tab stops for parameters are provided (if applicable).
* Expects that you don't suck at naming things. Will assume a class is UpperCamelCase and everything else is lowerCamelCase. It still works either way; it will just be faster if things are named properly.
* For every 1 million autocompletions, a beautiful masseuse appears and gives you a massage. You must be tired after all that coding.

### Support For Built-in Types

CA+ now supports autocompletion for the following built-in types:

* Array
* Boolean
* Date
* Function
* Number
* Object
* RegExp
* String

This makes life that much easier.

In addition, autocomplete suggests inherited properties and methods from Object.

### Custom Types

If you find yourself using a third-party library often and would like to have autocompletions for that library, you can create custom type. This is essentially a collection of autocomplete suggestions. CA+ supports jQuery (partially -- under development) out of the box, but you can always add others. If you do add a custom type and would like to share it, please issue a pull request and I will add it to the plugin. If your library is used as function, like jQuery, you can map that function's return value to a type. See the configuration section for how to do this.

### Type Hinting

* Variable declaration type hints
  - When you assign a variable, you can add a type hint that CA+ will use to look up the class autocompletions for you. This type must be in square brackets, and in a single-line `#` comment on either the same line or the previous line, in the form `[TYPENAME]`. Other text can surround the type hint. This works for assignments only.

```
	# The [String] to add type hinting for
	myString = someObject.getSomething()

	# Now, CA+ knows that it's a String
	myString.[autocomplete]

	# Alternate way to add type hinting
	otherString = someObject.getSomethingElse() # It's a [String], son.
```

* Parameter type hints
  - If you use [Codo](https://github.com/netzpirat/codo), or are in the habit of documenting your classes, you can add type hints for your method parameters as well. These hints must be in a single-line `#` comment above the method signature. The hint can either be in the form `[TYPENAME] parameterName` or `parameterName [TYPENAME]`. Other text can surround the type hint.

```
	# [Animal] critter
	# [Boolean] animalEscapes
	feedAlligator: (critter, animalEscapes) ->
		if animalEscapes:
			# CA+ knows that it's an Animal. Will he escape? Autocomplete!
			critter.[autocomplete]
```

### Goto Definition

Goto Definition is useful for finding where a class, function, or variable was defined or declared. Again, searching is performed from the current view and branches out to other files if nothing is found. With this, you can quickly jump between classes and zoom to functions&mdash;even ones defined in other files&mdash;with ease.

* Supports classes, functions and variable assignment.
* Searches backwards from selected token for assignment, then forwards.
* Considers variables declared in for loops.
* Considers method parameters.
* Tries to find something rather than nothing.
* Includes both mouse and keyboard shortcuts for convenience. Code your way.

### General 

* Generally fast lookups.
* You can configure directories to be be excluded from global .coffee search **(recommended)**
* You can configure the tool to only search in specific locations **(recommended)**

Usage
-----

### Autocomplete

Autocomplete can be triggered in coffee files after the dot operator. With the cursor after a dot operator `.`, press `ctrl+space`. This will trigger autocomplete, which will then try to figure out what you're doing and propose a list of suggestions.

Example usage: Inside a class, you type `this.` and would like a list of the available methods and properties. Press `ctrl+space` to trigger autocomplete, and view the available suggestions.

### Goto Definition

Looking for where a class, function or variable was defined? Look no further.

Place your cursor on any word and press `ctrl+alt+d` in Windows/Linux, and `ctrl+alt+d` in OS X, to goto the definition of the selected class, function or variable.

Alternatively, use `ctrl+alt` + `left click` in Windows/Linux, and `ctrl+alt` + `left click` in OS X.

Default Key Bindings
--------------------

### Windows/Linux:

Autocomplete: `ctrl+space` (after a dot operator)

Goto Definition: `ctrl+alt+d` or `ctrl+alt`+`left click`

### Mac OS X:

Autocomplete: `ctrl+space` (after a dot operator)

Goto Definition: `ctrl+alt+d` or `ctrl+alt`+`left click`

Key bindings can be changed by navigating to Preferences -> Package Settings -> CoffeeScript Autocomplete Plus -> Key Bindings. It is not recommended that you change the Default settings as they will be overwritten by plugin updates. Instead, make your changes in User settings, which will override the Default settings.

Configuration
-------------

CoffeeScript Autocomplete Plus has the following configurable settings:

In `CoffeeScript Autocomplete Plus.sublime-settings`:

* `coffee_autocomplete_plus_excluded_dirs` -- Excluded directories.
  - Directories to exclude from searching for CoffeeScript classes, functions and variables. All directories in your project are searched except for the ones that match the names listed in this setting. **This is recommended.** Some excluded directories have already been specified in settings.
  - Expected value: Array of directory names. 
  - Example: 

```
		"coffee_autocomplete_plus_excluded_dirs" : [".git", "SVN", "pub", "docs"]
```

* `coffee_autocomplete_plus_restricted_to_paths` -- Restrict searching to these directories.
  - Paths to restrict the search to. If one or more path is specified, searching will be restricted to `PATH_TO_PROJECT/your/defined/path`. For example, you specify `src/coffee`. Now, only `PROJECT_PATH/src/coffee` will be scanned. **This is recommended.** If `[]`, no restrictions are applied.
  - Expected value: Array of paths relative to your project directory.
  - Example:

```
  		"coffee_autocomplete_plus_restricted_to_paths" : ["src/coffee", "lib/src/coffee"]
```

* `coffee_autocomplete_plus_this_aliases` -- Aliases for `this` keyword
  - Due to lexical scoping you sometimes need to assign an alias for `this`, such as `that` or `self`. This config setting allows you to add or remove aliases that cause autocomplete to behave just like using `this` would. I aim to remove this setting with smarter assignment detection, but for now, it's convenient to have.
  - Expected value: Array of alias strings.
  - Example:

```
  		"coffee_autocomplete_plus_this_aliases" : ["that", "self"]
```
 
* `coffee_autocomplete_plus_function_return_types` -- Mappings for the return types of special functions, like jQuery.
  - You may want to make calls directly off of the returned values of special functions. For example, when using jQuery, you might type `$(selector).` and want some autocomplete suggestions. If you have a custom type defined, you can map the returns of function names, like `$` and `jQuery`, to that custom type. See the next config section for defining custom types.
  - Expected value: Array of objects. Each object has a `function_names` property that maps to an array of string names, and a `type_name` property that maps to the string name of the type the functions return.
  - Example:

```
  		"coffee_autocomplete_plus_function_return_types": 
	  	[
			{
				"function_names": ["$", "jQuery"],
				"type_name": "$"
			}
		]
```

In `CoffeeScript Autocomplete Plus Custom Types.sublime-settings`:

* `coffee_autocomplete_plus_custom_types` -- Custom types, allowing libraries like jQuery.
  - If you would like to define custom types, put them here. Autocomplete will then make the defined suggestions for you. By default, jQuery is defined (partially completed).
  - Use the `enabled` property to enable or disable a custom type.
  - Use the following example as a starting point:

```
		{
			"name": "MyType",
			"enabled": true,
			"constructor": {"args": []},
			"static_properties": [{"name": "CONSTANT_ONE"}, {"name": "CONSTANT_TWO"}],
			"static_methods": 
			[
				{"name":"myStaticMethod", "args": [{"name": "arg1"}, {"name": "arg2"}]},
				{"name":"myStaticMethodTwo", "args": []}
			],
			"instance_properties": [{"name": "thingOne"}, {"name": "thingTwo"}, {"name": "thingThree"}],
			"instance_methods":
			[
				{"name":"doStuff", "args": [{"name": "numTimes"}]},
				{"name":"doMoreStuff", "args": [{"name": "numTimes"}, {"name": "extraAmount"}]}
			]
		}
```

In `CoffeeScript Autocomplete Plus Built-In Types.sublime-settings`:

* `coffee_autocomplete_plus_built_in_types` -- Built-in types, like Object, Array, and String.
  - These are JavaScript's build-in types. These specs were gleaned from the [Mozilla MDN](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects).
  - Any of the built-in types can be disabled, if you'd like. Change the `enabled` property to `false`.

To configure these settings, open Preferences -> Package Settings -> CoffeeScript Autocomplete Plus.  It is not recommended that you change the Default settings as they will be overwritten by plugin updates. Instead, make your changes in User settings, which will override the Default settings.

Limitations and Plans
---------------------

> "Conceal a flaw, and the world will imagine the worst." — Marcus Aurelius, 16th Emperor of the Roman Empire

Autocomplete is smart, but not Mensa smart. Under the hood, we're using regular expressions and lots of scanning. I coded this rather quickly, and it's my first plugin, so there may be (read: probably are) bugs. Please let me know if there are. As I build out functionality, I will try to fix its limitations.

For now, here is the list of TBDs:

* Finish jQuery custom type
* Even smarter assignment detection (methods, where they came from)
* Constructor support
  - Add constructors to built-in types
* Additional built-in types (Error, etc)

Far too many times I've installed a plugin only to be disappointed because it fell short of my expectations. If you feel this way, please let me know how I can make this plugin better for you and I will do my best.

Happy coding!

-Justin

License
-------
CoffeeScript Autocomplete Plus is licensed under the MIT license.

Copyright (c) 2013 Justin Mahar <justin.m.mahar@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.