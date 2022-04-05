<!--
Current feedback:
 - I am worried about the length of the article (2000 words), and also about the time taken to write the article. It took me ~3 hours to write this incomplete draft. This time is a poor estimate because:
  + I had to make it up
  + I didn't have a draft to go off
  - I didn't have to talk to coworkers
  - I didn't have to spellcheck/make it good since it was a first draft

  Some potential solutions for the length include:
   + Chopping off sections (though I think the sections are quite minimal)
   + Making the more factual sections (such as Frameworks/Libraries) bullet-point lists, so that the author doesn't have to write full paragraphs
-->

<!--
Each report will start with a grading section (note that the table of contents and grading section will be combined in the final format).

Grades are chosen from [D, C, B, A, S]:
 - D: Very poor; made Haskell in some way untolerably awful
 - C: Poor; made Haskell hard to use... tolerable, but bad
 - B: Fine; nothing of particular note... not great, but definitely not bad either
 - A: Great; a big plus that made Haskell valuable to use over most other languages
 - S: Best-in-class; something that makes it hard to use other languages after using Haskell

For the section grades, I think I'll be adding modifiers (+/-) because sections end up having good and bad parts.

Note that you should make your grades relative to other realistic choices. Don't put a B just because a singularity AI didn't code your project for you - keep the grades comparative and grounded in some mainstream language experience.
-->


| Libraries and tooling |   B+  |
| --------------------- | ----- |
| Infra                 |   B   |
| Frameworks/Libraries  |   A   |
| Developer Tools       |   B   |

| Engineering Experience |   A+  |
| ---------------------- | ----- |
| Onboarding             |   A   |
| Engineer experience    |   S   |
| Code Maintenance       |   A   |

| Deployment             |   S   |
| ---------------------- | ----- |
| Runtime                |   A   |
| Errors/bugs            |   S   |
| Customer experience    |   S   |

