# Writing Next Generation JavaScript Libraries - Part 1

Excited to take advantage of new JavaScript language features - but not sure _where_ to start, or _how_? You're not alone! I've spent the better part of the last year and half trying to ease this pain. During that time there have been some amazing quantum leaps in JavaScript tooling. These leaps have made it possible for you and I to dive head first into writing fully-ES6 libraries, without compromising on the essentials like testing, linting and (most importantly) the ability for others to consume what we write with ease.

> Wait, is it ES6 or ES2015? My habits certainly prefer 'ES6', but the name was recently & officially changed to 'ES2015'. Given how long we knew it as "ES6", there's a greater level of awareness with that name, hence why I'm using "ES6" in this post.

## The Tools

Over the course of parts 1 and 2 of this series, we'll look at some of the tools that make this possible. Today we'll cover writing, transpiling and packaging our library, and in part 2 we'll focus on linting, formatting and testing. Meet your new best friends for part 1:

* [Babel](http://babeljs.io/) (which is nearing it's first birthday) has made the process of transpiling ES6 to ES5 not only simple, but _pleasant_.
* [Webpack](http://webpack.github.io/) silenced every aspect of the "module wars" on my team by letting us consume _everything_ (CommonJS, AMD & ES6) with aplomb. It turns out that Webpack also does a fantastic job of packaging stand-alone ES6 libraries - a fact we will look heavily at during this post.
* [Gulp](http://gulpjs.com/) is a powerful too for automating build-related tasks.

> Note: we're going to talk about writing ES6 client-side *libraries*, not bundling entire sites/apps. This is really any re-usable bit of code you'd like to share between projects, whether it's an OSS project or something you use internally at work between applications.

## Setting Up Our Project

You can see the code relevant to part 1 [here](). I usually start a project off with `src/`, `spec/` and `lib/` folders. In our `src/` folder, you'll see a contrived, but fun example set of modules that, when used together, let us retrieve a random quote from the [Lego Movie](http://www.imdb.com/title/tt1490017/). While the behavior is fairly useless, this example is making use of classes, modules, const and a generator - all features we'd like to safely transpile to ES5. Let's take a brief look at some of the code.

Don't worry if you're new to ES6 syntax. These examples are simple enough to follow.

### Class

In our `LegoCharacter.js` module, we see the following:

```
// Let's import only the getRandom method from utils.js
import { getRandom } from "./utils";

// the LegoCharacter class is the default export of the module, similar
// in concept to how many node module authors would export a single value
export default class LegoCharacter {
	// We use destructuring to match properties on the object
	// passed into separate variables for character and actor
	constructor( { character, actor } ) {
		this.actor = actor;
		this.name = character;
		this.sayings = [
			"I haven't been given any funny quotes yet."
		];
	}
	// shorthand method syntax, FOR THE WIN
	// I've been making this typo for years, it's finally valid syntax :-)
	saySomething() {
		return this.sayings[ getRandom( 0, this.sayings.length - 1 ) ];
	}
}
```

Pretty boring on its own – this class is meant to be extended:

```
import LegoCharacter from "./LegoCharacter";

// Here we use the extends keyword to make Emmet inherit from LegoCharacter
export default class Emmet extends LegoCharacter {
	constructor() {
		// super lets us call the LegoCharacter's constructor
		super( { actor: "Chris Pratt", character: "Emmet" } );
		this.sayings = [
			"Introducing the double-decker couch!",
			"So everyone can watch TV together and be buddies!",
			"We're going to crash into the sun!",
			"Hey, Abraham Lincoln, you bring your space chair right back!",
			"Overpriced coffee! Yes!"
		];
	}
}
```

### The Index.js

Our index.js – the "main" entry point for our library – imports a few Lego character classes, creates instances of them, and provides a generator function to `yield` a random quote anytime a caller asks for one:

```
// Notice that lodash isn't being import via
// relative path but all the other modules are.
import _ from "lodash";
import Emmet from "./Emmet";
import Wyldstyle from "./Wyldstyle";
import Benny from "./Benny";
import { getRandom } from "./utils";

// Taking advantage of new scope controls in ES6
// once a const is assigned, the reference cannot change.
// Of course, transpiling to ES5, this becomes a var, but
// a linter that understands ES6 can warn you if you 
// attempt to re-assign a const value, which is useful.
const emmet = new Emmet();
const wyldstyle = new Wyldstyle();
const benny = new Benny();
const characters = { emmet, wyldstyle, benny };

// Pointless generator function that picks a random character
// and asks for a random quote and then yields it to the caller
function* randomQuote() {
	const chars = _.values( characters );
	const character = chars[ getRandom( 0, chars.length - 1 ) ];
	yield `${character.name}: ${character.saySomething()}`;
}

// Using object literal shorthand syntax, FTW
export default {
	characters,
	getRandomQuote() {
		return randomQuote().next().value;
	}
};
```

## OK, Now What?

We have all this shiny new JavaScript, but how do we transpile it to ES5? Let's install Babel using npm:

```
npm install -g babel
```

Installing Babel globally gives us a `babel` CLI option. If we navigate to our project's root directory and type this, we can transpile the modules to ES5 and drop them in the `lib/` directory:

```
babel ./src -d ./lib/
```

A quick glance at these files might tell you a couple of things. First, these files could be consumed in node right now, as long as the babel/poyfill runtime dependency was required first (yes, transpiling typically involves a runtime dependency). Second, we still have some work to do so that these files can be packaged into one file and wrapped in a [Universal Module Definition (UMD)](https://github.com/umdjs/umd) (a module wrapper that allows the module to be used as a CommonJS, AMD or plain "browser global").

## Enter Webpack

Let's install webpack globally:

```
npm install -g webpack
```

Before we can use webpack, though, we need to create a webpack configuration file that tells webpack what we want it to do to our source files. We're not going to cover webpack in depth here, but it will help to explain a couple of key points first.

### Loaders

Webpack's docs describe loaders as "_transformations that are applied on a resource file of your app_". Loaders are used extensively in webpack, and they can do things like transpile ES6 to ES5, LESS to CSS, load JSON files, render templates and _much_ more. Loaders take a `test` pattern to use for matching files that it should transform. Many loaders can take additional configuration options as well, which we'll be making use of. (Curious as to what other loaders exist? Check [this list](http://webpack.github.io/docs/list-of-loaders.html) out.)

Lucky for us, `babel-loader` can enable webpack to load our ES6 modules and transpile them to ES5. We can install it, and save it to our package.json's `devDependencies` by running this:

```
npm install --save-dev babel-loader
```

### Output
A webpack configuration file can be given an `output` object that describes how webpack should build and package the source. In our case we'll be telling webpack to output a UMD library to our `lib/` directory.

### Externals
You might have noticed our example library uses lodash. We want our built library to be smart enough to require lodash, rather than have to include lodash itself in the output. The `externals` option in a webpack config file lets you specify the dependencies that should remain external. In the case of lodash, its global property key (`_`) is not the same as it's name ("lodash"), so our configuration (below) tells webpack how to require lodash for each given module scenario (CommonJS, AMD & browser "root").

Our first pass webpack.config.js file - which is just a plain node.js module – looks like this:

```
module.exports = {
    entry: "./src/index.js",
    output: {
        library: "legoQuotes",
        libraryTarget: "umd",
        filename: "lib/legoQuotes.js"
    },
    externals: [
        {
            lodash: {
                root: "_",
                commonjs: "lodash",
                commonjs2: "lodash",
                amd: "lodash"
            }
        }
    ],
    module: {
        loaders: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: "babel",
                query: {
                    compact: false
                }
            }
        ]
    }
};
```

You'll notice that our babel-loader just calls itself "babel" for the loader name. This is a webpack naming convention – where the module name is myLoaderName-loader, but webpack treats it as myLoaderName. We're testing for any file ending in js, except for files that live under our node_modules folder. The `compact` option we're passing to the babel loader turns off whitespace compression because I'd like our un-minified built source to be readable. We'll add a minified build in a moment.

If we run `webpack` in our console at the project root, it will see our `webpack.config.js` file and build our library, giving us output similar to this:

```
» webpack
Hash: f33a1067ef2c63b81060
Version: webpack 1.12.1
Time: 758ms
            Asset     Size  Chunks             Chunk Names
lib/legoQuotes.js  12.5 kB       0  [emitted]  main
    + 7 hidden modules
```

If we look in our `lib/` folder, we'll see a newly minted "legoQuotes.js" file. This time, the contents are wrapped in webpack's UMD:

```
(function webpackUniversalModuleDefinition(root, factory) {
	if(typeof exports === 'object' && typeof module === 'object')
		module.exports = factory(require("lodash"));
	else if(typeof define === 'function' && define.amd)
		define(["lodash"], factory);
	else if(typeof exports === 'object')
		exports["legoQuotes"] = factory(require("lodash"));
	else
		root["legoQuotes"] = factory(root["_"]);
})(this, function(__WEBPACK_EXTERNAL_MODULE_1__) {

// MODULE CODE HERE

});
```

This UMD performs one kind of CommonJS check, then an AMD check, then another style of CommonJS and finally it falls back to plain "browser globals". You can see how it knew to look for lodash as "lodash" if we were in a CommonJS or AMD environment, and to look for `_` on the window (root) if we were dealing with plain browser globals.

Looking at the module code you'll see that our ES6 source has been transpiled to ES5. For example, our LegoCharacter class is now an ES5 constructor function:

```
// around line 179
var LegoCharacter = (function () {
	function LegoCharacter(_ref) {
		var character = _ref.character;
		var actor = _ref.actor;
		_classCallCheck(this, LegoCharacter);
		this.actor = actor;
		this.name = character;
		this.sayings = ["I haven't been given any funny quotes yet."];
	}

	_createClass(LegoCharacter, [{
		key: "saySomething",
		value: function saySomething() {
			return this.sayings[(0, _utils.getRandom)(0, this.sayings.length - 1)];
		}
	}]);

	return LegoCharacter;
})();
```

### It's Usable!
At this point we could include this built file in both a browser (IE9+, as a general rule) and node, and it would work in either, as long as the babel polyfill was included.

In the browser, it could look like this:

<pre>
&lt;!-- the body --&gt;
&lt;div class=&quot;container&quot;&gt;
    &lt;blockquote id=&quot;quote&quot;&gt;&lt;/blockquote&gt;
    &lt;button id=&quot;btnMore&quot;&gt;Get Another Quote&lt;/button&gt;
&lt;/div&gt;
&lt;script src=&quot;../node_modules/lodash/index.js&quot;&gt;&lt;/script&gt;
&lt;script src=&quot;../node_modules/babel-core/browser-polyfill.js&quot;&gt;&lt;/script&gt;
&lt;script src=&quot;../lib/legoQuotes.js&quot;&gt;&lt;/script&gt;
&lt;script src=&quot;./main.js&quot;&gt;&lt;/script&gt;
</pre>

```
// main.js
( function( legoQuotes ) {
    var btn = document.getElementById( "btnMore" );
    var quote = document.getElementById( "quote" );

    function writeQuoteToDom() {
        quote.innerHTML = legoQuotes.getRandomQuote();
    }

    btn.addEventListener( "click", writeQuoteToDom );
    writeQuoteToDom();
} )( legoQuotes );

```

In node, like this:

```
require("babel/polyfill");
var lego = require("./lib/legoQuotes.js");
console.log(lego.getRandomQuote());
// > Wyldstyle: Come with me if you want to not die.
```


## Moving to Gulp
Did you notice that both babel and webpack have a CLI? You might be wondering if the other tools I mentioned do (mocha, JSCS, ESLint, etc.) – and the answer would be _yes_. You can stick to using the various CLIs, but it's common to use a task runner such as Gulp to handle executing them. This consistency can pay off if you participate in many projects, as the main CLI commands you'll need to remember consist of `gulp someTaskName`. I don't think there's a right or wrong answer here, for the most part. If you prefer the CLIs, use them. Using Gulp is simply one possible way to go about it.

### Setting Up a Build Task
First, let's install gulp:

```
npm install -g gulp
```

Next, let's create a gulp file that can run what have done so far. We'll use the `webpack-stream` gulp plugin, which can consume our webpack.config.js file.

```
// gulpfile.js
var gulp = require( "gulp" );
var webpack = require( "webpack-stream" );

gulp.task( "build", function() {
	return gulp.src( "src/index.js" )
		.pipe( webpack( require( "./webpack.config.js" ) ) )
		.pipe( gulp.dest( "./lib" ) )
} );
```

Since I'm using gulp to source our index.js and to write to the output directory, I've tweaked the webpack.config.js file by removing `entry` and updating the `filename`. I've also added a `devtool` prop, set to the value of `#inline-source-map` (this will write a sourcemap at the end of the file in a comment):

```
module.exports = {
	output: {
		library: "legoQuotes",
		libraryTarget: "umd",
		filename: "legoQuotes.js"
	},
	devtool: "#inline-source-map",
	externals: [
		{
			lodash: {
				root: "_",
				commonjs: "lodash",
				commonjs2: "lodash",
				amd: "lodash"
			}
		}
	],
	module: {
		loaders: [
					{
				test: /\.js$/,
				exclude: /(node_modules|bower_components)/,
				loader: "babel",
				query: {
					compact: false
				}
			}
		]
	}
};
```

### What About Minifying?

I'm glad you asked! One approach to minifying can be done using the gulp-uglify plugin, along with gulp-sourcemaps (since we'd like a sourcemap for our min file) and gulp-rename (which lets us target a different file name so we don't overwrite our non-minified build). In this approach, our unminified source will still have an inline source map, but our usage of gulp-sourcemaps below will cause the minified file's sourcemap to be written as a separate file (with a comment in the minified file pointing to the sourcemap file):

```
var gulp = require( "gulp" );
var webpack = require( "webpack-stream" );
var sourcemaps = require( "gulp-sourcemaps" );
var rename = require( "gulp-rename" );
var uglify = require( "gulp-uglify" );

gulp.task( "build", function() {
	return gulp.src( "src/index.js" )
		.pipe( webpack( require( "./webpack.config.js" ) ) )
		.pipe( gulp.dest( "./lib" ) )
		.pipe( sourcemaps.init( { loadMaps: true } ) )
		.pipe( uglify() )
		.pipe( rename( "legoQuotes.min.js" ) )
		.pipe( sourcemaps.write( "./" ) )
		.pipe( gulp.dest( "lib/" ) );
} );

```

If we run `gulp build` in our console, we should see something similar to:

```
» gulp build
[19:08:25] Using gulpfile ~/git/oss/next-gen-js/gulpfile.js
[19:08:25] Starting 'build'...
[19:08:26] Version: webpack 1.12.1
        Asset     Size  Chunks             Chunk Names
legoQuotes.js  23.3 kB       0  [emitted]  main
[19:08:26] Finished 'build' after 1.28 s
```

Our `lib/` directory will now contain three files: legoQuotes.js, legoQuotes.min.js and legoQuotes.min.js.map
 
### Webpack Banner Plugin
If you need to include a license comment header at the top of your built files, webpack makes it easy to do so. I've updated our webpack.config.js file to include the `BannerPlugin`. I don't like hardcoding the banner's information if I don't need to, so I've imported the package.json file to get the library's information. I also converted the webpack.config.js file to ES6, and am using a [template string]() to render the banner. Towards the bottom of the wepack.config.js file you can see I've added a `plugins` property, with the `BannerPlugin` as the only plugin we're currently using:

```
import webpack from "webpack";
import pkg from "./package.json";
var banner = `
	${pkg.name} - ${pkg.description}
	Author: ${pkg.author}
	Version: v${pkg.version}
	Url: ${pkg.homepage}
	License(s): ${pkg.license}
`;

module.exports = {
	output: {
		library: pkg.name,
		libraryTarget: "umd",
		filename: `${pkg.name}.js`
	},
	devtool: "#inline-source-map",
	externals: [
		{
			lodash: {
				root: "_",
				commonjs: "lodash",
				commonjs2: "lodash",
				amd: "lodash"
			}
		}
	],
	module: {
		loaders: [
			{
				test: /\.js$/,
				exclude: /(node_modules|bower_components)/,
				loader: "babel",
				query: {
					compact: false
				}
			}
		]
	},
	plugins: [
	 new webpack.BannerPlugin( banner )
	]
};

```

Our updated gulpfile includes two additions: the requiring of the babel register hook (on line 1) and the passing of options to the gulp-uglify plugin:

```
require("babel/register");
var gulp = require( "gulp" );
var webpack = require( "webpack-stream" );
var sourcemaps = require( "gulp-sourcemaps" );
var rename = require( "gulp-rename" );
var uglify = require( "gulp-uglify" );

gulp.task( "build", function() {
	return gulp.src( "src/index.js" )
		.pipe( webpack( require( "./webpack.config.js" ) ) )
		.pipe( gulp.dest( "./lib" ) )
		.pipe( sourcemaps.init( { loadMaps: true } ) )
		.pipe( uglify( {
			preserveComments: "license",
			compress: {
				negate_iife: false
			}
		} ) )
		.pipe( rename( "legoQuotes.min.js" ) )
		.pipe( sourcemaps.write( "./" ) )
		.pipe( gulp.dest( "lib/" ) );
} );

```
