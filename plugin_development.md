# Writing Plugins

Extending the parsing ability of Knwl.js is done through parser plugins.

Parser plugins are what allow Knwl.js to parse through text. Without them, Knwl.js is merely a shell.
The default plugins provided are likely the most commonly parsers, and include plugins for things like dates, times, and places. There are
also a number of experimental plugins. These are unpolished and often lack the accuracy of the default plugins, however,
they should still be stable.

If you are wanting to make a plugin that offers a new parsing ability to Knwl.js, this document will help you get started.

##Basic Plugin Template

Here's a basic template for a parser plugin:

```javascript

	function PluginName(knwlInstance) {
		
		this.languages = { //supported languages
			english: true	
		};
		
		this.calls = function() {
			
			var words = knwlInstance.words.get('words'); //get the String as an array of words
			var resultsArray = [];
			
			/*
				
				Parser Code
			
			*/
			
		    return results;
		};
	}
	
	module.exports = PluginName;


```

1. When your plugin is loaded using ```Knwl.register()```, it is passed the instance of the Knwl. In this object, you can access the APIs of Knwl.js.

	```javascript
		function PluginName(knwlInstance) {
	```
2. Your plugin should specify the languages it supports by their full names in an object ```languages```.
	```javascript
		this.languages = { //supported languages
			english: true,
			spanish: true
		}
	```
	Your plugin can check the language of the Knwl.js instance with the property ```Knwl.language```. If the instance of Knwl.js does not have a specified language,
	this property will have a value of ```unknown```.

3. A ```.calls()``` method must be provided for ```knwl.get()``` to call your plugin.
	```javascript
		this.calls = function() {
	```
	
4. Your parser plugin should use the database of words provided by the Knwl.js Instance to parse.
	```javascript
		var words = knwlInstance.words.get('words');
	```
	```Knwl.words.get()``` may be used to retrieve one of three different versions of the String data.
	1. ```"words"``` an array containing the String broken into words with punctuation removed and in lowercase.
	2. ```"linkWords"``` an array of the words in lowercase with punctuation intact.
	3. ```"linkWordsCasesensitive"``` an array of the words with punctuation intact and in original case.
	
5. Your plugin should expect to find multiple results when parsing. Thus, your plugin should return an array that contains the results.
	```javascript
		return results;
	```
	
	Individual results should be stored as an object in this array, with a format similar to this:
	```javascript
		var result = {
			label: value,
			label: value,
			
			preview: knwlInstance.tasks.preview(foundPosition),
			found: foundPosition
		}
	```
	
	The first properties are specific to each plugin, however, ```"preview"``` is
	a standard property needed for all results. This property contains a sentence, or a portion of a sentence,
	generated by ```Knwl.tasks.preview()``` of the result's position in the String. 
	
	The first parameter of ```Knwl.tasks.preview()``` is the position of the result in the words database.
	
	Finally, ```"found"``` is the position of the result
	in the word database.

## Knwl.register()

```Knwl.register()``` is used by someone wanting to use your plugin to load it into their web app. This method creates a new instance of your plugin and passes the Knwl.js Instance in the first parameter.

## Knwl.get()
```Knwl.get()``` is the method responsible for calling your plugin. When a web application invokes this method with a plugin name in the first parameter,
```knwl.js``` looks for that plugin and then calls it, passing any parameters provided to ```knwl.get()``` to the init function of the plugin.

Here's an example:

```javascript
knwl.init("This is a string that contains a phone number: 5223241324"); //initialized on String

var results = knwl.get('phones', 'parameter2', 'three');

/*

	# Passed to plugin
	knwl.get('phones', 'parameter2', 'three') --> plugin('phones', 'parameter2', 'three')
	
	# Plugin returns results
	plugin --> knwl.get() --> app

*/

console.log(results);

```

## Standards and Best Practices

There are a few standards we ask (nicely) you keep in mind by to ensure a coherent experience for those wanting to use your plugin.

1. Parser plugins should be contained within one file.

2. Plugins should not modify the global ```Knwl.words``` database.
When ```Knwl.init()``` is called on a string, Knwl.js breaks the string appart into its component parts. It then stores
these parts as properties in a global object: ```Knwl.words{}```. Your plugin should retrieve its required data from
this database, however, be careful not to modify the data contained within. If this happens, plugins that run after your own will
not access the originally intended data, but instead, the changes your plugin made. This can cause plugins to become inaccurate.

3. Consider publishing documentation for your plugin if you plan to release publicly. Documentation should show the user how to use ```Knwl.get()``` to initiate the plugin, e.g. ```knwl.get('dates')```.

4. Make something awesome.

Oh, and if you build something, let us know! We'd love to feature your plugin on the Knwl.js website one day soon: knwl@loadfive.com

## Publishing

If you intend on releasing your parser plugin publicly, the best and most approachable way is to create a GitHub Repository for the plugin.
In the near future, we plan to release a website for browsing community plugins created for Knwl.js, and to do so, plugins will need
to be hosted.

We'll be updating everyone on the status of the website on the G+ community (https://plus.google.com/communities/108874785204246812675).

## Further Help

We'd recommend anyone developing plugins for Knwl.js to look at preexisting plugins first for inspiration.

If you need more granular assistance in developing your plugin, or this documentation doesn't make any sense, no problem! You can reach us at anytime here: knwl@loadfive.com.

We also have a **Google+ Community**, where you can discuss plugins and more: https://plus.google.com/communities/108874785204246812675.
