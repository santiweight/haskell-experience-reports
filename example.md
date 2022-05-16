<!--
For Santi:
          Current feedback:
            - I am worried about the length of the article (2000 words), and also about the time taken to write the article. It took me ~3 hours to write this incomplete draft. This time is a poor estimate because:
            + I had to make it up
            + I didn't have a draft to go off
            - I didn't have to talk to coworkers - I didn't have to spellcheck/iterate since it was a first draft

          Some potential solutions for the length include:
            + Chopping off sections (though I think the sections are quite minimal)
            + Making the more factual sections (such as Frameworks/Libraries) bullet-point lists, so that the author doesn't have to write full paragraphs
-->

<!--
Each report will start with a grading section (note that the table of contents and grading section will be combined in the final format).

Grades are chosen from [S, A, B, C, D]:
 - D: Very poor; made Haskell in some way untolerably awful
 - C: Poor; made Haskell hard to use... tolerable, but bad
 - B: Fine; nothing of particular note... not great, but definitely not bad either
 - A: Great; a big plus that made Haskell valuable to use over most other languages
 - S: Best-in-class; something that makes it hard to use other languages after using Haskell

For the section grades, modifiers (+/-) will be added since they will be the average of their subsections.

Grade relative to other realistic competitors. For example, you might compare Haskell's IDE or stability to Java's, and Haskell's correctness and type system facilities to Scala's or Ocaml's.
-->


