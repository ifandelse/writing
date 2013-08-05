Ever wondered what your options are when it comes to storing data on the client in your web application? Browser support, API features, storage size - like so many DOM features, it can be difficult to know what's available, what browsers support it, and which option is the best fit for your needs. We'll take a look at the basics of each major type of client-side storage in this post, look at some of the pros and cons, and discuss some guidelines to keep in mind as you brings these tools to bear in your applications.

#First - let's fill the gaps
For a long time, the web limped along with one primary form of client side storage: cookies. Browsers typically limited cookies to a size of 4KB, allowing 20 per domain. Given today's standards, that's some tiny storage space - and it comes at the (potentially high) price of the cookies being sent with each HTTP request.

Thankfully, browsers didn't stop there:

* The ["Web Storage" spec](http://dev.w3.org/html5/webstorage/) – introduced in 2009 – provided APIs for `sessionStorage` & `localStorage`. The name `localStorage` has become nearly synonymous in usage with "Web Storage" - and you will often hear it used instead. Most implementations give you up to 5MB of key/value storage.
* [Indexed DB]() (called an "indexed table system") is a key/value store – not a relational database like Web SQL. You can persist JavaScript objects to an "object store" - but it does not require you to define a schema ahead of time. In addition, you can define indexes to improve performance.
* Chrome 27+, Opera 15+ and Blackberry 10 have a [FileSystem API]() as well. While the opportunities to utilize the FileSystem API won't be as common as the other scenarios, this option is especially useful when local storage and Indexed DB fall short of your needs.
* If you're writing hybrid mobile applications – using a framework like Apache Cordova (a.k.a. - "PhoneGap") – in addition to having access to features like local storage (assuming the mobile webview supports it) you have a [File API]() that allows you to read & write from the device's file system.
* Though it's currently deprecated (as of 2010), you may still come across applications making use of a [Web SQL database](). Web SQL - supported primarily in Webkit browsers - provided a means to manipulate client-side data storage using a variant of SQL. It comes with all of the power and headaches of working with a relational database - sporting larger size limitations than local storage (Safari, for example, let Web SQL DBs get up to 50MB). The W3C abandoned Web SQL databases in favor of the [Indexed DB]() specification.

#Web (Local & Session) Storage
Of the storage options we'll cover, Web Storage is the most widely supported option available to you as of today. The Web Storage spec gives us `sessionStorage` and `localStorage` – which share the same API (both implement the `Storage` prototype). `sessionStorage` is used to store data for the duration of the current browser session. It will persist between page reloads, but will be cleared when the browser is closed. Data stored in `localStorage` is persistent. It will only be cleared when the user specifically clears it via browser tools, or when your code removes items previously stored, etc.

Are there any real advantages of using `sessionStorage` over `localStorage`? In my opinion, no. The typical use-case for `sessionStorage` would be when you need to persist data in a form, for example, because the user might accidentally refresh the page. The problem, though, is that the user might also accidentally close the browser. If you were relying on `sessionStorage` in case of a refresh, and the user closes the browser instead…Oops. `localStorage` handles both issues well. Bottom line: you will probably find `localStorage` to be the best option of the two 99.9% of the time.

Let's look at an example. First - the JavaScript:

	// cache our selectors
	var $name = $("#fullName");
	var $loc = $("#location");
	var store = {
		// store our input values as members of an object that gets
		// serialized, since local storage stores *strings*
    	saveToStorage: function() {
        	localStorage.setItem("example", JSON.stringify(this.getInputValues()));
	    },
	    // a bit naive, but we parse the JSON into an object
	    // and if we get something, we set our input values
    	loadFromStorage: function() {
        	var store = JSON.parse(localStorage.getItem("example"));
	        if(store) {
    	        $name.val(store.fullName);
        	    $loc.val(store.location); 
	        }
    	},
	    clearFields: function() {
    	    $name.val("");
        	$loc.val("");
	    },
    	clearStorage: function() {
        	localStorage.clear();
	    },
    	getInputValues: function() {
        	return {
            	fullName: $name.val(),
            	location: $loc.val()
	       }
    	}
	};
	// hook up event handlers
	$("#setStorage").on("click", $.proxy(store.saveToStorage, store));
	$("#loadStorage").on("click", $.proxy(store.loadFromStorage, store));
	$("#clearFields").on("click", store.clearFields);
	$("#clearStorage").on("click", store.clearStorage);
	
You can see from the above code that the `localStorage` API is quite simple. In our `saveToStorage` method we're calling `localStorage.setItem` and passing the key and value as arguments – saving our serialized object under the key "example" (we have to serialize our object because `localStorage` only stores string values.) Then, in our `loadFromStorage` method, we call `localStorage.getItem`, passing the key of the item we want. Of course, we get a string back, which we need to parse into an object. Further down, you'll notice we're calling `localStorage.clear`, which will remove *all* `localStorage` data for this origin. It's possible to remove specific keys, however, by calling `localStorage.removeItem(key)`.
	
Take the example for a test drive:
<iframe width="100%" height="300" src="http://jsfiddle.net/ifandelse/NPKUJ/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
	
##Important Details About Local Storage
* `localStorage` stores *strings*. This means that you will need to serialize your objects before storing them if you intend to persist something other than a string.
* The [Same Origin](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Same_origin_policy_for_JavaScript) policy rules are in effect. This means you cannot access the local storage from another domain. Keep in mind that same-orign rules apply even to different ports or [schemes](http://en.wikipedia.org/wiki/URI_scheme) from the *same* domain. In other words, http://my.domain.com *cannot* access local storage for https://my.domain.com AND http://my.domain.com:80 *cannot* access http://my.domain.com:8088. 
* Careful - *you can run out of space*. Many browsers allow for 2.5-5MB of local storage per domain. Bearing in mind that JavaScript strings are UTF-16, and thus take 2 bytes per character, a 5MB limit means you can store 2.5MB of text. [Here](http://dev-test.nemikor.com/web-storage/support-test/) is an informative test runner that will show you the amount of text data you can store per browser. From what I've researched, Opera and Firefox are the only browsers that currently allow you increase the `localStorage` size limit (though Chrome allows this for apps downloaded via the Chrome Web Store).
* The [Storage Event](http://www.w3.org/TR/2011/WD-webstorage-20110208/#event-storage) isn't quite what you'd expect. Most browsers only fire the `storage` event if a *different* window changes local storage (IE 10 will fire the event for same-window changes, but Chrome and Safari do not, for example). So don't expect the event to be fired for changes within the same window.
* Simplicity comes with trade-offs:
	* It's easy to store and retrieve keys, but you're on your own when it comes to searching/filtering data.
	* The need to proactively serialize, and the fact that the API is synchronous *might* cause you performance headaches. I've never had an issue here, though.
	* Since you aren't required to define a schema, validating the stored data is up to you. 
	
Overall - `localStorage` can be a reliable workhorse - with older browser support often made possible via polyfills. The **good** news is that current support looks good:
<iframe src="http://caniuse.com/namevalue-storage/embed/" width="100%"></iframe>

The **bad** news is – as with ANY of these client-side storage options – you should *never* assume that the data will be truly persistent (after all, the user can nuke it from orbit if they desire), and you should be careful as to the kinds of data you store (it's not hard for any user to view stored data via browser tools).

#Indexed DB

#Using the File System
## Normal Web Clients
## Hybrid Mobile Apps
## Chrome Web Store Apps?

##Web SQL
As I mentioned earlier, Web SQL has been deprecated, so we're not going to spend much time on it. However, there are quite a number of applications in the wild using this feature, so you may run into one.

#Wrapping Up