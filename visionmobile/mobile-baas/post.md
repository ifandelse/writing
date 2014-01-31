#Mobile Backend-as-a-Service

Mobile apps — just like any other — need a place to store data. Fortunately, the industry's push towards outsourced infrastructure (i.e., — "cloud" services) has had a profound impact on both the affordability as well as the availability of cloud-based data storage, often referred to as [Backend-as-a-Service](http://en.wikipedia.org/wiki/Backend_as_a_service) (BaaS), or Mobile-Backend-as-a-Service (MBaaS).

##What is BaaS?
At its core, a BaaS provider typically enables you to store your data & interact with other critical services in normalized manner, without the headaches and cost of maintaining your own infrastructure. Cloud-based *storage* is just the beginning of a BaaS offering, though. Quite often, BaaS providers enable push notifications, integration with other social networks, user administration, custom business logic and more.

##Which BaaS is Right For Me?
To determine which BaaS can work for you, you need to start with *at least* asking these two questions:

* **What platforms do I need to support?** It's common to expect to see SDKs for most major platforms — and usually an HTTP service layer fallback which nearly any client could easily utilize.
* **What features do I need?**
	* Storage (should be a given)
	* Push notifications
	* Usage analytics
	* Social integration
	* User Administration
	* Custom Code Integration 
	
The second question above really breaks down into all kinds of sub-questions, and by no means covers all the possible features you can find in a BaaS. It's important to understand the needs of your app *before* selecting a BaaS offering. So - what feature-specific questions may be helpful to ask as you analyze your needs?

###Feature Analysis
####Storage
While every BaaS provider will offer some kind of storage, there are wide variety of differentiating factors. Is the storage schema-less? What's the search API like? Can you establish relationships between stored items? Can you query geospatial data (without 3rd party intervention)? Are changes pushed to connected clients, or do they need to re-query for updates? Does the BaaS support "data connectors" — allowing you to sync the cloud-based data to another system behind your own firewall? Where are the servers physically located (this can be a critical question to ask both for availability and regulatory reasons)? How difficult is it to export your data en masse should you decide to leave the provider for another option? What *kinds* of data can be stored — does it include files/binary data storage?

####Push Notifications
If you've used a mobile app, odds are you are familiar with push notifications. Whether it's a news app pushing breaking headlines, Twitter alerting you of direct messages or any number of possibilities — push notifications allow you to get important information to your users without the costs of other channels (SMS, for example) and without requiring your app to be in the foreground at the time. As most push notifications arise due to a change in data, it's logical that many BaaS providers are including this as an offering. Good questions to ask: What platforms are supported (iOS, Android, etc.)? Will I get a consistent/normalized API to work with, rather than having to work with each platform-specific push notification API? Will I get the advantage of automatic scaling during periods of high demand?

####Usage Analytics
Does the service enable you to track the number of API calls, storage quota or other feature usage? Since many BaaS pricing plans can vary based on usage, having a means to view analytics is a vital part of wisely (& economically) using the service. Depending on what metrics are tracked, usage analytics may also give you insight into *how* your app is being used. Does the service support custom "events" which you can provide to track either feature usage, performance data and/or campaign response(s)? Does the provider give you the ability to query the usage over time, filter the data and export reports?

####User Administration & Social Integration
Many BaaS options provide built-in user management features. Does the service allow you to create users? Does it focus on role-based user permissions, or allow granular claims-based permissions? At what level can you restrict information — schemas/tables, records, fields? Your target users may not care to create yet another login in order to use your app. Does the BaaS provide integration with 3rd party authentication with sites like Facebook, Twitter, Google and others? 

####Custom Code Integration
It's difficult to generalize to a common use-case, and still enable enough flexibility to users without giving them a means to inject custom logic for certain operations. Whether it's validation, injecting a custom step, priming a cache or other needs, a good question to ask is your provider allows you the necessary hooks to inject your own code. Not only that — but what language(s) are supported?

##Options?

Of course, knowing *which BaaS options exist* is important as well! Below I'll share a few observations of the BaaS's that I've had experience with to some degree, but this is by no means an exhaustive list. Check out a [larger list](http://www.developereconomics.com/sector/backend-as-a-service/) of the tools we're tracking in this space.