- [Background](#background)
  - [Problem Background](#problem-background)
  - [Team Background](#team-background)
- [Choosing Haskell](#choosing-haskell)
  - [Options](#options)
    - [Scala](#scala)
    - [Haskell](#haskell)
  - [Library choices and Tooling](#library-choices-and-tooling)
    - [Infra](#infra)
    - [Frameworks/Libraries](#frameworkslibraries)
    - [Developer Tools](#developer-tools)
- [Using Haskell](#using-haskell)
  - [Engineering Experience](#engineering-experience)
    - [Onboarding](#onboarding)
    - [Engineer Feedback](#engineer-feedback)
    - [Code Maintenance](#code-maintenance)
  - [Deployment](#deployment)
    - [Runtime](#runtime)
    - [Errors/bugs](#errorsbugs)
    - [Customer experience](#customer-experience)
  - [Haskell at XXX](#haskell-at-xxx)
    - [Summary](#summary)
    - [Future Haskell Prospects](#future-haskell-prospects)

# Background

<!--
The background section is used to situate the reader and to establish a sense of trust in the future content of the report. This content should be extremely non-Haskell but should try to describe the technical requirements of the project. Your target audience a 50 year old hobby programmer.

This example tells the story from project inception; but you should tell the story as you experienced it - don't make things up ;)
-->

## Problem Background

<!--
Describe the problem that you were tasked with solving, and the key parameters for evaluating a solution. This section should describe the market and technical requirements of the problem. We are trying to focus on

If the problem was not well-defined, you could instead describe the market requirements and the type of prototyping experience you wanted going in.
-->

At Acme corporate, we needed to develop a new domain-specific language (DSL) in order to write low latency stock trades. Our clients were ~10 internal traders the company who would be writing 5-20 trade programs every day.

Our previous implementation was a Java library that output low-level C code. The library had been extended ad-hoc as the low-latency trade first grew and had a large number of features that were used by the team consistently; we would need to match most of these features. This implementation was no longer sustainable because writing non-trivial trades was leading to verbose and overly-complicated code for our traders to maintain.

We chose to reimplement our library by developing an in-house DSL that could scale to larger numbers of traders (50+) with in-built support for trader permissions and correctness guarantees.

## Team Background

<!--
Describe the programmers who would implement the solution and their programming background. Some key points include:
 - industry experience (job titles are enough)
 - programming experience (especially languages/paradigms)
 - team size
 - scaling requirements

Try to avoid criticising Haskell team scaling; leave that for the "Hiring/Team" section.
-->

Our team started as 2 senior engineers, with one having an extensive background in systems programming and the other in compilers and programming languages (primarily Haskell). We expected that within the year we would require 3 new junior programmers since we would have to achieve parity with the fully-featured Java implementation. We were not married to any paradigm going into the project - we primarily wanted a stable programming language that would enable maintainable high-correctness code.

Our team is now composed of 4 engineers on the compiler, written in Haskell. 3 of these engineers were experienced Haskellers; the other had more familiarity in Ocaml.

# Choosing Haskell

## Options

<!--
Describe the options that were evaluated prior to starting the project. Focus on at most 3 choices (preferably two) and their advantages and disadvantages. List Haskell last, and why you chose it.
-->

The project was split between two primary projects:
 - The frontend DSL compiler that our traders would interact with to write trades. This were the majority of the work would be.
 - A systems backend that the frontend language would compile down to. We knew this backend would be written in C, because we already had a trading library in C that we had we were having continuing success with.

For the DSL compiler, we looked primarily at two options: Scala and Haskell. Other options included Ocaml, which we did not have enough experience with, and Rust, but since we did not like the idea of managing memory within the compiler.

### Scala

Scala was an obvious choice because we would be able to use the old Java library via Scala's first-class Java support. We had no previous experience with Scala but were confident in its ability to scale due to colleagues' experiences and industry use.

Our PL engineer was not very experienced with Scala, but had dabbled before and achieved success in calling our Java library from Scala.

### Haskell

Haskell was the other choice to do our PL engineer's primary background being in Haskell. While we were worried about not being able to use the Java library, we also felt that the library had outgrown its welcome and that we would revisit most of the library anyway.

Haskell also had the added advantage that, prior to developing a frontend, we could first implement an embedded DSL in Haskell that would call the C code directly. This would enable us to try out new DSL designs without creating a frontend syntax and typechecker. While Scala also has reasonable eDSL features, with Scala, we would still have to have a code generator, which would make the development cycle slower, and the build tooling more complex.

Scala Native was also a valid alternative to Haskell, since we could call Java and C in the same codebase. However, we decided that we would either support interfacing with the old Java code _or_ with C, but not both; so Scala Native would not be too different from using Haskell.

We ultimately decided that the ability to develop an eDSL prior to a full-fledged type checker was invaluable, and that calling C directly from this eDSL would be too valuable during development. We were quite optimistic about either choice, but chose Haskell over Scala Native, primarily because of our PL engineer's extensive experience with Haskell and our lack of familiarity with Scala Native.

## Library choices and Tooling

<!--
From this point on, try to keep things in the present tense. Focus on what works and what doesn't. You might mention what didn't work and how you fixed it, but keep it short. The tone here should be more factual: list-like sentences are okay.

Note that many of your complaints about upgrading libraries/GHC versions might be better placed in the "Haskell ecosystem" section.
-->

### Infra

<!--
Important points to touch on:
 - Build tools (Cabal; Stack; Nix...)
 - Any caching or lack thereof
 - Mention any eccentric GHC's (forked GHC; GHCJS; Asterius; Eta; Frege...)
 - CI/versioning (Gitlab; Github; Gerrit...)
 - Key frameworks (Yesod/Servant/...; Esqueleto/Persistent/...; Miso/Reflex/IHP;)
 - Key libraries (lens/optics/generic-lens; megaparsec/attoparsec/parsec;)
 - Your favourite goofy Haskell library (singetons; recursion-schemes; tardis...)
-->

We use GHC 8.6.5 and Cabal for our build management and integrate that workflow with Gitlab CI. Getting CI up and running was not difficult thought the CI definitely takes its time once dependencies change.

The issues we've encountered regarding build times have been frustrating at times as they increase CI time to ~20 mins, though our developers do not experience these issues thankfully. We do not have any distributed caching. Initially our developers experienced large build times. We eventually found some folklore techniques to keep build times down and now developer builds are very manageable (<1 min projectwide); however, getting to this place was very taxing for a time.

### Frameworks/Libraries

<!--
Describe some of the key frameworks and libraries that you use on your team. Describe your experience with them and the problems as well as successes.

Here is a good opportunity to mention whether you had any issues with compatibility over multiple GHC versions with libraries, or if library quality/documentation was
-->

Our technology stack is guided by the experience of our initial compiler engineer. We avoid using any too-fancy compiler tools such as `recursion-schemes` and it has helped our onboarding experience drastically. As a smaller compiler team, the libraries have been less important and we adopt new libraries on a case-by-case basis.

Our language parser is implemented in Megaparsec, after transitioning from Parsec, which was a big improvement in terms of code quality and library documentation. The experience of using a parser combinator library for our language has been exceptional - it has been far easier to prototype and extend than the parser/lexer frameworks we have used in other languages.

We use Servant for some of our tooling, such as Slack integration for our language. Our non-Haskeller was hit with horrifying Servant error messages a couple of times and had to be taught techniques (typed-holes) to improve the errors. We would switch Servant if we had to scale the team to have many non-Haskellers.

Our favourite library has been the use of `tasty` testing framework, especially the libraries `tasty-bench` and `tasty-golden`. `tasty-bench` has allowed us to easily defines benchmarks and we have plans to integrate these benchmarks into our Gitlab CI process. `tasty-golden` has been invaluable in being able to easily write golden tests - we recommend golden tests for any compiler writers, but we have been greatful for the excellent built-in support that `tasty` has for them.

### Developer Tools

<!--
List your developers' work environment. If your devs use a mix of environments, feel free to list them and explain any difficulties the variety may have caused.
-->

We use VS Code and the Haskell extension for our developer environment. We used to use Stack but had to switch due to (at the time) poor integration with the Haskell Language Server (HLS); since switching HLS has had few problems at all, and once up and running the process is smooth. Switching to M1 required adding a few steps to the start up script, but using GHCup has made getting started on the project take <2 hours (the first build takes ~1 hour).

For linting we use hlint. Although the HLS integration is sometimes flakey, we have enjoyed the experience. For formatting we use Ormolu, which we have also had no problems with.

# Using Haskell

## Engineering Experience

### Onboarding

We have had few issues with onboarding because our engineers have mostly been Haskellers. The one engineer who came from Ocaml had some minor issues at first but quickly ramped up. We have not had too many issues with regards to hiring, though we do fear that it might not have been easy without the Haskell connections of our PL team lead.

We believe that part of the benefit of Haskell in onboarding has been the ease of understanding the project's code: with purity and granular effects, it has been easy for new employees to catch up with the compiler's complexities than it might have been otherwise.

### Engineer Feedback

Our team, without exception loves using Haskell. Compilers are perhaps the best application of Haskell and our employees' experience reflects that. Our employees are all excited to work on the project and to learn new ways to improve the codebase.

### Code Maintenance

<!--
NOTE: Perhaps we should split up code maintenance into multiple sections???
Code maintenance is a rather large section that includes:
 - Code review
 - Refactors/rewrites
 - Ecosystem and library stability
-->

Code maintenance is another commonly-cited advantage of using Haskell, and we have found the ease of maintenance to be overall excellent. Code refactors and reorganisation is far easier in Haskell than in the old Java codebase, especially because effects make it clear what code can be inlined or extracted as a function.

The biggest downside we have found, however, is that common refactoring tools in Java are either not available or uncommon in Haskell. For example, in Java, a right click in the IDE will allow an engineer to rename all occurrences of an identifier. This lack of such common tooling can be a very real grind at times.

We have also needed to take a purposefully controlled approach to code complexity. In Haskell, code can often be expressed in elegant ways that might be less easy to understand. Thankfully, the high amount of Haskell experience on the team makes it so that this has not been an issue.

## Deployment

### Runtime

<!--
Describe the runtime behaviour of your Haskell program in production:
 - Memory footprint
 - Runtime
 - Scaling with users/data
 - Benchmarking
-->
We have not yet had any significant problems with runtime. We have extensive experience with Haskell programs on the team and have therefore been able to use strictness ahead of time to prevent large memory footprints.

When scaling to larger source sets for compilation (such as multi-module trade descriptions), we did not encounter any significant issues relating to performance. The biggest difficulty we had was ensuring the correct laziness for compilation inputs - but thankfully our Haskell experience made this no worse than the problems with laziness that might exist in a strict language.

### Errors/bugs

<!-- TODO
Some things to include:
 - Frequency of bugs
 - Nature of bugs
 - Debugging: how easy it to debug? how long to triage bugs
 - How often did the code fail in production? How often were bugs caught in tests
-->

### Customer experience

<!-- TODO
Was the final product good for customers
-->

## Haskell at XXX

### Summary
<!--
Try to hone in on the value (or lack of value) that Haskell has provided. Keep this short and sweet: <1 paragraph if possible. Some things that could help to mention (sadly numbers talk the best):
 - How much time/value (maybe in money?) has Haskell added to the company?
 - How did Haskell meet/break expectations?
-->

### Future Haskell Prospects
<!--
Describe the future of Haskell at your company:
 - Would/will you use Haskell again at ACME?
 - Will current Haskell usage grow or shrink?
-->
