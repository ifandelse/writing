Over the last decade, application testing has continually proved itself to be an important concern. When done well, testing can drastically reduce the number of bugs that make it into your release code (and thus actually affect your users). In addition, good testing approaches will help your team catch bugs *earlier* in the development lifecycle – resulting in a savings of both time and money (not to mention reputation with your users). Code that has good test coverage enables you and your team to make changes and introduce new features to your app without the fear of it breaking existing functionality.

The word "Testing" is a large umbrella, and is usually better understood when you break it down to specific *types* of testing. For example:

* Unit Testing - automated tests written by developers, with each test targeting a narrow slice of application behavior.
* Functional & Acceptance Testing - typically performed by QA personnel or automated UI testing frameworks.
* Performance Testing - often performed manually with profiling tools (for example - heap and CPU profiling tools), though many mobile app developers are moving towards integrating app analytics to gather this data from real usage as well.

That's certainly not an exhaustive list of the types of testing, but enough to make an important point: mobile applications face several challenges when it comes to testing. Key among those challenges are:

* Platform & Device Diversity
* Immature Tooling
* Lack of Awareness

If you opt to write native applications for each target platform, then any code-level testing (i.e. - Unit Tests) will not be transportable as you move from Objective-C (iOS) to Java (Android). In addition, any scripted UI-Automation testing tools may not work for multiple platforms (or at the very least require separate scripts for each platform). Hybrid solutions like PhoneGap, or cross-compiled solutions like Xamarin can offer a single approach to unit testing (given a single codebase for multiple platforms) - but do not always offer the same level of quality as native tooling when it comes to performance profiling. Despite the trade offs involved, I've found in my own experience that the biggest barrier to entry in mobile app testing is often a *lack of awareness of what tools are available*. *That* is the barrier which I hope to address in this post. 

##Unit Testing
Unit testing for specific platforms or cross-platform tools is not difficult, and your options abound. Let's look at a sample of some of these choices.

### iOS
iOS developers who've been writing Objective-C for a while may be familiar with [OCUnit](http://www.sente.ch/software/ocunit/), which shipped with XCode prior to the XCTest framework. It's still supported in XCode 5, but the understanding is that new and future projects should focus on using XCTest.

