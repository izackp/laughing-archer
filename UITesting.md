UI Tests
--------

This is mostly research to help me decide which is the best framework to go with.

The overall goal is to keep anyone from having to work until midnight testing the same features over and over again through a completely automated process.

####KIF

http://github.com/kif-framework/KIF

KIF takes the approach of writing each UI test in Objective-C as an XCTestCase and manipulates the UI as a user would do using internal Apple APIs that access each UI element’s accessibility labels.


*Pros:*
 - Integrated into Xcode testing - KIF tests are XCTestCases and can be run together with your normal Unit Tests
 - Tests are written in Objective-C - no new language to learn for the developer
 - Can access internal methods when needed - this is helpful when, for example, you want to reset state between tests
 - Does not require embedding any special code in the app - KIF test code like unit tests is separate from the app code
 - Works on both simulator and device
 - Continuous Integration support - runs with Apple Bots and can be integrated with CI tools such as Jenkins
 - Open source - source code is available to fix any issues encountered
 - Active community - apart from a Google discussion group, Google itself is known to use KIF

*Cons:*
 - Uses internal Apple APIs - Apple could change these APIs breaking KIF
 - Only for iOS - currently OS X is not supported, and clearly KIF will not help you with Android UI testing

Conclusion: Not worried about the cons. Seems like a great solution. Integrated into Xcode testing is a Huge Plus.


####Calaba.sh

http://Calaba.sh

Calabash is an automated testing technology for Android and iOS native and hybrid applications. A driver is planted inside of the app
and Calabash runs gherkin tests through that driver.

*Pros:*
 - Human readable acceptance tests - passing tests provides something we can deliver to the client and tests is something the client can write themselves
 - Android Support
 - Open Source - all the tools in this approach are open source tools
 - Plans to support c# and jvm
 - CI support

*Cons:*
 - Need to learn Gherkin and Ruby
   - The foundation of most Gherkin tests will have to be written in ruby
 - Separate tool from Xcode - cannot be run together with your other Unit Tests
 - Embedded code - some of these tools embed code in your app so the final app is different from the tested one

Conclusion: Setup is clunky. The most valuable feature is the ability to provide gherkin based acceptance tests. Android support is a plus since the same gherkin tests can be used for both, but I believe they will need to have different ruby scripts to _back_ those tests.


####Unit Tests For View Controllers

http://appleprogramming.com/blog/2014/01/20/tdd-of-viewless-controllers/

This explains a design pattern which separates a _controller_ from a _viewController_. At which point, this gives us a testable layer to write unit tests with.

*Pros:*
 - Avoids actually writing tests for UI since UI is so dynamic and changes frequently

*Cons:*
 - Not actual UI tests - I'm not sure if this will solve the same set of problems
 - Might be too abstract. Well written ViewControllers should generally be around 300 lines of code.

Conclusion: Needs Further Research

####Appium

http://appium.io

Appium and ios-driver focus on leveraging the Selenium WebDriver protocol and supporting it on iOS.

*Pros:*
 - Supports ObjC, Ruby, C#, Java, node.js, Python
 - Awesome GUI based introspector that does some code generation
 - Works externally - No need to embed any code
 - CI Support - I know it at least supports Jenkins

*Cons:*
 - Limited Android support (v16+)
 - Documentation is clunky

Conclusion: Seems like a solid framework..


####The Apple Approach

The use of Instruments with the javascript UIAutomation library

*Pros:*
 - Apple tool - you are taking an approach that will be supported in the foreseeable future
 - Good for running performance, analysis and profiling tests - Instruments shines in these areas
 - Continuous Integration support - can be integrated with CI tools such as Jenkins, Bwoken & cisimple
 - Works on both simulator and device
 - Can be used for both iOS and OS X - no need to learn a new tool should you develop an OS X app
 - Does not require embedding any special code in the app - test and production app are the same

*Cons:*
 - Javascript based
 - No built in test structure
 - Tests are too easy to break when making minor changes to the UI
 - Apple tool - no open source so you cannot fix any issues you come across
 - Separate tool from Xcode - cannot be run together with your other Unit Tests and currently cannot be run with Apple’s Bots
 - No access to code - UI Automation manipulates the UI similar to a user using accessibility labels and you have no access to the code itself if for example you would like to reset the app for a new test
 - No android support

Conclusion: Looks like a pain. My Least Favorite.


####KIF vs Calabash

The only reason why KIF doesn't win hands down is because Calabash provides the value of Gherkin tests. However, it is possible to write tests based on Gherkin with KIF.

```
/* @given that the app is in a fresh clean state
 @and   that no one has ever registered with the server

 @then  the user can register their themselves with the server
 @and   immediately start with the rider's map
 @and   their location on the map shows
 */

- (void)testRegistration
{
    [tester flushDbase];
    [tester reset];
    [tester singleUserRegistration];
    [tester showUserCurrentLocationOnMap];
}
```