![](https://s3.amazonaws.com/toolatlas/logos/stackmob.png)
###StackMob
StackMob is one of the more well known names in this space. They directly support SDKs that cover iOS, Android, JavaScript & Java, and they expose an HTTP REST API for other clients as well. StackMob also benefits from an active community which has written 3rd party tools focusing on OAuth integration with other languages & platforms like PHP, ActionScript, Python and Sencha as well as an Appcelerator Titanium SDK.

StackMob has an impressive feature list. Their storage engine uses schemas which can be auto-generated for you based on your data, or you can use a dashboard they provide to define them. They support file storage if you have an Amazon S3 account (their services wrap the calls to Amazon). StackMob supports OAuth 2.0, as well as integration with Twitter and Facebook, and provides adequate user administration/permissions features (depending on your needs, you may need to elaborate if you need as granular as field-level permissions). You can upload custom Java, Scala and/or Clojure code and expose it via HTTP endpoints while having access to their data APIs from within your code. Another interesting feature is StackMob's [HTML hosting](https://developer.stackmob.com/module/html5), which cannot only host your app, but also integrate with [GitHub](http://github.com), enabling you to deploy your app based on what's committed to the git repository.
_____

![](https://s3.amazonaws.com/toolatlas/logos/parse.png)
###Parse
Parse was well known before it was acquired by Facebook in April of 2013, even more so after. It supports storing photos and geolocation data, 3rd party/social integration, push notifications and realtime usage analytics. The analytics are particularly impressive, in that they allow you to track custom events & types of devices, highly customized reports and more. Parse has an impressive array of client SDKs. Officially, Parse provides SDKs for iOS, Android, Windows, Windows Phone 8, Mac OS X, JavaScript and Unity (the cross-platform game engine). You also have the option of utilizing the HTTP REST API. Third party developers have created client libraries for .NET, ActionScript, Clojure, Java, Qt, Ruby, PHP, Python, WebOS and more!
_____

![](https://s3.amazonaws.com/toolatlas/logos/firebase.png)
###Firebase
Firebase is a recent — and interesting — option in this space, focusing on a specific kind of use-case as opposed to larger, more general offerings like StackMob: *realtime data synchronization*. Multiple clients accessing the same data can see realtime updates (if you're wondering about latency, it's impressively *low*. To users, it will be instantaneous). Firebase also has a very flexible security model — including the ability for you to incorporate your own auth tokens or those from third party services. SDKs are available for JavaScript (both web clients and node.js), Objective-C (iOS) and Java (Android) and an HTTP REST API also exists. Realtime BaaS's have interesting challenges in the areas of how they handle transactions and conflict resolution. Firebase is one of the first I've seen that provides the necessary hooks you need to inject conflict resolution logic *without* also dictating the client-side UI framework stack. In fact, Firebase provides officially supported integrations with AngularJS, Backbone.js and EmberJS (all client-side JavaScript frameworks). A number of third party libraries exist as well, extending Firebase client support to Python, PHP, Ruby, Clojure and Perl.
_____

![](telerik.png)
###Telerik Backend Services
Telerik Backend Services (formerly known as "Everlive") is another recent and feature-filled option. The backend storage is an object store, but you can create relationships between objects, upload files, store geospatial data (and query it comparatively to other locations for distance) and more. You can inject custom code — written in JavaScript – to be executed before or after items are created, read, updated or deleted. The user management features allow you to create users, assign roles, constrain permissions to resources and data, integrate with third parties like Facebook, Microsoft (LiveID) and Google or even your own corporate Active Directory. Telerik Backend Services support push notifications for iOS and Android, as well as email and SMS notifications. It's worth noting that the management dashboard provides very easy-to-use tools to define schemas, manage users, create/edit data and *test push notifications from within your browser*. You also get usage analytics for database size, file storage, bandwidth used, push notifications and emails sent and HTTP requests.

##So Much More Awaits
Those are only four examples — we're tracking at least *fifty* BaaS offerings, and it seems more options appear nearly every month. Are you using a BaaS provider now? We'd love to hear which one(s) and what features drove your decision in the comments below.