Don't let Apple's [sparse documentation](https://developer.apple.com/library/ios/documentation/ToolsLanguages/Conceptual/Xcode_Overview/UnitTestYourApp/UnitTestYourApp.html#//apple_ref/doc/uid/TP40010215-CH21-SW1) on unit testing deter you from checking out the XCTest framework. If you're running an OS X Server, you can also take advantage of the [XCode service's continuous integration features](https://developer.apple.com/library/ios/documentation/ToolsLanguages/Conceptual/Xcode_Overview/UnitTestYourApp/UnitTestYourApp.html#//apple_ref/doc/uid/TP40010215-CH21-SW3). As part of a continuous integration workflow, you can create "bots", which can continually build and test your app.

Many developers prefer a Behavior-Driven-Development (BDD) style syntax for unit testing. If this describes you, be sure to check out [Kiwi](https://github.com/allending/Kiwi) - a BDD style unit testing framework for iOS.

One other important mention is [OCMock](http://ocmock.org/ios/) a mocking framework for iOS. Mocks are an indispensable part of writing adequate tests around your application's behavior!

###Android
[JUnit](http://junit.org/) is perhaps by far the most well known (and officially recommended by Google) testing framework for Android. The JUnit Android extensions allow you to mock Android components, but I've also seen quite a number of Android developers use JUnit with [Mockito](https://code.google.com/p/mockito/), another Android mocking framework.

[Robolectric](http://robolectric.org/) takes a different - and very interesting - approach by allowing you to run your Android unit tests in the normal JVM (Java Virtual Machine), without the need for an emulator. This enables your tests to not only run from within your IDE, but also as part of a continuous integration workflow.

###Qt
Qt made the top 5 most used CPTs in 2013. If you're building mobile applications with Qt, you'll be happy to know about [QTestLib](http://qt-project.org/doc/qt-4.8/qtestlib-manual.html), a unit testing framework built by Nokia. Based on my research, it appears that QTestLib can be integrated with a 3rd party continuous integration workflow - enabling very helpful testing automation.

###PhoneGap/Apache Cordova
Web-based hybrid approaches to mobile apps can take advantage of a host of testing and mocking frameworks, not to mention scripted UI/acceptance testing tools as well (more on that in a moment). When it comes to unit testing JavaScript, three of the biggest names are [QUnit](http://qunitjs.com/), [Mocha](http://visionmedia.github.io/mocha/) and [Jasmine](http://pivotal.github.io/jasmine/). I've personally used all three, with my favorite setup including Mocha and [expect.js](https://github.com/LearnBoost/expect.js/) (which provides a BDD style test syntax). Mocking and "spy" frameworks abound in JavaScript as well, with [Sinon.js](http://sinonjs.org/) and [JsMockito](http://jsmockito.org/) among the more popular stand-alone mocking options.

Many PhoneGap developers take advantage of tools like [PhantomJS](http://phantomjs.org/) - which is a "headless" (no UI) WebKit browser, with a JavaScript API. PhantomJS can be easily integrated into a continuous integration workflow to automatically run unit tests against your hybrid mobile application's codebase.

###Xamarin
Xamarin uses a customized version of [NUnit](http://nunit.org/) (ported from JUnit), called [NUnitLite](http://www.nunitlite.com/) which enables you to write unit tests against your Xamarin iOS & Android projects. For any shared codebase, you can use the unit testing framework of your choice.

##Scripted UI Testing
Not every team can afford to hire an army of manual QA testers, despite how valuable that can be. Automated tooling can bridge the gap.

If you're writing native iOS and Android apps, you're in luck. Apple provides an ["Automation instrument"](https://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/UsingtheAutomationInstrument/UsingtheAutomationInstrument.html) that will automate UI tests against your iOS mobile application. The Android SDK provides the ["uiautomator" library](http://developer.android.com/tools/testing/testing_ui.html), described as "*A Java library containing APIs to create customized functional UI tests, and an execution engine to automate and run the tests.*" In addition to these, you can use third party tools like [Squish](http://www.froglogic.com/squish/gui-testing/index.php), [Ranorex](http://www.ranorex.com/) and [Perfecto Mobile's MobileCloud Automation](http://www.perfectomobile.com/Products/MobileCloud_Automation) to automate UI tests against Android and iOS apps, web apps and more. It's worth mentioning that Perfecto Mobile's [MobileCloud Automation](http://www.perfectomobile.com/Products/MobileCloud_Automation) exposes an API to better facilitate integration with existing build/continuous integration tools. Perfecto Mobile also offers [MobileCloud Interactive](http://www.perfectomobile.com/Products/MobileCloud_Interactive), which enables you to "*perform remote manual testing on real smartphones and tablets regardless of where you are*" – who wouldn't want to have a "testing army" of *real* mobile device users at their disposal?

Among the more interesting developments in mobile UI automated testing is the emergence of an open source project named [Appium](http://appium.io/). Appium uses the [WebDriver](https://code.google.com/p/selenium/wiki/JsonWireProtocol) JsonWireProtocol to interact with iOS, Android and Firefox OS apps and gives you the choice of writing your UI tests in *any WebDriver-compatible language* (Java, Objective-C, JavaScript, PHP, Python, Ruby, C#, Clojure, Perl and others).

##Performance & Profiling
Apple's [Instruments](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/Introduction/Introduction.html) is one of the more impressive native toolsets I've seen recently. With Instruments, it's possible to profile how your app executes, run stress tests, record and replay user actions, create custom instruments and *a lot* more. If you're writing native iOS apps & not using Instruments, I recommend reading through the [Quick Start](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/InstrumentsQuickStart/InstrumentsQuickStart.html#//apple_ref/doc/uid/TP40004652-CH19-SW1) to get up to speed.

With Android apps, you have several (albeit, lower-level) tools available: [Systrace](http://developer.android.com/tools/debugging/systrace.html) & [Traceview](http://developer.android.com/tools/debugging/debugging-tracing.html). You can also use the Device Monitor to view memory usage based on [logcat](http://developer.android.com/tools/help/logcat.html) messages.

For hybrid mobile apps, you have a host of mature desktop browser tools ([Chrome Developer Tools](https://developers.google.com/chrome-developer-tools/), [Firefox](https://developer.mozilla.org/en-US/docs/Tools)/[Firebug](http://getfirebug.com/), etc.), which you can bring to bear on your app to profile CPU usage, memory, DOM manipulation and much more.

Many mobile developers have started taking advantage of third party analytics services such as [Google Mobile Analytics](http://www.google.com/analytics/mobile/), [Countly](https://count.ly/), [EQATEC](http://www.telerik.com/analytics), [Flurry](http://www.flurry.com/flurry-analytics.html), [Perfecto Mobile's MobileCloud Monitoring](http://www.perfectomobile.com/Products/MobileCloud_Monitoring) and many others. The focus of these kind of analytics is usually more about how your app is actually used, user engagement, demographics, feature popularity, etc. However, it provides an opportunity to measure certain pieces of application performance *from within real-world usage*. While I wouldn't recommend this being your first line-of-defense in performance testing, having the ability to track real world performance metrics can be a powerful tool in tuning your application to your users' needs.

We've only scratched the surface of the various testing options available for mobile app development. What testing approaches & tools are you using when writing mobile apps? If you're not currently testing your application, what are some factors that would change your mind?