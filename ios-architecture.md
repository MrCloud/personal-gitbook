# iOS Architecture

## Overview

Today we have a kind of Monolithic Application with a few modules, that may need a redesign or a split.

fig.1 Pre-existing Architecture

![](blob:https://app.gitbook.com/56be7286-c1aa-4a41-874b-dca2b3a7267e)

### Why do we need a Modular Architecture ? 

> A modular architecture is a software design technique that emphasizes separating the functionality of a program into independent, interchangeable modules, such that each one contains everything necessary to execute only one aspect of the desired functionality.

### Business reasons

* The company requires that parts of the codebase are reused and shared across projects, products, and teams; \(EMA, other BUs, …\)

### Tech reasons

* The codebase has grown to a state where things become harder and harder to maintain and to iterate over;
* Development is slowed down due to multiple developers working on the same monolithic codebase;
* Besides reusing code, you need to port functionalities across products.

### Multiple teams

* The company structured teams following strategic models \(e.g. Spotify model\) and functional teams only work on a subset of the final product;
* Ownership of small independent modules distributed across teams enables faster iterations;
* The much smaller cognitive overhead of working on a smaller part of the whole product can vastly simplify the overall development.
* We’ll be onboarding teams from other BUs soon \(Italy, …\) and we need to allow efficient, effective collaboration with these teams on the modules we’ll be sharing together.

### Pre-existing knowledge

* Members of the team might already be familiar with specific solutions \(Carthage, CocoaPods, Swift Package Manager, manual frameworks setup within Xcode\). In the case of a specific familiarity with a system, it’s recommended to start with it since all solutions come with pros and cons and there’s not a clear winner at the time of writing.

> Modularizing code \(if done sensibly\) is almost always a good thing: it enforces separation of concerns, keeps complexity under control, allows faster development, etc. It has to be said that it’s not necessarily what one needs for small projects and its benefits become tangible only after a certain complexity threshold is crossed.

fig.2: Upcoming Architecture \(Draft 1 of a modular architecture for our app\)

![](blob:https://app.gitbook.com/a2f33d7f-efa8-46fa-9106-778f0bc50b09)

This first draft of a modular architecture for our project / product shows different grouping of modules here is a quick explanation of each group: 

**Core Modules**: They are the utility backbone behind other modules, they offer shared utilities that ease development of other modules, with our own Foundation additions, HTTP Client configuration, Reusable UI Components …

**Domain Commons Modules**: Since we do not have a single, centralized way to deal with our APIs we cannot have one client for all our micro-service APIs. Domain Modules will offer a way to interact with our Micro-Backends in order to deal with Domain objects \(Entities\) within the App. 

**Feature / UI Modules**: They are the whole business in our app, these modules include the Business Logic and UI for a feature / set of features of the app. They are configurable \(ie. Features can be flipped on and off\) cleanly designed and have their own internal architecture which allows to customize, extend or substitute any part of it \(ie. the UI\) in order to use another implementation.

## In Depth:

### 1. App Context

Our app history takes us back to 2014 when we decided to take down our previous app and write a new one from scratch, using Swift 2.x at the time. Back in the day there was a completely different group strategy and there was no need nor effort to converge or collaborate with other BUs in order to reuse or share any bit of code. 

Today we’re facing new challenges, and challengers on the market will be able to take some of our market share. We also want to capitalize on the effort produced as different teams distributed in BUs in order to go further with our apps and converge, share, reuse our code between projects. 

Due to the history of Leroy Merlin / Adeo, we build different apps:

* Per Country \(BUs\)
* Per Brand
* From different codebases

We have today the opportunity to onboard Leroy Merlin Italy and work together to build our collaboration model and update our codebase to support our needs in sharing and reusing code, improving quality and discoverability of our software among other BUs.

Let’s dive into the architecture that will support and fulfill these needs.

### 2. Core & Shared Modules

The Core \(Shared\) modules represent the foundations of our stack, things like:

* Custom UI Components
* Theming engine
* tracking and analytics libraries
* Common HTTP Client setup
* …

These modules, with little change over time \(due to their nature\), will have to be backward compatible and it’s important that we deprecate old APIs when introducing new ones. Any higher level module \(Domain, Feature / UI modules, app modules\) can have core / shared modules as dependencies, since updating these modules will require propagating the changes in the stack, we’ll try to keep the number of shared modules very limited.

These modules must respect the following set of rules: 

* _**TBD**_

### 3. Domain Commons Modules

Domain Commons Modules are modules designed to consume our micro-backends in order to interact with all our information systems, these modules contain the definition of the services relative to each domain along with HTTPClient implementations required to perform the requests to these micro-backends and perform the serialization/deserialization of data from/to entities usable in the apps. 

These modules must respect the following set of rules: 

* UIKit Code usage is prohibited in these Modules
* _**TBD**_

### 4. Feature / UI Domain Modules

Feature / UI Modules are higher domain modules that contains features specific to an area of the product. As the fig.2 diagram above shows, the sum of all those parts makes up the Leroy Merlin App. They contain the business logic and UI of the app features. These modules are constantly modified and improved by our teams and updating the consumer apps to use newer versions is an explicit action. We don’t particularly care about backward compatibility here since we are the sole consumers and it’s common to break the public interface quite often if necessary.

These modules must respect the following set of rules: 

* _**TBD**_

### 5. Structure of a Module

When building a module, our root _principle_ is:

> Every module should be well tested, maintainable, readable, easily pluggable, and reasonably documented.

First of all, the code must be unit _tested_, and in the case of UI domain modules, UI tests are required too. Without reasonable code coverage, no code is shipped to production. This is the first step to code _maintainability_, where maintainable code is intended as “code that is easy to modify or extend”. _Readability_ is down to reasonable design, naming convention, coding standards, formatting, and all that jazz.  


_**COMPLETE WITH STRUCTURE Example**_

### 6. Overall Design

_**CHALLENGE THIS APPROACH \(CF 10. Monorepo\)**_

fig.3 Design

![](blob:https://app.gitbook.com/7a0f13d0-7734-47d4-b768-96b453fdd45d)

We generate all our modules using CocoaPods via $ pod lib createwhich creates the project with a standard template generating the Podfile, podspec, and demo app in a breeze. The podspec could specify additional dependencies \(both third-party and Core modules\) that the demo app’s Podfile could specify core modules dependencies alongside the module itself which is treated as a development pod as per standard setup.  
The backbone of the module, which is the framework itself, encompasses both business logic and UI meaning that both source and asset files are part of it. In this way, the demo apps are very much lightweight and only showcase module features that are implemented in the framework.  
The following diagram should summarize it all.

_**CHALLENGE THIS:**_

_The architecture described in fig.2 will suppose to have 2 different projects/repositories for a whole domain of features for the product since a Domain Commons module can be shared as a dependency by various Feature Domain modules it cannot be part of the same repository of the Domain Commonsmodule._ 

### 7. Demo Apps \(Complete with Ideas\)

Every module comes with a demo app we give particular care to. Demo apps are treated as first-class citizens and the stakeholders are both engineers and product managers. They massively help to showcase the module features – especially those under development – vastly simplify collaboration across Engineering, Product, and Design, and force a good mock-based test-first approach.  
Following is a SpringBoard page showing our demo apps, very useful to individually showcase all the functionalities implemented over time, some of which might not surface in the final product to all users. Some features are behind experiments, some still in development, while others might have been retired but still present in the modules.  
Every demo app has a main menu to:

* access the features
* force a specific language
* toggle configuration flags via Feature Flipping
* customize mock data

### 8. Module Internal Architecture

It’s common for iOS teams in the industry to debate on which architecture to adopt across the entire codebase but the truth is that such debate aims to find an answer to a non-existing problem. With an increasing number of modules and engineers, it’s fundamentally impossible to align on a single paradigm shared and agreed upon by everyone. Betting on a single architectural design would ultimately let down some engineers who would complain down the road that a different design would have played out better.  
Stick with the following rule of thumb:

> Developers are free to use the architectural design they feel would work better for a given problem.

### 9. Dependency Management

_**CHALLENGE THIS:**_

We rely heavily on CocoaPods since we adopted it in the early days as it felt like the best and most mature choice at the time we started modularizing our codebase. With a growing number of modules, comes the responsibility of managing the dependencies between them. No panacea can cure [dependency hell](https://en.wikipedia.org/wiki/Dependency_hell), but one should adopt some tricks to keep the complexity of the stack under reasonable control.  
Here’s a summary of what worked for us:

* Always respect [semantic versioning](https://semver.org/);
* Keep the dependency graph as shallow as possible. From our apps to the leaves of the graph there are no more than 3 levels;
* Use a minimal amount of shared dependencies. Be aware that every extra level with shared modules brings in higher complexity;
* Reduce the number of third-party libraries to the bare minimum. Code that’s not written and owned by your team is not under your control;
* _Never make modules within a group \(domain, core, shared\) depend on other modules of the same group;_
* Modules can never be inter-dependent
* Automate the publishing of new versions. When a pull request gets merged into the master branch, it must also contain a version change in the podspec. Our continuous integration system will automatically validate the podspec, publish it to our private spec repository, and in just a matter of minutes the new version becomes available;
* _Fix the version for dependencies in the Podfile. Whether it is a consumer app or a demo app, we don't want both our modules and third-party libraries to be updated unintentionally. It’s acceptable to use the_ [_optimistic operator_](https://guides.cocoapods.org/using/the-podfile.html#specifying-pod-versions) _for third-party libraries to allow automatic updates of new patch versions;_
* _Fix the version for third-party libraries in the modules’ podspec. This guarantees that modules’ behavior won’t change in the event of changes in external libraries. Failing to do so would allow defining different versions in the app’s Podfile, potentially causing the module to not function correctly or even to not compile;_
* _Do not fix the version for shared modules in the modules’ podspec. In this way, we let the apps define the version in the Podfile, which is particularly useful for modules that change often, avoiding the hassle of updating the version of the shared modules in every podspec referencing it. If a new version of a shared module is not backward compatible with the module consuming it, the failure would be reported by the continuous integration system as soon as a new pull request gets raised._

### 10. Monorepo Approach

When it comes to dependency management it would be unfair not to mention the opinable [monorepo](https://en.wikipedia.org/wiki/Monorepo) approach. Monorepos have been discussed quite a lot by the community to pose a remedy to dependency management \(de facto ignoring it\), some engineers praise them, others are quite contrary. Facebook, Google, and Uber are just some of the big companies known to have adopted this technique, but in hindsight, it’s still unclear if it was the best decision for them.  


In my opinion, monorepos can sometimes be a good choice.  
For example, in our case, a great benefit a monorepo would give us is the ability to prepare a single pull request for both implementing a code change in a module and integrating it into the apps. This will have an even greater impact when all the Leroy Merlin / Adeo consumer apps are globalized into a single codebase. But as of today and due to the Domain Commons Module layer existence we cannot achieve this approach.