<!--
Explanations and examples for each section can be found by going to each subheading and seeing the documentation under that subheading. Use the table of contents to your advantage.
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
  - [Authors](#authors)
  - [Problem Domain](#problem-domain)
  - [Team Background](#team-background)
- [Choosing Haskell](#choosing-haskell)
  - [Options](#options)
    - [Scala](#scala)
    - [Haskell](#haskell)
- [Library choices and Tooling](#library-choices-and-tooling)
  - [Infra](#infra)
  - [Frameworks/Libraries](#frameworkslibraries)
  - [Developer Tools](#developer-tools)
- [The Developer Experience](#the-developer-experience)
  - [Onboarding](#onboarding)
  - [Engineer Satisfaction](#engineer-satisfaction)
  - [Code Maintenance](#code-maintenance)
- [Deployment](#deployment)
  - [Runtime](#runtime)
  - [Errors and Debugging](#errors-and-debugging)
  - [Customer experience](#customer-experience)
- [Haskell at ACME](#haskell-at-acme)
  - [Hiring](#hiring)
  - [Summary](#summary)
  - [Future Haskell Prospects](#future-haskell-prospects)

# Background

<!--
The background section situates the reader in some team and problem domain. The background should develop trust between reader and author, through credentials and understanding of project requirements.

Target audience: general technical audience. No Haskell knowledge assumed
-->

## Authors

<!--
Who is writing the article: the author themselves; co-authors.

Read the `principles.md` for some ideas on who would be a great addition to the authors section for adding credibility to the report.

Try to keep the background short and sweet (you've seen those 1 sentence blurbs many times) - highlight:
 - prior industry experience (non-Haskell is a plus)
 - managerial experience
 - past language-based lessons
-->

John Doe is a industry veteran with 20 years of software engineering experience. He spent the first 10 years of his career working on Java's compiler at Oracle, but has since been developing compilers in functional programming languages. John's focus is on the development of highly maintainable compiler pipelines, with heavy emphasis on modern compiler design techniques. John is an expert Java and Haskell programmer, with some minimal experience in C and LLVM when writing compiler backends.

<!--
Could someone write a fake systems engineer career summary for me XD
-->

Jane Doe has been working [INSERT example background of systems engineer]...

## Problem Domain

<!--
Describe the problem that you were tasked with solving, and the key factors considers for a valid solution. This section should describe the market and technical requirements of the problem:
 - client/stakeholder
 - deadlines
 - prototype/rewrite/legacy project
-->

At Acme corporate, we needed to develop a new domain-specific language (DSL) for running low latency stock trades. Our clients were ~10 internal traders the company who would be writing 5-20 trade programs every day.

Our previous implementation was a Java library that compiled down to low-level C code. The library had been extended ad-hoc as the trading team grew and the library had a wide number of features that were used by the team consistently. We would need to match most of these features. This implementation was no longer sustainable because writing non-trivial trade logic was leading to verbose and overly-complicated code for our traders to write without help.

We chose to reimplement the old library by developing an in-house DSL that could scale to larger numbers of traders (50+) without an engineer's help. We also decided to have the new DSL include systems for security permissions and certain correctness assurances (such as detection of faulty trades; limits on trade sizes).

This report will focus on the trading DSL, since much of the low-level C code was not changed during the rewrite.

## Team Background

<!--
Describe the experience/background of the team as a whole (if different from the authors):
 - industry experience (job titles are enough)
 - programming experience, especially languages/paradigms
 - team size
 - team scaling requirements

Focus on the team's requirements (leave evaluation of Haskell etc. for post-background)
-->

Our team started with 2 senior engineers, with one having an extensive background in systems programming and the other in compilers and programming languages (primarily Haskell). We expected that within the year we would require 3 new programmers to work on the compiler, in order to achieve parity with the fully-featured Java implementation and to add ease-of-use features.

Our team is now composed of 4 engineers on the compiler, written in Haskell. 3 of these engineers were experienced Haskellers; the other had experience in Ocaml and lisp dialects.

# Choosing Haskell

## Options

<!--
Describe the options that were evaluated prior to starting the project. Focus on at most 3 choices (preferably two) and their advantages and disadvantages. List Haskell last, and why you chose it.

Feedback: Is this too much unnecessary info? Maybe best to dive in quicker - who cares what other languages were considered.
-->

For the compiler, we looked primarily at two options: Scala and Haskell. Other options included Ocaml, which we did not have enough experience with, and Rust, but since we did not like the idea of managing memory within the compiler.

### Scala

Scala was an obvious choice because we would be able to use the old Java library via Scala's first-class Java support.  We were apprehensive about continuing to use a language that did not interoperate smoothly with C. We were initially optimistic about Scala Native, but realised that using Scala Native would not allow us to use the Java library in early development.

### Haskell

Haskell was our primary option, given our background and first-in-class compiler writing support. Though we would have to rewrite a lot of the old Java logic from scratch, we felt that the time for rewriting would be offset by the down-the-line advantages of choosing a functional programming language with a C FFI.

With Haskell, we knew that we could first implement an embedded DSL in Haskell that would call C code directly, without the initial need for a full parser-typechecker frontend. While Scala also has reasonable eDSL features, we would still have required a code generator and build pipeline (to generate and run the C code), which would make the development cycle slower than necessary.

We ultimately decided that the ability to develop an eDSL prior to a full-fledged type checker was invaluable, and that being able to call C directly from the compiler would be invaluable for testing and rapid development.

# Library choices and Tooling

<!--
The rest of the report will focus on evaluating Haskell as a production tool.

Focus on what works and what doesn't. Try to focus on your current issues, and avoid mentioning issues that are no longer relevant, for example:
 - Good: Two years ago, Haskell's IDE support was minimal, but has recently become quite good in our experience. Our engineer's report have to restart the IDE once every few days, but all-in-all the development experience is comparable to other languages (besides Java).
 - Bad: last year, there was very little support for a Haskell IDE, and we struggled with it for a long time. We struggle with this for a few years, until...

Don't avoid Haskell's issues, just keep the issues contemporary. The same goes for languages you might compare Haskell against.

Note that many of your complaints about upgrading libraries/GHC versions might be better placed in the "Code Maintenance" section.
-->

## Infra

<!--
Points to touch on:
 - Build tools (Cabal; Stack; Nix...)
 - Mention any eccentric GHC's (forked GHC; GHCJS; Asterius; Eta; Frege...)
 - Build times/caching
 - CI/versioning (Gitlab; Github; Gerrit...)
-->

We use GHC 8.10.7 and Cabal for our build management and integrate that workflow with Gitlab CI. Getting CI up and running was not difficult.

Initially our developers experienced large build times during development, but were ablesome folklore techniques to keep build times down and now developer builds are very manageable (<20 seconds). We also started testing GHC 9.2, which brings our build times down by 50% to 10 seconds from clean.

When dependencies change, our CI rebuilds all dependencies, which bumps CI's build time to ~20 mins, which certainly leaves us wanting for an easily available caching option. We have considered using cachix as a solution, but fear that it won't be worth the effort.

## Frameworks/Libraries

<!--
Describe some of the key frameworks and libraries that you use on your team:
 - Key frameworks (Yesod/Servant/...; Esqueleto/Persistent/...; Miso/Reflex/IHP;)
 - Key libraries (lens/optics/generic-lens; megaparsec/attoparsec/parsec;)
 - Documentation of libraries

Now is a good time to describe ecosystem stability and GHC version migrations.
-->

Our library selection is guided by the experience of our lead compiler engineer and uses popular production-Haskell  libraries. We avoid using any fancy compiler tools (e.g. `recursion-schemes`) in order to aid in the onboarding experience. As a smaller compiler team, we have not had a need for a larger framework, and so dependencies to the project as needed.

Our language parser is implemented in Megaparsec, after transitioning from Parsec, which was a big improvement in terms of code quality and library documentation. The experience of using a parser combinator library for our language has been exceptional - it has been far easier to prototype and extend than Antlr, which we used in the Java library. Similarly, we switched to Haskell's `prettyprinter` library, which we enthusiastically recommend. Overall, the parser/prettyprinter experience is first class in Haskell, and is a significant improvement over Java's options.

We use Servant for some of our tooling, such as Slack integration for our language. Our non-Haskeller was hit with horrifying Servant error messages a couple of times and had to be taught to use typed-holes during development. We would likely switch Servant if our team had less experience with Haskell.

For testing we use the `tasty` testing framework, especially the supplementary library `tasty-golden`. `tasty-golden` has been invaluable in being able to easily write golden tests. We recommend golden tests for any compiler writers, especially when in comes to pretty printing source code. Though, the content of the library itself is very good, unfortunately Haskell and `tasty` do not have any integration with VSC's Haskell IDE. In our Java code, we were able to click run in the source text when wanting to write a test. We know have to run tests from the commandline: unforunate...

## Developer Tools

<!--
List your developers' work environment. If your devs use a mix of environments, feel free to list them and explain any difficulties the variety may have caused:
 - IDE
 - refactoring tools
 - linters
 - code formatters
-->

We use VS Code and the Haskell extension for our developer environment. We used to use Stack but had to switch due to (at the time) poor integration with the Haskell Language Server (HLS). Since switching to Cabal, we have had few problems getting set up with HLS, besides for the occasional 20s restart.

Switching to M1 Mac laptops required adding a few steps to the start up script due to some as-yet unfixed bugs in Haskell's compiler that require setting some C_INCLUDES for building dependencies. Even so, because of the recent improvements to Haskell's installation toolchain (GHCup), getting started with the repo take <1 hour (the first full build takes ~20 mins locally).

For linting we use hlint. Although the HLS integration is sometimes flakey, we have enjoyed the experience. For formatting we use Ormolu, which we have also had no problems with. We also intermittently run the Stan code-linter to check for any suspicious anti-patterns.

# The Developer Experience

<!--
This section should aim to convey the qualitative experience of using Haskell in production. Here it is appropriate to mention quantitative metrics (such as time to ramp up on a team) but also qualitative experiences (such as "I like/hated using Haskell")
-->

## Onboarding

<!--
How was the experience of onboarding new developers to the project. Be sure to consider the backgrounds of the new team members and how industry experience and Haskell familiarity affect learning a new Haskell project.
-->

For us, onboarding Haskell has been little different from other languages, mostly because our engineers have mostly been Haskellers. The one engineer who came from Ocaml had some minor issues (such the aforemention `servant` errors) at first but quickly ramped up.

We have found that, due to Haskell's purity and descriptive type systems, it has been **notably easy** for experienced Haskellers to catch up with the codebase without too much handholding. Most of the time, our experienced Haskellers were quite content to read an introductory readme to some part of the codebase, and then fill in the details by reading functions' type signatures.

## Engineer Satisfaction

<!--
Purely subjective: do engineers like using Haskell? Do they feel engaged with the project and do they enjoy working on it?
-->

Our team, without exception, enjoys using Haskell (even our mildly-converted Ocaml engineer). Compilers are perhaps the best application of Haskell and our employees' experience reflects that. Our employees are all excited to work on the project and improve the codebase. All of us love using Haskell, and for most of us it is our first choice when it comes to programming language.

## Code Maintenance

<!--
TODO: Perhaps we should split up code maintenance into multiple sections???

Code maintenance is a rather large section that includes:
 - Code review
 - Refactors/rewrites
 - Ecosystem and library stability (particularly bugs and backwards compatibility)
 - GHC version bumps
-->

Code maintenance is another commonly-cited advantage of using Haskell, and we have found the ease of maintenance to be overall excellent, but have wanted for better refactoring tools.

Code refactors are far easier in Haskell than they were in the Java codebase, mostly due to Haskell's purity and the fact that the types are so precise.

The biggest downside we have found, however, is that common refactoring tools in Java are either not available or uncommon in Haskell. For example, in Java, a right click in the IDE will allow an engineer to rename all occurrences of an identifier. This lack of such common tooling can be a very real grind at times.

We have been helped by taking a purposefully controlled approach to code complexity. In Haskell, code can often be expressed in elegant ways that might be less easy to understand. At times, we have to catch ourselves from making overly-eager Haskelly design choices, and we are careful not to introduce too many fancy Haskellisms at the expense of less-experienced Haskell engineers.

Haskell's ecosystem stability is probably Haskell's greatest fault. When it came to less-commonly used libraries, such as diagram libraries, we sometimes had to make version bump PRs ourselves, but many of these PRs have been left unanswered, and so we are stuck on employees' library forks. Thankfully, the same has not occurred for popular libraries, which we have found to be easy to integrate with our libraries' dependencies.

We have not yet experienced any problems with GHC version bumps, and were unaffected by the simplified subsumption changes in GHC 9. At times, we become concerned by some core library churn, such as the suggested changes to Eq of no Neq, or the Data.List monomorphization. Thankfully, whereas before we might have been bitten by such changes, it appears that the core libraries are far more stable and less subject to change than they have been in the past...

# Deployment

## Runtime

<!--
Describe the runtime behaviour of your Haskell program in production:
 - Memory footprint
 - Runtime performance
 - Scaling with users/data
 - Benchmarking
-->

We have not yet had any problems with runtime. Our team has extensive experience with Haskell programs and so we have been able to intercept potential problems before they occur, such as by using strict records fields throughout the repo.

We have noted at times that it can be difficult to reason about performance, and so when it comes to optimising the code (which we rarely do), the work has generally been done by senior engineers. Such folk knowledge has included some precise compiler flags to GHC that override some silly defaults and helping other engineers to understand cost-centres when attempting to improvement the runtime.

## Errors and Debugging

<!--
Some things to include:
 - Frequency of bugs
 - Nature of bugs
 - Debugging: how easy it to debug? how long to triage bugs
 - How often did the code fail in production? How often were bugs caught in tests

TODO Add an example of debugging in example?
-->

In our experience, Haskell's reputation of "if it compiles, it works" has been quite true. Whenever we have found certain bugs in our code, we have often been able to ensure they never occur again through strategic use of Haskell's type system. In contrast, the Java code was riddled with recurring bugs, such as mistakes in inter-stage rewrites; and we had some quite awful experiences with Antlr, which caused quite a number of NPE's for us...

For example, we originally had an AST that looked something like:

```
data VarRef = SourceVar Text | RenamerVar ModuleOrigin Text | RuntimeVar Int
data Expr = Var VarRef | IfThenElse ...
```

Two different stages of our compiler had rewrites over the variables in our AST: first to resolve user's variables to the right module (such as in the case of an import), and then a rewrite to make variables be referenced by integers for runtime.

We encountered a recurring bug caused by a failure to rewrite some variables to the next stage's expected variable type. We solved this problem with:

```
newtype SourceVar = SourceVar Text
data RenamerVar = RenamerVar ModuleOrigin Text
newtype RuntimeVar = RuntimeVar Int

data Expr var = Var var | IfThenElse (Expr var) (Expr var) (Expr var)
```

With the new AST definition, it is now impossible for this bug to occur (in fact we caught one yet-undiscovered bug), because Haskell derives Functor for us without possibility of failure. This type of refactor in our codebase has saved us from many unnecessary hardships down the line!

## Customer experience

<!--
How well was the product received by customers:
 - performance, such as app responsiveness or performance under heavy workloads
 - ease of use
 - ability to deliver on time
-->

TODO

# Haskell at ACME

## Hiring

<!--
The experience of acquiring new talent for the team:
 - access to candidates
 - quality of candidates
 - location of candidates (remote/local)
 - evaluation of candidates
 - affordability of talent -- TODO is this a reasonable thing to discuss?
-->

We have not had any issues with regards to hiring, in fact it has been rather easy due to connections within the team and Haskellers' eagerness to join a compiler team using Haskell. Most of our team have worked with another member of our team in the past, and those who had not came by personal recommendation. It is worth noting that, while we do have an office on the east coast, only 2 of our team commute to work, and our team is otherwise remote, with an occasional on-site meeting every third month or so.

TODO Is there more to say?

## Summary
<!--
Try to hone in on the value (or lack of value) that Haskell has provided. Keep this short and sweet: <1 paragraph if possible. Remember that numbers talk:
 - How much time/value (maybe in money?) has Haskell added to the company?
 - How did Haskell meet/break expectations?
 - Did the project itself succeed?
 - The state of the project as of writing
-->

TODO

## Future Haskell Prospects
<!--
Describe the future of Haskell at your company:
 - Would/will you use Haskell again at ACME?
 - Will current Haskell usage at ACME grow or shrink?
-->

TODO