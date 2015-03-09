# Five Qualities of Good Flux Implementations
It has been an exciting year for my team at LeanKit. Last May we kicked off a project using ReactJS, and over the course of the project, we've learned a lot about React and Flux - Facebook's recommended architectural principles for React apps. Today we'll take a look at some of the key lessons we've learned. Whether you're new to React & Flux, or going as far as building your own Flux implementation, I think you'll not only enjoy this journey with us, but find some thought-provoking questions and wisdom you can apply in your own endeavors.

## Helpful Background
This post assumes you have some level of familiarity with React and Flux. Already familiar with them? Feel free to skip to "What is Lux?". Otherwise, I recommend reading through the links below.

### React
[Telerik](http://developer.telerik.com/featured/introduction-to-the-react-javascript-framework/) recently called 2015 "the year of React". Here's a high level summary of React:

* React focuses on view concerns, and does not attempt to be an "everything framework"
* React UIs are built out of components.
* React components can be written using JSX - an XML-based extension to JavaScript, or with plain JavaScript.
* React components render to a "virtual DOM". Subsequent renders are diffed with the previous render, and the minimum number of DOM mutations are executed to effectively "patch" the DOM to bring it up to date.

Check out Facebook's [Getting Started](http://facebook.github.io/react/docs/getting-started.html) guide.

### Flux

Flux is an architectural pattern Facebook recommends for building apps with React. Where React's opinions *nudge* you towards uni-directional data flow, Flux provides a fuller picture as to what that *actually* looks like. Several Flux implementations have arisen (LeanKit's lux.js, included), providing a fascinating view into how different teams are tackling the challenges they face. A high level summary of Flux would include:

* Flux apps have three main abstractions: Views (React components), Stores and the Dispatcher.
* Views "propagate" Actions (e.g. - user interaction) through the Dispatcher.
* The Dispatcher handles notifying the various Stores of the Action.
* If a Store's state changes, it emits a change event, and Views depending on that Store for state will re-render.

Check out Facebook's [overview](http://facebook.github.io/flux/docs/overview.html#content) of Flux.


## What is Lux?

![](lux.png)

JavaScript developers crank out new frameworks as fast as a politician making promises at a campaign rally. Why, then, write another framework? I love this subject, though it falls outside the scope of this article. It's sufficient to say that our team has a specific set of needs, skills and goals that we've tailored lux to fit. We believe that a "stack" is superior to a "framework". Our work with lux attempts to strike a delicate balance between consistent opinions and flexibility to include other libraries that best solve for the concern at hand.

Over time, we've found these qualities to be the drivers of success in our own flux implementation:

* Don't get in React's way.
* Continously eliminate boilerplate.
* Treat every input as an Action.
* Store operations *must* be synchronous.
* Make it easy to "play well" with non-lux/non-React instances.

### Examples

