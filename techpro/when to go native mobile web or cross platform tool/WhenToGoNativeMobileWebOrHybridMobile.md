#Why Mobile Should Be On Your Mind
Ever since Luke Wroblewkski coined the phase "mobile first" [in 2011](http://www.lukew.com/resources/mobile_first.asp), the zeitgeist of software development (at least the vast portions of it that touch mobile devices in some way) has been building towards a critical mass of not only "mobile first", but – as the CEO of Twitter [recently said](http://venturebeat.com/2013/05/29/twitter-ceo-were-not-competing-with-tv-and-news-media-were-complementary/#WYqDjtpkpw4gBlJp.99) – "all-in on mobile." 

I know I don't need to remind you of how quickly the world has changed. If you've been developing software for as little as 2 years, you've already witnessed unprecedented shifts in focus and innovation, much of it occurring in a language once considered an also-ran by many –  JavaScript. The odds are *very* high that you – the reader – have access to electricity at home and at work, and you most likely interact with the internet through multiple devices - desktop, notebook, tablet and/or mobile phone. It's easy, then, to not fully grasp the groundswell of change headed our way. That being the case, let's step outside of our normal techno-cultural environment for a moment to see the change through the eyes of East Africa (and of course, if you're reading this from Kenya, asante sana).

When I travelled to Africa in 1998, my luggage was stolen. I spent several hours in an airline office in Nairobi, Kenya - where it took thirty minutes *just to get a landline connection* to the airline's office in Uganda. It was busy. *Thirty minutes just to get a busy signal!* I travelled again to the same areas in 2003 and then 2005 – and I noticed something: the use of mobile phones had exploded. It was fascinating to observe the region skip the period of wired infrastructure (which we'd had for numerous decades where I live) and jump straight to wireless.

Why is this important? In a great piece by [Toby Shapshak](), he notes some interesting facts about Africa:

