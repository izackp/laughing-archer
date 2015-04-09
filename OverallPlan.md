####The Overall Goal:
Develop bug-free apps faster

####The Ideal Process:
- Push Code To Repo
- CI Server Runs Tests Then Either:
 - Returns an Error or:
 - Increments the build number
 - Tags the commit with the build number
 - Builds a Signed IPA with the latest provisioning
 - Deploys that IPA to iTunes Testflight

####Unit Testing
 - Builtin Unit Test System is great
 - Expecta framework also looks like an interesting solution to make more _readable_ tests. Not sure if its needed tho.

####UI Based Testing
 - KIF - An excellent framework that works with xcode's unit tests that can implement tests quickly.
 - Calabash - Provides Gherkin tests which is _value_ to clients. Though slower to implement.
 - There is a lot of inherit problems with UI Testing. The problem is, it takes a ton of time to implement and maintain UI based tests.
  - We can avoid UI Tests altogether
  - Write only tedious or important tests
  - Dedicate a time slot to writing UI tests without spending more time then allocated
  - Avoid UI Tests and abstract the view controller so you can write unit tests on user's input/output

  [More Details](uitesting)

####CI Server
 - Travis

####Deployment
 - https://fastlane.tools/ - Seems to provide a *lot* of tools to deploy builds with. Documentation seems lacking.
 - altool - Used to submit IPAs to iTunes
 - Hockeyapp - Deployment software for both iOS and Android