Dmitri Voronianski created [flux-comparison](https://github.com/voronianski/flux-comparison), which lets you see a side-by-side comparison of several flux variants (using a basic shopping cart example). I've implemented the same example using lux to help illustrate the explanations along the way. I highly recommend checking this project out - it's a great way to quickly familiarize yourself with several leading Flux implementations.

OK - with all that out of the way, let's look at the "why" behind the qualities I mentioned above.

-------
## 1.) Staying Out of the Way
React does a great job at focusing only on what it aims to solve. By not being prescriptive on broader things like remote data communications (http, WebSockets), and by providing hooks that enable you to incorporate non-React UI libraries, you have the opportunity to assemble the tools that best address the needs of your app. Just as React stays out of the way of concerns it doesn't solve for, we've found it's equally important to stay out of React's way. It's easy to get in the way as you begin abstracting common patterns in how you use another library/framework. For example, let's look at the common component behaviors we've built into lux, and how our usage of them has evolved.

### Controller Views 
You will often hear React developers refer to "controller views" – a React component that typically sits at or near the top of a section of the page, which listens to one or more stores for changes in their state. As stores emit change events, the controller view updates with the new state and passes changes down to its children via props.

lux provides a `controllerView` method that gives you back a component capable of listening to lux Stores (and it's also an ActionCreator), for example: 

```
var CartContainer = lux.controllerView({

    getActions: [ "cartCheckout" ],

    stores: {
        listenTo: [ "cart" ],
        onChange: function() {
            this.setState(getStateFromStores());
        }
    },

    getInitialState: function () {
        return getStateFromStores();
    },

    onCheckoutClicked: function () {
        var products = this.state.products;
        if (!products.length) {
            return;
        }
        this.cartCheckout(products);
    },

    render: function () {
        return (
            <Cart products={this.state.products} total={this.state.total} onCheckoutClicked={this.onCheckoutClicked} />
        );
    }
});
```

While we still like this convenient approach, we've found ourselves moving to the alternative approach of standing up a plain React Component, and passing the lux mixins necessary to achieve the same result. Note that, here, we're calling `React.createClass` and using the `mixins` option:

```
var CartContainer = React.createClass({

    mixins: [ lux.reactMixin.store, lux.reactMixin.actionCreator ],

    getActions: [ "cartCheckout" ],

    stores: {
        listenTo: [ "cart" ],
        onChange: function() {
            this.setState(getStateFromStores());
        }
    },

    // other methods, etc.
});
```

Either approach is valid - though we feel the second approach is more "out of React's way". Why?

* We get a component's `displayName` for free (as the JSX transformer will use our var name when it sees `React.createClass`).
* Some controller views don't need to be ActionCreators. The second approach means we could only pass the `store` mixin in those cases, keeping concerns focused. The first approach always gives the component both mixins, even if not used.
* There's no need to explicitly pass the React instance to lux (done via `lux.initReact( React )`) so that it knows how to create components.

-----
## 2.) Boilerplate Elimination
In our experience, adopting React & Flux has moved infrastructure/framework concerns into the background so that we can focus on *actually creating features for our app*. Still, though, there are annoying bits of code that tend to crop up a lot. For example, consider this common approach to wiring/un-wiring components to listen to Store change events:

```
// Taken from the facebook-flux example: 
// https://github.com/voronianski/flux-comparison/blob/master/facebook-flux/js/components/CartContainer.jsx
var CartContainer = React.createClass({
    // only showing the methods we're interested in

    componentDidMount: function () {
        CartStore.addChangeListener(this._onChange);
    },

    componentWillUnmount: function () {
        CartStore.removeChangeListener(this._onChange);
    },
    
    // more methods, etc.
});
```

Honestly - the boilerplate tax isn't high here, but it's still present. Since mixins can provider component lifecycle methods, we made this automatic when you include lux mixins:

```

var ProductsListContainer = React.createClass({

	mixins: [ lux.reactMixin.store ],

    stores: {
        listenTo: [ "products" ],
        onChange: function() {
            this.setState(getAllProducts());
        }
    },

	// more methods, etc.
});
```

When our ProductsListContainer stands up, it will be wired up to listen to any of the store namespaces provided in the `stores.listenTo` array, and those subscriptions will be removed if the component unmounts. Goodbye boilerplate!

### ActionCreator Boilerplate
In Flux apps, you'll usually see dedicated ActionCreator modules like this: 

```
// snippet from: https://github.com/voronianski/flux-comparison/blob/master/facebook-flux/js/actions/ActionCreators.js
var ActionsCreators = exports;

ActionsCreators.receiveProducts = function (products) {
    AppDispatcher.handleServerAction({
        type: ActionTypes.RECEIVE_PRODUCTS,
        products: products
    });
};

ActionsCreators.addToCart = function (product) {
    AppDispatcher.handleViewAction({
        type: ActionTypes.ADD_TO_CART,
        product: product
    });
};
```

As we continually asked "what repeated code can we eliminate and replace with convention?", ActionCreator APIs kept coming up. In our case, we use postal.js for communication between ActionCreators and the Dispatcher. 99.9% of the time, an ActionCreator method published an Action message with no additional behavior. Things evolved over time like this:

```
// The very early days
// `actionChannel` is a ref to a postal channel dedicated to lux Actions
var ActionCreators = {
	addToCart: function() {
		actionChannel.publish( {
			topic: "execute.addToCart",
			data: {
				actionType: ActionTypes.ADD_TO_CART,
				actionArgs: arguments
			}
		} );
	}
};
```

That was very quickly abstracted into an ActionCreator mixin to enable this:

```
// The early-ish days
var ActionCreators = lux.actionCreator({
	addToCart: function( product ) {
		this.publishAction( ActionTypes.ADD_TO_CART, product );
	}
});
```

You'll notice two things in the above code: first, the use of `lux.actionCreator`, which mixes `lux.mixin.actionCreator` into the target and second, the `publishAction` method (provided by the mixin).

At the same time we were using the above mixin approach, we'd fallen into the practice of having matching handler names on our Stores (the handler method name matched the action type). For example, here's a lux Store that handles the `addToCart` Action:

```
var ProductStore = new lux.Store( {

    state: { products: [] },

    namespace: "products",

    handlers: {
        addToCart: function( product ) {
            var prod = this.getState().products.find( function( p ) {
                return p.id === product.id;
            } );
            prod.inventory = prod.inventory > 0 ? prod.inventory - 1 : 0;
        }
    },

    // other methods, etc.
} );
```

Matching Action type names and Store handler names made conventional wire-up very simple, but we saw another area we could eliminate boilerplate: if 99% of our ActionCreator API implementations just published a message, why not infer creation of ActionCreator APIs based on what gets handled by Stores? So we did, while still allowing custom implementations of ActionCreator methods where needed. For example, when the Store instance in the snippet above is created, lux will see that it handles an `addToCart` Action. If an ActionCreator API hasn't already been defined for this Action under `lux.actions`, lux will create one, with the default behavior of publishing the Action message.

Taking this approach means our components can specify what ActionCreator methods they want in an "a la carte" style. In this next snippet, our ProductItemContainer is using the `lux.reactMixin.actionCreator` mixin, which looks for a `getActions` array, and provides the specified Actions as top level methods on the component. You can see we're using the `addToCart` ActionCreator method in the `onAddToCartClicked` handler method.

```
var ProductItemContainer = React.createClass({

	mixins: [ lux.reactMixin.actionCreator ],

    getActions: [ "addToCart" ],

    onAddToCartClicked: function () {
        this.addToCart(this.props.product);
    },

    render: function () {
        return (
            <ProductItem product={this.props.product} onAddToCartClicked={this.onAddToCartClicked} />
        );
    }
});
```

As with any convention, there are trade-offs. Composition is important aspect of ActionCreator APIs. They should be modeled separate from the component(s) that use them. So far we believe this approach upholds that, while trading some of the explicit nature (e.g. – keeping ActionCreators in their own module) for flexibility & terseness.

-----
## 3.) Everything is an Action
Since this behavior of "providing action creator APIs" was abstracted into a mixin, it made it possible for both React components as well as "non-lux/react" instances to use the mixin. My team has been taking advantage of this when it comes to things like remote data APIs (we're using a hypermedia client called [halon]()). Our client-side wrapper for halon uses lux's `actionCreator` and `actionListener` mixins so that it can not only publish actions, but also handle them. 

We approach it this way because we believe *every input* - whether it be user input or queued async execution (via AJAX, postMessage, WebSockets, etc.) - *should be fed into the client as an Action*. If you've kept up with any of the React discussions over time, you might be thinking "Jim, Facebook is OK with calling dispatch directly on an XHR response, rather than use another Action Creator". Absolutely - and that makes perfect sense when your implementation gives your "util" modules (like remote data APIs) a handle to the Dispatcher. With lux, we opted for the gateway to the Dispatcher to be via message contract, and removed the need for the Dispatcher to be a dependency of any module.

So if every "input" is an Action, this means we might have Actions in our system that none of our stores care about. Other Actions might be of interest to both a store as well as our remote data API. The value of how this complements and forces you into the "pit of uni-directional data flow success" can be illustrated in this image:

![](luxdataflow1.png)

In the above scenario, a user clicked a button on the page that resulted in a server request. When the server responds, the response is published as a new Action. While we *know* that the two actions are related, modeling things this way reinforces the avoidance of cascading updates, *and* it means your app's behavior will be capable of handling data being *pushed* to it, not just *pulled* through http requests.

What if we wanted to update the UI to reflect that data is loading? It's as easy as having the appropriate store handle the same action:

![](luxdataflow2.png)

Another benefit about treating every input as an Action: It makes it easy to see what behaviors are possible your app. For example, here's the output of calling `lux.utils.printActions()`:

![](lux.utils.printactions.png)

## 4.) Stores & Synchronicity

### Actions, Stores & Remote Data I/O
I believe a classic pitfall to those rolling their own Flux implementations is putting remote data i/o in stores. In the first version of lux, I not only fell into this pit, I pulled out a golden shovel and dug even deeper. Our stores had the ability to make http calls - and as a result, the need for Action dispatch cycles to be asynchronous was unavoidable. This introduced a ripple of bad side effects:

* Retrieving data from a store was an asynchronous operation, so it wasn't possible to synchronously use a store's state in a controller view's `getInitialState` method.
* We found that requiring asynchronous reads of store state discouraged the use of "read only" helper methods on Stores.
* Putting i/o in stores led to actions being intiated by stores (e.g. - on XHR responses or WebSocket events). This was quickly undermining the gains from uni-directional data flow. Flux Stores publishing their own actions could lead to cascading updates - the very thing we wanted to avoid!

I think the temptation to fall into this pit has to do with the trend of client-side frameworks to-date. Client-side models are often treated as write-through-caches for server-side data. Complex server/client synchronization tools have sprung up, effectively encouraging a sort of two-way binding across the server/client divide. Yoda said it best: You must unlearn what you have learned.

About the time I was realizing I'd be better off making lux Stores synchronous, I read Reto Schläpfer's post ["Async requests with React.js and Flux, revisited."](http://www.code-experience.com/async-requests-with-react-js-and-flux-revisited/). He had experienced the same pain, and the same realization. Making lux Stores synchronous from the moment the Dispatcher begins handling an Action to the moment Stores emit change events made our app more deterministic and enabled our controller views to synchronously read Store state as they intialized. We finally felt like we'd found the droids we were looking for.

Let's take a look at one of the lux Stores in the flux-comparison example:

```
var CartStore = new lux.Store( {
    namespace: "cart",

    state: { products: { } },

    handlers: {
        addToCart: {
            waitFor: [ 'products' ],
            handler: function( product ) {
                var newState = this.getState();
                newState.products[ product.id ] = (
                    newState.products[ product.id ] ||
                    assign( products.getProduct( product.id ), { quantity: 0 } )
                );
                newState.products[ product.id ].quantity += 1;
                this.setState( newState );
            }
        },
        cartCheckout: function() {
            this.replaceState( { products: {} } );
        },
        successCheckout: function( products ) {
            // this can be used to redirect to success page, etc.
            console.log( 'YOU BOUGHT:' );
            if ( typeof console.table === "function" ) {
                console.table( products );
            } else {
                console.log( JSON.stringify( products, null, 2 ) );
            }
        }
    },

    getProduct: function( id ) {
        return this.getState().products[ id ];
    },

    getAddedProducts: function() {
        var state = this.getState();
        return Object.keys( state.products ).map( function( id ) {
            return state.products[ id ];
        } );
    },

    getTotal: function() {
        var total = 0;
        var products = this.getState().products;
        for (var id in products) {
            var product = products[ id ];
            total += product.price * product.quantity;
        }
        return total.toFixed( 2 );
    }
} );
```

A lux Store contains (at least) a `handlers` property and a `namespace`. The names of the keys on the `handlers` property match the Action type that they handle. In keeping with Flux principles, it's possible for lux Stores to wait on other stores before executing their handler. The stores you need to wait on can be specified on a per-action basis. The `addToCart` handler above is a good example. In the `waitFor` array, you specify the namespaces of any other store you need to wait on - this handler waits on the "products" store. The Dispatcher determines the order in which stores need to execute their handlers at runtime, so there's no need to worry about managing the order yourself in your Store logic. (Note that if you don't need to wait on any other stores, the handler value can be just the handler function itself rather than the object literal representation on `addToCart` above.)

You can also set initial state on the store, as we're doing above, and provide top-level methods that are used for reading data (the lux Store prototype provides the `getState()` method). Since Store handlers execute synchronously, you can safely read a Store's state from any component's getInitialState method, and you can be assured that no other Action will interrupt or mutate Store state while another Action is being handled.

lux Stores also provide `setState` and `replaceState` methods - but if you attempt to invoke them directly, an exception will be thrown. Those methods can only be invoked during a dispatch cycle - we put this rather heavy-handed opinion in place to reinforce the guideline that only stores mutate their own state, and that's done in a handler.

-----
## 5.) Plays Well With Others

Another key lesson for our team - lux needs to make it easy for non-React/lux instances to play well with together. To that end, lux provides mixins that can be used by something other than React components and Stores.

### Store Mixin
The `store` mixin enables you to listen for store change events. For example, this snippet shows an instance that's wired to listen to our ProductStore and CartStore:

```
var storeLogger = lux.mixin({
    stores: {
        listenTo: [ "products", "cart" ],
        onChange: function() {
            console.log( "STORE LOGGER", "Received new state" );
        },
    }
}, lux.mixin.store);
```

### Action Creator Mixin
The actionCreator mixin gives the instance a `publishAction( actionName, arg1, arg2...)` method. This method handles packaging the metadata about the Action into a message payload and then publishes it (if you've created a custom action creator that does more than just publish the action message, it will invoke that behavior):

```
// calling lux.actionCreator is a convenience wrapper around
// lux.mixin( target, lux.mixin.actionCreator );
var creator = lux.actionCreator( {
	doAThing: function() {
		this.publishAction( "doJazzHands", "hey, I can lux, too!", true, "story" );
	}
} );
```

### Action Listener Mixin
The actionListener mixin wires the instance into postal, so that it listens for any lux action messages. When a message arrives, it checks the `handlers` property for a matching handler and invokes it:

```
var listener = lux.actionListener({
	handlers: {
		doJazzHands: function(msg, someBool, lastArg) {
			console.log(msg, someBool, lastArg); // -> hey, I can lux, too! true story 
		}
	}
});
```

### Why Not Both?
It's not uncommon – especially if remote data API wrappers are involved – to need both the actionCreator and actionListener mixins. lux provides a convenience method for this, unsurprisingly named `actionCreatorListener`. In the flux-comparison example, the wrapper around the mock remote data API uses this:

```
// WebAPIUtils.js
var shop = require( '../../../common/api/shop' );
var lux = require( 'lux.js' );

module.exports = lux.actionCreatorListener( {
    handlers: {
        cartCheckout: function( products ) {
            shop.buyProducts( products, function() {
                this.publishAction( "successCheckout", products );
            }.bind( this ) );
        },
        getAllProducts: function() {
            shop.getProducts( function( products ) {
                this.publishAction( "receiveProducts", products );
            }.bind( this ) );
        },
    }
} );
```
The above module listens for the `cartCheckout` and `getAllProducts` actions. As it handles them, it uses the `publishAction` method (simulating how a server response would initiate a new Action).

So far, the mixins have covered every need we've had to make non-lux/non-React instances play well with lux. If those weren't enough, though, the underlying message contracts for Actions and Store update notifications are very simple, and could serve as an alternative. In fact, we plan to use those in some future Chrome dev tools extensions for lux.

## Wrapping Up
As I've looked through other Flux implementations, I've been encouraged to see that these principles are frequently present in them as well. The number of options available can feel overwhelming - but overall I find it an encouraging development. Solid & successful patterns like Flux will, by their very nature, encourage multiple implementations. If our experience is any indication, keeping these principles in mind can help guide you as you select, or write, the Flux implementation you need.