* More people have access to a mobile phone than electricity
* "Mobile money" - payment systems that allow you to transfer money to another phone user - is expected "to become a $617 billion industry by 2016".
* "80% of the *world's* mobile money transactions are happening in East Africa…" (with *half* of Kenya's GDP moving through mobile payment services!)
* In 2012, Google reported that 25% of its weekday searches in South Africa occur on mobile devices – and that figure jumps to 65% on the weekends

Perhaps the most bellweather-like point from Tony's article is that the Economist noted, in 2011, that "six of the 10 fastest growing economies in the last decade were in Africa".

"Simply put, Africa is not just a mobile-first continent", Tony concluded. "It is mobile-only."

> "Great - but I write line of business applications for my company in Europe. I'm not writing payment, radio or apps like [Farmerline](http://farmerline.org/). How is this relevant?"

If you don't want to be caught by surprise, or unprepared, it's relevant. The largest players in  the development world (and a myriads of smaller ones) are heavily investing in mobile innovation, with a particular common theme of open web standards throughout many of the advancements. Mozilla is working on the exciting FirefoxOS (an OS that will be capable of impressive features on even lower-quality phones). Microsoft debuted the ability to write Windows 8 desktop & tablet applications in HTML, JavaScript & CSS. Adobe purchased Nitobi - the company that created Cordova/PhoneGap (more on this in minute, don't worry) – and are actively developing it. I probably don't need to mention Apple and Google!

The mobile tsunami is already underway - the question is, what will you do when it reaches you? What are your options, when the time comes that your next project is a mobile application? What does this mean for your team? Will you need to hire new talent? Which mobile platforms should you focus on supporting?

#Understanding Your Options
> A little learning is a dangerous thing *–– Alexander Pope*

> Knowing is half the battle *–– GI Joe*

Would it surprise you to learn that there's more than one way to write an application for an iOS or Android device (not to mention other mobile platforms)? To create an application for a mobile device, you generally have three choices:

* **Write the app in the native platform/language for the device.** For iOS, this means writing it in Objective C using a Mac + XCode, etc. For Android, this means writing it in Java using Eclipse/Or-Insert-favorite-IDE + the Android SDK on Windows/Mac/Linux/BeOS (just kidding about BeOS). For Windows Phone 8, this means writing it in C#/XAML on a Windows 8 machine + Visual Studio 2012, etc.
* **Write a mobile web site.** Your application is a web app - hosted on the web and accessed by the device's browser.
* **Use a "Cross-Platform Tool (CPT)"** To quote VisionMobile, "CPTs can be used to develop native, hybrid and web apps and come in several technology flavours: JavaScript frameworks, App factories, Web-to-native wrappers, Runtimes and Source code translators." One of the most common approaches along these lines is the **hybrid mobile app.** In this case, your application is effectively a web app, but hosted inside a native-to-the-device application container. It's installed, launched and used like any native app, and can access device APIs (with some exceptions/hurdles) – but it's written in HTML, JavaScript and CSS. The most popular approach is to use [Apache Cordova]() - often referred to as PhoneGap, though that is the Adobe-branded re-distribution of Cordova. Both [Telerik]() and [Adobe]() have Cordova-based options for hybrid mobile applications, for example. Other popular CPT approaches exist as well, including Appcelerator's Titanium and Xamarin.

As you might already suspect, there are a host of trade-offs you will confront in choosing any one of the above options. These trade-offs are critical for the decision makers in your organization to understand. My goal in this article is to help jumpstart the discussion around these trade-offs, and help you begin to form the rails on which to guide and direct your energies as you assess which approach makes sense for you and your team. We can't possibly cover *every* aspect of how these options compare, nor can we anticipate the widely-varying needs of each team, application, company, etc. – all very important drivers in a decision. But I can tell you now that - no matter what the "native-only-or-you're-doing-it-wrong" or the "hybrid-is-just-as-good-as-mobile-all-the-time" pundits say – there is no *single right answer* in this debate.


#Comparing Trade-Offs
Let's start with a high-level view of pros and cons:

<table cellspacing="0">
	<tr>
 		<th>Approach</th>
 		<th>Pros</th>
 		<th>Cons</th>
	</tr>
	<tr>
 		<td style="font-weight: bold;">Native</td>
 		<td>
 			<ul>
 				<li>Full Access to Device/Platform/APIs</li>
 				<li>Best Performance (esp. with UI concerns)</li>
 				<li>App Store Discoverability</li>
 			</ul>
 		</td>
 		<td>
 			<ul>
 				<li>Different skills/languages/tools for each target platform</li>
 				<li>Tend to be most expensive-to-develop, with thin margin</li>
 				<li>Client code not re-usable between platforms (of course)</li>
 			</ul>
 		</td>
	</tr>
	<tr>
 		<td style="font-weight: bold;">Mobile Web</td>
 		<td>
 			<ul>
 				<li>Arguably the broadest reach</li>
 				<li>Can re-use existing, responsively designed sites</li>
 				<li>Code base is re-usable between platforms</li>
 				<li>Finding necessary skills isn't difficult</li>
 			</ul>
 		</td>
 		<td>
 			<ul>
 				<li>Extremely limited access to device APIs</li>
 				<li>Limited discoverability (no app store presence)</li>
 				<li>Tend to be more difficult to monetize</li>
 			</ul>
 		</td>
	</tr>
	<tr>
 		<td style="font-weight: bold;">Hybrid (CPT)</td>
 		<td>
 			<ul>
 				<li>Natively-installed & run, but built with JavaScript, HTML & CSS</li>
 				<li>Code base is re-usable between supported platforms</li>
 				<li>App Store Discoverability</li>
 				<li>Access to many device APIs (& extendable via plugins)</li>
 			</ul>
 		</td>
 		<td>
 			<ul>
 				<li>UI Performance affected by native webview implementation & (potentially) poorly written JavaScript/HTML</li>
 				<li>Differing webview implementations per platform</li>
 			</ul>
 		</td>
	</tr>
	<tr>
 		<td style="font-weight: bold;">Cross-Compiled (CPT)</td>
 		<td>
 			<ul>
 				<li>Can re-use existing skills if source language matches team skills</li>
 				<li>Code base is re-usable between supported platforms</li>
 				<li>Access to many (if not all) device APIs</li>
 				<li>App Store Discoverability</li>
 			</ul>
 		</td>
 		<td>
 			<ul>
 				<li>May not support all target platforms</li>
 				<li>Can be difficult to debug</li>
 			</ul>
 		</td>
	</tr>
</table>

#Why Not Just Go Native?
Let's be honest – as I oversimplify for a moment – if money isn't an issue and your company can afford to venture into multiple native platforms (i.e. - hiring talent experienced in each platform, etc.) *and* you need app-store discoverability, then native might be the ideal option for you. Developing native applications mean you will not be constrained by any "lowest common denominator" forced on you by a hybrid/cross-compiled approach. It also means that you won't be sacrificing performance *and* you'll have access to device-differentiating features that may not be readily accessible to hybrid containers. 

Then why do the other options exist? It turns out the developing native applications comes at a price. Literally.

##Native App Means Native Cost
In VisionMobile's [Developer Economics (2012)](http://www.visionmobile.com/product/developer-economics-2012/) report, they found that the average mobile app would take roughly three man-months to develop. The average cost, at the time, to develop an app for iOS (the ["best platform for generating revenue"](http://www.visionmobile.com/product/developer-economics-2013-the-tools-report/)) was $27k. They also found that only 54% of iOS developers managed to break even. 54%? That's a bit concerning, but it doesn't stop there.

In the [Developer Economics 2013](http://www.visionmobile.com/product/developer-economics-2013-the-tools-report/) report, Vision Mobile described what they called the "app poverty line" - if you made less than $500 per app, per month, you were below the app poverty line. Of the survey respondents from that report, 82% of them were writing mobile applications to make money. Of that 82%, 67% of them were *below the app poverty line*! They go on to say: 

>"In mobile development, loyalty to one platform is not something that pays off. Our research shows that the revenues are higher when using more platforms."

This is just a peek into the economic factors of writing mobile applications, but it already paints a clear picture: the margins are razor-thin, a large portion of companies don't break even, and your chances improve if you can target mutliple platforms. *That* is why the other options exist, and why their siren call – the reach of multiple platforms without the expense of multiple teams – appeals to many. The question of "when should I go native?" is often best answered with "When you can afford to support each target platform - including hiring developers, designers, QA & analysts as well as purchasing/maintaining any infrastructure required to build for the native target".


#When Native Isn't an Option
> How will I know when it's love? –– Van Halen

> Now what?! –– Bloat, Finding Nemo

If going native is off the table as an option, how do you know which alternative to choose? There's no silver bullet, but there are questions you can ask that will help determine your options:

* Do you need to have an app store presence? If yes, then mobile web is out. If "no" or "not necessarily", then mobile web is an option.
* Are you OK with no app store presence, you need the ability to frequently update the application and you're not nessarily concerned with heavy monetization of the app? (Believe it or not, this happens - particularly when an established company is expanding its reach to mobile to establish presence before other mobile initiatives.) If yes to all 3, then mobile web could be your answer (assuming you don't need heavy device API availability).
* Do you need to be able to access device APIs (accelerometer, camera, GPS, key chain, use push notifications, etc.)? Then mobile-web is out.

From the above questions, you can begin to see the logic behind answering "When should I go mobile web?". In many cases, this is best answered with: "When you don't need an app store presence, aren't really using device APIs, aren't *as* concerned about app monetization, and want to potentially take advantage of re-using a responsively-designed desktop site". Don't underestimate what's possible here. Both [time.com](http://time.com) and [forecast.io](http://forecast.io) are good examples of what's possible with this approach. (It's worth noting that some device-level APIs are available to mobile web applications - good examples being geolocation, media capture, contacts and others.)

#Cross Platform Tools - Democratizing Mobile Development
Since hybrid mobile applications utilize many of the exact same skills necessary to create a mobile web site, this can be an attractive option – especially given the likelihood that you may already have a web development team and can leverage those skills immediately. Developers that had previously been kept out of a platform due to the walled-garden nature of developing for it can now create applications that usually look and feel like native apps. However, it's not as simple as writing your web application and hosting it inside the native container app provided by, for example, Apache Cordova. You can (and probably will) run into road blocks - and it's important to be aware of where that might happen. VisionMobile listed over 100 CPTs in their [Cross Platform Tools 2012 report](http://www.visionmobile.com/product/cross-platform-developer-tools-2012/) - we obviously can't cover them all. But let's examine one of the most popular options.

##Apache Cordova/PhoneGap
Cordova-based options are among the most popular hybrid mobile approaches (you will often hear "PhoneGap" used interchangably with "Cordova" – see [this](http://www.icenium.com/blog/icenium-team-blog/2013/03/26/demystifying-cordova-and-phonegap) for more explanation). The native 'webview' of the device is used to run the application you create in HTML/JavaScript/CSS - effectively within a full-screen, chrome-less browser window. As a baseline, it's relatively safe these days to assume that most, if not all, of these APIs will be available to you:

<table>
    <tbody>
        <tr>
            <td>Accelerometer</td>
            <td>Camera</td>
            <td>Compass</td>
            <td>Contacts</td>
        </tr>
        <tr>
            <td>File</td>
            <td>Geolocation</td>
            <td>Media</td>
            <td>Network</td>
        </tr>
        <tr>
            <td>Notification - Alert</td>
            <td>Notification - Sound</td>
            <td>Notification -Vibration</td>
            <td>Storage</td>
        </tr>
    </tbody>
</table>

As far as devices, Apache Cordova/PhoneGap supports, to some degree or another, the following platforms:

<table>
    <tbody>
        <tr>
            <td>Android</td>
            <td>Bada</td>
            <td>Blackberry</td>
            <td>iOS</td>
        </tr>
        <tr>
            <td>Symbian</td>
            <td>Tizen</td>
            <td>webOS</td>
            <td>Windows Phone 7</td>
        </tr>
        <tr>
            <td>Windows Phone 8</td>
            <td>Windows 8</td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

Not bad for platform coverage. But what do you do when access to a native API doesn't exist? You can write a [Cordova Plugin](http://docs.cordova.io/en/edge/guide_plugin-development_index.md.html#Plugin%20Development%20Guide) to open up a native API to your JavaScript. This is both a strength and weakness of this type of hybrid approach. [Many Cordova plugins already exist](https://github.com/phonegap/phonegap-plugins) (with generous open source licenses), which is a positive. However, if you need the functionality on (for example) iOS, Android and Blackberry – this entails writing a version for each platform. So now we're back to needing other skills like Java and Objective C. The silver lining, though, is that the need for these skills is focused on the plugin only - and it's reasonable to outsource the creation of these plugins to experienced contractors if you don't have the skills in-house.	
If you decide to go the Cordova/PhoneGap route, three of the biggest challeneges you need to prepare for are:
###1.) UI Performance
It's not rocket science - running an app *inside* a container can have performance concerns. Cordova/PhoneGap has gotten a bad rap in some cases due to slow webview implementations (looking at you, Android 2.x), as well as popular (ahem) UI frameworks that excel at causing reflow due to how they manage the DOM. Desktop browsers often cover up inefficient DOM and JavaScript due to the sheer brute force available – you can't assume the same level of power will be available on mobile devices. This means that even your experienced web team needs to pay close attention to things that can help or harm performance. I recommend starting with [this post by Andrew Trice](http://www.tricedesigns.com/2013/03/11/performance-ux-considerations-for-successful-phonegap-apps/). The performance of webview implementations has improved to the point to where hybrid approaches work well for line-of-business style applications that don't need game-intensive UI performance.

###2.) Debugging
Not all mobile devices support remote debugging like iOS 6 (for shame). [Weinre](http://people.apache.org/~pmuellr/weinre/docs/latest/) is the next best option (currently) for remote debugging, but it can be a painful, buggy experience. You will find that running your hybrid app in a dekstop browser simulator (Icenium and Ripple both provide simulators like this) will help a great deal - given the maturity of debugging tools in browsers like Chrome. Of course, if you write Cordova plugins, you will need to become familiar with each platform/SDK's debugging toolsets as well.

###3.) Platform Requirements & Maturity
In 2012, Vision Mobile's [Cross Platform Developer Tools](http://www.visionmobile.com/product/cross-platform-developer-tools-2012/) report indicated that while PhoneGap had a high adoption rate, 27% were abandoning it  - likely due to frustrations with the development & debugging experience, coupled with lack of desired native APIs. (Appcelerator Titanium had a slightly higher abandonment rate of 33%.) As predicted, vendors have improved their tooling quite a bit in the last year, though it still has a long way to go. Both Adobe and Telerik are making headway in this area by providing cloud-based build tools alonside other differentiating features. Removing the burden of locally-installed SDKs, improving the debugging experience and reducing the pain of including custom Cordova plugins will likely go a long way towards retaining developers.

##There Can't Be Only One
I would be foolish to not at least mention two of the alternatives to Cordova: Appcelerator Titanium and Xamarin. Titanium provides a cross-platform JavaScript API (giving you some code re-use), while it also focuses on leveraging performant native UI widgets. Xamarin allows you to develop iOS, Android & Mac applications in **C#** - and even provides the "[Xamarin Component Store](http://components.xamarin.com/)" which includes UI controls, themes, services, graphs and more. Vision Mobile's [Cross Platform Developer Tools 2012](http://www.visionmobile.com/product/cross-platform-developer-tools-2012/) report indicated that both PhoneGap/Cordova and Titanium could substantially improve retention if they address debugging and development features, and that Xamarin appeared to be climbing in popularity as well.

So - this begs the question: **"When do I go with a cross-platform tool?"**

* Do you need to support multiple platforms but don't have the skills or budget in-house to go native?
* Is an app store presence a must-have?
* Do you have existing web skills (or C#, in the case of CPTs like Xamarin) that you want/need to leverage for your mobile application?
* Is maintaining one code base (and thus synchronizing releases across platforms) a goal?
 
Answering yes to the questions above (especially the first one) is a good indicator that using a CPT might be your best bet. As to the question of *which* CPT to use, it depends so heavily on details unique to your circumstance that it's not easily predictable. Obviously, a heavy .NET shop might favor Xamarin. Companies with available web talent may favor Cordova. The tools provided by vendors can drive things as well. If you target iOS and Android primarily and want to sidestep SDK requirements, easier testing & simulation and a simpler publishing-to-app-store experience, using [Icenium](http://www.icenium.com/) is a good option. If you have a custom build tool chain, need to support multiple platforms, want to use Cordova and graft it into your build setup, then [PhoneGap Build](https://build.phonegap.com/) might be an option. The different alternatives exist because the needs of different apps and companies can vary widely! 

##Where To Go From Here
In 2011, Marc Andreessen said that "software is eating the world". It's clear from current trends that a vast portion of the software to which he refers will be on mobile devices. If you, then – as a decision maker in an organization – want a better view of the landscape, I recommend reviewing the [research](http://www.visionmobile.com/products/research/) published by VisionMobile as a starting point. If you are evaluating alteratives currently, I recommend a small pilot project in 2 or 3 of your top choices to get in-the-trenches experience with them. Once you set a course, find and follow the top voices for that platform or CPT – and become a voice as well.
