#A Thrown-to-the-Wolves-Hands-On Introduction to React

There are some great [React](http://facebook.github.io/react/) overview posts (which I will link to at the bottom of this post), so I wanted to try something a bit different. Having used other MV\* frameworks, I wanted to dive right into React and see what it took to get up and running, and how it felt *in the trenches* getting from ideas to DOM. So, up front, we're going to focus on turning an app concept into a React-based implementation – we'll chat more about the philosophy and opinion behind React at the end. All you need to know for the moment is that React is "a JavaScript library for building user interfaces".

> Note: Even though I already know a decent amount about how React works, I'm going document my __initial__ thought process and impressions of it when I first picked it up. Why? Because it's always fun learning new things, and my internal dialogue during those times might sound familiar to your own. These sidebar quotes are my internal dialogue. I'll try to edit out my own insanity and/or profanity. :-)

*NOTE: The code snippets that are in-line in this post have been trimmed of any Bootstrap-related fluff (yay for `div` and class explosion). I took my friend [TJ Van Toll](https://twitter.com/tjvantoll)'s suggestion to remove the bootstrap class names and some of the div nesting to make them easier to read. However, the embedded fiddle examples have all of that, so if you prefer to swim in div/class soup, feel free.*


##The Concept
Years ago I wrote budget tracking app, where each month had a fairly typical worksheet of income and expenses. Let's see what it would take to create a single (scaled-down) budget worksheet using React to manage our UI.

###A Budget Worksheet
A single `Worksheet` will apply to a defined date range (for example - May 2014) and contain a list of budget `Items`. Each budget `Item` will have a `description`, `budget` amount, `actual` amount and an expense `type` (e.g. - "income" or "expense"). The `Worksheet` will also have an aggregate total for `budgetExpenses`, `budgetIncome`, `budgetRemainder`, `actualExpenses`, `actualIncome` and `actualRemainder`. A simplified JSON representation of a worksheet might look something like this:

```
{
	"period" : "May 2014"
	"items"  : [
		{ 
			"description" : "Rent",
			"type"        : "expense",
			"budget"      : 1600,
			"actual"      : 1600
		},
		{
			"description" : "Salary",
			"type"        : "income",
			"budget"      : 5300,
			"actual"      : 4875
		},
		{ 
			"description" : "Groceries",
			"type"        : "expense",
			"budget"      : 800,
			"actual"      : 650
		}
	],
	"budgetExpenses"  : 2400,
	"budgetIncome"    : 5300,
	"budgetRemainder" : 2900,
	"actualExpenses"  : 2250,
	"actualIncome"    : 4875,
	"actualRemainder" : 2625
}
```

> Me, to myself: This is *not* a complicated data structure - much simpler than the real thing. I can already picture how this would be done in KnockoutJS or Backbone. \*cracks knuckles\*

##Getting a Raw Page Set Up
I'm using Sublime Text 3, so I've thrown together a simple `index.html` that, for the moment, looks like this:

```
<!DOCTYPE html>
<html lang="en">
<head>
	<title>React Budget Worksheet</title>	
	<link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css">
	<link rel="stylesheet" href="main.css">
</head>
<body>
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.0/jquery.min.js"></script>
    <script src="//netdna.bootstrapcdn.com/bootstrap/3.1.1/js/bootstrap.min.js"></script>
	<script src="http://fb.me/react-0.10.0.min.js"></script>
	<script src="http://fb.me/JSXTransformer-0.10.0.js"></script>
	<script src="//cdnjs.cloudflare.com/ajax/libs/accounting.js/0.3.2/accounting.min.js"></script>
	<script src="main.js" type="text/jsx"></script>
</body>
</html>
```
What's with the `JSXTransformer-0.10.0.js` file? React's documentation shows an XML-based-yet-HTML-like syntax for templates called JSX, or an imperative approach to creating DOM elements. My internal dialogue sums up how I feel about this:

> While, on one hand, I can imagine some of the benefits of imperatively creating elements, I'd rather eat brussel sprouts every meal for a year (\**chokes back vomit*\*) than abandon markup entirely. I'm wary of the fact that JSX is actually an XML syntax, but willing to give it a go.

You might have also noticed that the `main.js` file is coming in with a type of `text/jsx`. This will keep it from being evaluated as JavaScript, so that React can parse it for JSX and 'compile' it to JavaScript. To opt into JSX parsing, I've included the `/** @jsx React.DOM */` pragma at the top of my `main.js` file.

 Now - what do I need to put in that `main.js` file to get things rolling?


##Rendering a Single Line Item
First, let's start with what it takes to render a single budget item. I know that I want to render the description, budget and actual amounts on a single line. 
If I had a static template (i.e. - no data to bind), I know I could do something along these lines after looking at React's first few getting started examples:

```
/** @jsx React.DOM */
var Item = React.createClass({
    render: function() {
        return <div className="budget-item">
                  <span className="desc">Rent</span>
                  <input type="text" placeholder="budget" value="1600" />
                  <input type="text" placeholder="actual" value="1600" />
               </div>;
    }
});

React.renderComponent( <Item /> , document.body);
```

The [result](http://jsfiddle.net/ifandelse/KX4XE):

<iframe width="100%" height="300" src="http://jsfiddle.net/ifandelse/KX4XE/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>


> Apparently, I've just created a simple React component. See how I'm passing `<Item />` as the first arg to `renderComponent`? This actually looks promising, since I can already envision the compositional advantages this can bring to bear when I start introducing parent-child relationships down the road.

That's fine - but I need to be able to pass *data* to my component. It appears that I can pass initial state into my component as attribute/value pairs - but I need to make a change to my Item's JSX markup first:

```
/** @jsx React.DOM */
var Item = React.createClass({
    render: function() {
        return <div className="budget-item">
                  <span className="desc">{ this.props.description }</span>
                  <input type="text" placeholder="budget" value={ this.props.budget } />
                  <input type="text" placeholder="actual" value={ this.props.actual } />
               </div>;
    }
});
```
I've replaced the hard-coded values with JavaScript expressions wrapped in curly braces. These expressions will be evaluated for their actual values at runtime.

> For what it's worth, I'm using "props" for these values because I don't expect them to change once the view has been rendered (`props` are intended to be immutable). Later on I'll have need of mutable data, and will store that as `state` instead. I'm also a bit surprised that I don't actually *hate* having JSX inline with my code. Everything I thought I knew is wrong...

So - to pass data in & render our Item component, we can now do this:

```
var rent = {
  description : "Rent",
  type        : "expense",
  budget      : 1600,
  actual      : 1600
}

React.renderComponent(
    <Item description={rent.description} budget={rent.budget} actual={rent.actual} />,
    document.body
);
```

In the `renderComponent` call I'm passing the `description`, `budget` and `actual` amount values in as attribute/value pairs. Remember, though, this is JSX so my values need to be wrapped in curly braces since they are, in this example, JavaScript expressions that need to be evaluated. These attribute values will be stored under the component's `props` member.

The [result](http://jsfiddle.net/ifandelse/j6fCC):
<iframe width="100%" height="300" src="http://jsfiddle.net/ifandelse/j6fCC/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

> I can already see what I need to do to iterate over a worksheet's list of items and render an Item component for each one. The worksheet component shouldn't be that hard.... \**crosses fingers*\*

##Creating a Worksheet Component
First, let's get our Worksheet rendering everything *except* our budget `Items` *and* let's also create a real worksheet state object we can pass into the `Worksheet` component:

```
var may2014 = {
    period : "May 2014",
    items  : [
        { 
            description : "Rent",
            type        : "expense",
            budget      : 1600,
            actual      : 1600
        },
        {
            description : "Salary",
            type        : "income",
            budget      : 5300,
            actual      : 4875
        },
        { 
            description : "Groceries",
            type        : "expense",
            budget      : 800,
            actual      : 650
        }
    ]
};

var Worksheet = React.createClass({
        getInitialState: function(){
            return {
                period : "",
                items  : [],
                budgetExpense  : function() {
                    return this.items.reduce(function(accum, val){
                      return val.type === "expense" ? accum + val.budget : accum;
                    }, 0);
                },
                budgetIncome    : function() {
                    return this.items.reduce(function(accum, val){
                      return val.type === "income" ? accum + val.budget : accum;
                    }, 0);
                },
                budgetRemainder : function() {
                    return this.budgetIncome() - this.budgetExpense()
                },
                actualExpense  : function() {
                    return this.items.reduce(function(accum, val){
                      return val.type === "expense" ? accum + val.actual : accum;
                    }, 0);
                },
                actualIncome    : function() {
                    return this.items.reduce(function(accum, val){
                      return val.type === "income" ? accum + val.actual : accum;
                    }, 0);
                },
                actualRemainder : function() {
                    return this.actualIncome() - this.actualExpense()
                }
            };
        },
        render: function() {
            return  <div>
                        <form>
	                        <div>
                                <h1>{ this.state.period }</h1>
                            </div>
	                        <div>
	                            <div className="items">
		                            <em>(No Budget Items Yet)</em>
		                        </div>
		                        <div>
		                            <div className="summary budget">
	                                    <div className="summary-header">
	                                        <h3>Budget</h3>
	                                    </div>
	                                    <div>
	                                        <div>
	                                            <div>Income:</div>
	                                            <div>{ accounting.formatMoney(this.state.budgetIncome()) }</div>
	                                        </div>
	                                        <div>
	                                            <div>Expense:</div>
	                                            <div>{ accounting.formatMoney(this.state.budgetExpense()) }</div>
	                                        </div>
	                                        <div>
	                                            <div>Remainder:</div>
	                                            <div>{ accounting.formatMoney(this.state.budgetRemainder()) }</div>
	                                        </div>
	                                    </div>
	                                </div>
		                            <div className="summary actual">
		                                <div className="summary-header">
	                                        <h3>Actual</h3>
	                                    </div>	
	                                    <div>
	                                        <div>
	                                            <div>Income:</div>
	                                            <div>{ accounting.formatMoney(this.state.actualIncome()) }</div>
	                                        </div>
	                                        <div>
	                                            <div>Expense:</div>
	                                            <div>{ accounting.formatMoney(this.state.actualExpense()) }</div>
	                                        </div>
	                                        <div>
	                                            <div>Remainder:</div>
	                                            <div>{ accounting.formatMoney(this.state.actualRemainder()) }</div>
	                                        </div>
	                                    </div>		                            </div>
		                        </div>
	                        </div>
                        </form>
                    </div>;
        }
    });
    
var worksheet = <Worksheet />;

React.renderComponent(
    worksheet,
    document.body
);

worksheet.setState(may2014);

```

You'll notice I'm using the `state` property of the component this time - instead of `props`. Why? The worksheet's data needs to be mutable - otherwise, editing a budget would be a bit difficult. By using `state`, I can use a component's `setState` method to update data (or add new budget items), and React will cause the component to re-render after the change. React actually renders components to an 'intermediate' DOM (effectively an in-memory JavaScript instance), which is then used to compare to the real DOM so that only the changed elements get re-rendered. I &lt;3 that behavior. I've also implemented the `getInitialState` method - not only setting initial state, but some "computed state" methods allowing me to get aggregate values for the worksheet as well.

This should land us reasonably close to [this](http://jsfiddle.net/ifandelse/VCc5e):

<iframe width="100%" height="300" src="http://jsfiddle.net/ifandelse/VCc5e/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

> I have a feeling component approach is probably going to pay off well.

## Rendering the Child Collection of Budget Items

Since I'm using Bootstrap's fluid grid system, I'll make a few tweaks to our item template's markup first:

```
/** @jsx React.DOM */
var Item = React.createClass({
    render: function() {
        return  <div className="budget-item">
                    <div className="desc"> 
                    	{ this.props.description }
                    </div>
                    <div>
                        <input type="text" value={ this.props.budget } />
                    </div>
                    <div>
                        <input type="text" value={ this.props.actual } />
                    </div>
                </div>;
    }
});
```

Next I just need to swap out this div from our `Worksheet`'s `render` method:


```
 <div className="items">
 	<em>(No Budget Items Yet)</em>
 </div>
```

With this:

```
 <div className="items">
	 <div className="row budget-item">
	     <div className="header">Description</div>
	     <div className="header amount ">Budget</div>
	     <div className="header amount">Actual</div>
	 </div>
	 {
	     this.state.items.map(function(i) {
	         return <Item description={i.description} budget={i.budget} actual={i.actual} />;
	     })
	 }
</div>
```

The relevant bit above is the expression wrapped in curly braces. We're calling `map` on our `items` array, and for each budget item, we're returning an `Item` component instance, passing in the initial state as attribute values (which become "immutable" `props` on the component). These `Item` component instances will become children of the `div` that contains them.

> I love the easy separation between `Worksheet` and `Item` - components feel great. However, that `Worksheet` component seems a bit bloated. What if I could better separate and organize it?

Now that we're rendering `Item` components, our worksheet is [taking shape](http://jsfiddle.net/ifandelse/qj3PX):

<iframe width="100%" height="300" src="http://jsfiddle.net/ifandelse/qj3PX/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>


##Extracting More Components
The two "summary" tables ("Budget" and "Actual"), are ideal candidates to be their own components. In fact - they could both utilize the *same* component. Let's create a summary component:

```
var Summary = React.createClass({
    render: function() {
        return <div className={ "summary " + this.props.type }>
            <div>
                <div className="summary-header">
                    <h3>{ this.props.type.charAt(0).toUpperCase() + this.props.type.slice(1) } </h3>
                </div>
                <div>
                    <div>
                        <div>Income:</div>
                        <div className="amount">{ accounting.formatMoney(this.props.income) }</div>
                    </div>
                    <div>
                        <div>Expense:</div>
                        <div className="amount">{ accounting.formatMoney(this.props.expense) }</div>
                    </div>
                    <div className="remainder">
                        <div>Remainder:</div>
                        <div className="amount">{ accounting.formatMoney(this.props.remainder) }</div>
                    </div>
                </div>
            </div>
        </div>
    }
});
```

> I love deleting code. The only thing better is deleting code while eating cheesecake. Or cannolis.

Now that we have a `Summary` component, we can rip out the manual creation of the summary tables in the `Worksheet`, leaving us with a `render` method that looks like this:

```
var Worksheet = React.createClass({
    // just showing the changes to render.
    render: function() {
		return  <div>
		            <form>
		            	<div className="worksheet-title">
		                    <h1>{ this.state.period }</h1>
		                </div>
		            <div>
		                <div className="items">
		                    <div className="budget-item">
		                        <div className="header">Description</div>
		                        <div className="header amount ">Budget</div>
		                        <div className="header amount">Actual</div>
		                    </div>
		                    {
		                        this.state.items.map(function(i) {
		                            return <Item description={i.description} budget={i.budget} actual={i.actual} />;
		                        })
		                    }
		                </div>
		                <div>
		                    <Summary type="budget"
		                             income={ this.state.budgetIncome() }
		                             expense={ this.state.budgetExpense() }
		                             remainder={ this.state.budgetRemainder() } />
		                    <Summary type="actual"
		                             income={ this.state.actualIncome() }
		                             expense={ this.state.actualExpense() }
		                             remainder={ this.state.actualRemainder() } />
		                </div>
		            </div>
		            </form>
		        </div>;
}});
```

> Now that's better. I'm resisting the urge to further 'componentize' `Worksheet` for now. I really want to be able to add new budget `Item`s, and I'm thinking that could be its own component as well.

##A Component for Adding Items
Creating a component dedicated to allowing you to add a budget item to a worksheet isn't hard at all. You may find the tricky part is deciding how to communicate from "child" components up to the parent. To quote [Pavan Podila](http://code.tutsplus.com/tutorials/intro-to-the-react-framework--net-35660), "*Within a component-tree, data should always flow down.*" This is super easy with React (as we've seen in the `Worksheet` setting child `Item` components' `props` values). But if a child component needs to communicate back up to the parent, what's the best option? In my own opinion & experience, this is where some sort of de-coupled messaging (or eventing) approach fits well. In my case, I'm going to use [postal.js](https://github.com/postaljs/postal.js) to publish a message from the `ItemAdd` component, and our `Worksheet` component will need to be updated to subscribe to the message and alter the component's state when an item is added. First, the `ItemAdd` component:

```
var ItemAdd = React.createClass({
        addItem: function(e) {
            e.preventDefault();
            postal.publish({
                channel: "worksheet",
                topic: "item.add",
                data: {
                    description : $("#item_desc").val(),
                    type        : $("#item_type").val(),
                    budget      : Number.parseFloat($("#item_budget").val()),
                    actual      : Number.parseFloat($("#item_actual").val())
                }
            });
        },

        render: function() {
            return  <div className="budget-item-add">
                        <div className="budget-item">
                            <div className="desc">
                                <input type="text" id="item_desc" placeholder="description" />
                            </div>
                            <div>
                                <input type="text" id="item_budget" placeholder="budget"/>
                            </div>
                            <div>
                                <input type="text" id="item_actual" placeholder="actual"/>
                            </div>
                        </div>
                        <div className="budget-item ctrls">
                            <div className="desc">
                                <select id="item_type">
                                    <option value="expense">Expense</option>
                                    <option value="income">Income</option>
                                </select>
                            </div>
                            <div>
                                <button name="addItem" onClick={this.addItem}>Add</button>
                            </div>
                        </div>
                    </div>;
        }
    });
```

In our `render` method, you'll notice we've provided an `onClick` attribute, and set the value to the component's `addItem` method. This works because React supports you providing an event handler as a camel cased property. In our `onClick` handler – the `addItem` method – we're publishing a message containing the form data the user filled out. Our `Worksheet` component will receive this message and add the item. In the `Worksheet` component, I implemented the `componentWillMount` method (which is fired as the component is "mounted" – more on that in a bit), and I'm wiring our subscription inside it:

```
var Worksheet = React.createClass({
    // other methods, etc.
    // only showing relevant bits here...
	componentWillMount: function() {
        postal.subscribe({
            channel: "worksheet",
            topic : "item.add",
            callback: function(d, e) {
                this.addNewItem(d);
            }
        }).withContext(this);
    },
    addNewItem: function(item) {
        var items = this.state.items.concat([item]);
        this.setState({ items: items });
    },    
```

You can see that if our `Worksheet` component receives an `item.add` message, it will invoke `addNewItem`, which will trigger a re-render of the component, since the state will have changed.

> I've seen other React examples where parent controls pass method references into child controls in order to facilitate child-to-parent communication. While that's certainly do-able, it's not my preferred approach in any framework.

At this point, our worksheet looks something like this:

<iframe width="100%" height="300" src="http://jsfiddle.net/ifandelse/bBfLM/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

##Plot Twist
As I was getting up to speed on React, one thing threw me for a loop: *[controlled components](http://facebook.github.io/react/docs/forms.html#controlled-components)*. In our worksheet, the `ItemAdd` component works just fine to add items to the budget. However, if we attempted to change the value in any of our rendered `Item` components' text inputs, it wouldn't let us. According to the React docs: "*An &lt;input&gt; with value set is a controlled component. In a controlled &lt;input&gt;, the value of the rendered element will always reflect the value prop.*" This is why our `ItemAdd` component works (none of its text inputs were rendered with a value assigned), and our `Item` components do not (each was rendered with a `budget` or `actual` value). To get around this, we can utilize an `onChange` event in our `Item` component:

```
var Item = React.createClass({
        onChange : function(e) {
            var $el = $(e.currentTarget);
            var type = $el.parent().hasClass("actual") ? "actual" : "budget";
            postal.publish({
                channel: "worksheet",
                topic: "item.change",
                data : {
                    index: this.props.index,
                    type : type,
                    val  : Number.parseFloat($el.val())
                }
            });
        },
        render: function() {
            return  <div className="budget-item">
                        <div className="desc"> { this.props.description }</div>
                        <div className="budget">
                            <input type="text" value={ this.props.budget } onChange={ this.onChange }/>
                        </div>
                        <div className="actual">
                            <input type="text" value={ this.props.actual } onChange={ this.onChange } />
                        </div>
                    </div>;
        }
    });
```

In this case I've chosen, once again, to use messaging to communicate the changes back to the `Worksheet` component. While this might seem foreign to you if you hail from other frameworks, I have to say that having my child components be as immutable as possible (i.e. - they're fed `props`) and eventing any changes up to the parent that actually *owns* the state has been very helpful in isolating complexity and avoiding any sort of parent-child state synchronization issues.

I also updated the `Worksheet` component to listen for the `item.change` message published above:

```
var Worksheet = React.createClass({
        // other methods, etc.
		// only showing the relevant bits
        componentWillMount: function() {
            postal.subscribe({
                channel: "worksheet",
                topic : "item.add",
                callback: function(d, e) {
                    this.addNewItem(d);
                }
            }).withContext(this);
            postal.subscribe({
                channel: "worksheet",
                topic: "item.change",
                callback: function(d, e) {
                    this.updateItem(d.index, d.type, d.val);
                }
            }).withContext(this);
        },
        updateItem: function(index, type, val) {
            var items = this.state.items.slice(0);
            items[index][type] = val;
            this.setState({ items: items });
        },
        /* render was also updated to pass the index of the budget item to the
           Item component: 
           {
               this.state.items.map(function(i, idx) {
                   return <Item index={idx} description={i.description} budget={i.budget} actual={i.actual} />;
               })
           }
        */
});
```

The [result](http://jsfiddle.net/ifandelse/62EN7): 
<iframe width="100%" height="300" src="http://jsfiddle.net/ifandelse/62EN7/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

##Taking Stock
While this code certainly isn't the prettiest code I've ever written, in a short time we've managed to dive into the following:

* how to create multiple React components
* grasp the beginnings of how to use `props` and `state`
* how to pass data from parent to child components
* how to listen to DOM events and how to communicate from a child component up to the parent. 

There's a lot that can be done just to clean up the current code, not to mention adding real data retrieval & persistence, validation, supporting multiple worksheets, pre-compiling our JSX and much *much* more. Nevertheless, this is a good point to stop and talk more about React from a higher level.


##Why React?
Normally you might expect this section to be at the beginning of a post! Now that you've gotten to see a bit of React in action, though, some of this might make more sense. React is *not* intending to be a full stack MV\* framework like Ember or AngularJS. To quote their docs: "Many people choose to think of React as the V in MVC." While I'd argue that it's a bit more than just the "V" (given the facilities for `props` and `state`), how React handles UI certainly differentiates it from other popular frameworks. As I mentioned earlier, React constructs an intermediate DOM, to which components initially render. This intermediate DOM is then compared to the real DOM, and React calculates the minimum set of DOM mutations that are needed to update the real DOM. Using this approach, React is capable of very fast DOM manipulations. In fact - Pete Hunt mentioned recently on JavaScript Jabber that React is capable of 60 fps on *a mobile device*.

Overall - I find the focus on components, the simplicity of composing the UI with those components, the intelligent and highly optimized approach to rendering and UI lifecycle as well as the "plays-well-with-other-popular-libraries" to be compelling factors in React's favor. I was *highly* skeptical of JSX, but found that I truly enjoyed it after getting into the groove.

Comparing React to other frameworks (which is inevitable) should not be done without comparing the specific feature overlap – since React doesn't provide opinions on transports (http, websockets, etc.), routing, etc. If you prefer a one-stop-shop approach, then odds are you will prefer Angular, Ember or something comparable. However, if you are comfortable assembling your own 'stack', I'd wager that you'll find React a welcome part of your tool set.

##Where to Go From Here
Stay tuned for future posts that will continue to evolve this rough implementation - by the end of which you should get a clearer picture of what it's *really* like to use React. In the meantime, I highly recommend the following links:

* [Intro to the React Framework](http://code.tutsplus.com/tutorials/intro-to-the-react-framework--net-35660) by Pavan Podila
* [React.js in pure JavaScript](http://webdesignporto.com/react-js-in-pure-javascript-facebook-library/)
* [React Docs - Getting Started](http://facebook.github.io/react/docs/getting-started.html)
* [A Look at Facebook's React](http://dailyjs.com/2013/08/15/react/)





