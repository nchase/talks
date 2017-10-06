# Measuring and Optimizing Tail Latency - Kathryn McKinley

- tail latency matters
- "engineering trends"
- we need to optimize to deliver a good experience, keep costs down, and maybe
  save the env
- tail latecy reduction is at odds with keeping server costs low
  - requests are split between servers, so slowest server matters quite a bit
  - "tail at scale"
  - diurnal workloads

  what is in the tail?
  - 3 sources of tail latency: queues, noise, workloads
  - use CDF offline to reduce tail latecy.

  - if software passes along request IDs, we can use that as a signal for
    optimization later.


insight: long requests reveal themselves parallelism improves tail latency on a
single machine (duh)

The subject matter of this talk is incredibly far away
from what I do.

---

# the design of javascript type systems - dimitris vardoulakis

- several type systems have been proposed for JS. three are being used at scale
  - closure compiler
  - typescript
  - flow


- what requirements drove the development of these systems? what makes them
  unique? what makes them different from java?

- as the web grew in popularity, JS was used to build larger and larger
  systems. JS was missing modules ,types, and classes.
- a type system was added to the ES4 proposal (circa 2006). closure compiler
  added type system in anticipation, bu ES4 didn't take. closure did types in
  comments.

- ES6 added modules and classes. around the same time typescript and flow
  entered public consciousness.

- requirements for js type system:
  - handle native (ie. non-typed) style of programming; don't restrict the
    language too much
  - find many bugs, few false positives
  - allow users to _gradually_ type their code - the system should handle a mix
    of typed and untyped code
  - fast: runs in seconds for whole program, sub-second incremental checking.

- familiar features in JS type systems:
  - single-inheritance classes, multiple-inheritance interfces
    - in contrast to JS's native prototypal inheritance, which the speaker
      deems too dynamic (insofar as it's difficult to type-check at compile
      time)
  - forbid implicit conversions by adding types to primitive operators
  - fixed arity functions - no variadic funcs
    - the type systems under discussion here will let you choose whether to
      warn or throw in the presence of a variadic func
  - walk the line between being too restrictive and being able to find many
    bugs.

## features:

### structural typing: don't specificy how an object is constructed, but define
the  properties that the object should have


### unions and flow-sensitive typing

- allows to check nullability and ability to decide if something is one thing
  or another in code (w/ relative safety)

### unsoundness: the type checker might infer the _wrong_ type for some
expression
- speaker says this is a good choice for JS:
  - some js constructs are very dynamic
  - if people are adding types gradually, we don't have to get a metric tonne
    of warnings for code that has not been annotated with types yet.
  - the type checker needs to be fast (stated previously) and with the dynamism
    of JS, we'd end up having a slow type checker

### sources of unsoundness:

- unknown type (aka "any")
  - should be used sparingly, but necessary as an escape hatch
- array access and out-of-bounds array indices
- lots of possibilities for sources of unsoundness


### how does js type checker work?

- phase 1: whole program analysis, collectypes type defs, creats inheritance
  hierarchy
  - find user-defined classes and interfaces
    - assignments to `this` are useful for classic JS constructor funcs
- 2: typecheck each func in isolation
  - this is accomplished by making a control flow graph and executing each path
  - caveat: doesn't handle aliasing correctly (i.e. assigning entry arg to a
    variable, you won't get the relationship between two variables, rln b/w
    variables is not tracked)

[question: what if we wanted to infer the signature of an unannotated func?]

### evaluating the quality of a js type checker

- if the type cheker is "unsound", what guarantees can it possibly provide?
  - empirical evaluation based on user feedback (i.e. YOU help to decide if the
    type checker is doing a good job. dialogue between implementer and user)
  - it's better to reduce false positive even if it means finding fewer bugs
- speaker: "soundness is a continuous spectrum, not binary"

### how do we write js that typechecks well?

i.e. how should I write my code right now to help with the introduction of a
type checker later?

1. it is helpful if data definitions can be found for all properties on each
type - one should be able to statically construct the inheritance hierarchy.
2. classes and interfaces should be annotated.
3. variables across scopes (i.e. used across closures, used in 2 or more
functions) should be annotated.
4. function parameters should be annotated.

if your type checker provides a strict mode, it is useful to use it.

### what's next for js types?

the three type systems inform each other:
1. non-nullable types, unions, and type aliases are now in TS
2. structural interfaces are in closure compiler; bounded generics, func
overloading and types coming to closure soon

browsers, notably, don't have types

### there are type systems built on top of other dyn langs

- the same requirements that the speaker laid out for a js type system should
  apply to type systems in other dyn langs
  - and, speaker says, it should follow that the high-level design should be
    transferrable too

---

version control: I would like to keep track of (store) some data, over time.

when we say "some data" we mean files on a computer.

and the speaker says it makes sense to keep track of bytes

people used to use checksums to sum the bytes. but that's super problematic


speaker requires 'digest/sha1' to get a hash of the checksum

speaker says we may as well just use the filesystem for our database.
  - filenames are keys, contents of files are the values.
  - "Blob" - binary large objects - just big bins of bytes

speaker uses zlip to "deflate" bytes before saving them
  - should we get the SHA of the compressed bytes of the uncompressed bytes?
    - compressed bytes: really easy to check integrity of stuff, but if we
      change compression algorithm, everyone's versions everywhere will all be
      lost.
    - so we hash on get uncompressed bytes, then compressed and store bytes.
  - another question: what's the minimum amount of information to store per
    directory?
    - answer: just ask the filesystem
  - question: should we include permissions?
    - seems very questionable. we want to know the kind of file we have (i.e.
      symlink/dir/file). we'll keep the permissions around for now but throw
      away the info other than the kind of file.
  - question: what separators should we use?
    - space for things like numbers, `\0` otherwise
  - back to permissions: speaker changes modes of files to be uniform after
    reading out the file type info mentioned above
- speaker wants to represent filenames as bytes, not shas. it looks longer, but
  is actually shorter (half the length)

now that we have the ability to keep track of data, how can we get histories?
we don't have that yet.

  - we want: commit message, author, timestamp, and the merger (potentially
    distinct from the author)
  - now that we have these, we have a system like Monotone (a vcs that isn't
    super popular)
  - why bytes instead of files? Ralph Merkle's doctoral thesis which includes
    many fun things to do with hashes, including content-addressable storage

git's major innovation: use a cache, keep a single list saved to file (the git
index)
  - this solved speed problem, but also provided a working area where we can
    choose to (or not to) actually commit stuff

---

# it me: under the hood of web authentication

people think web authentication is a solved problem - you just plug in a
library and go.  in reality, there are problems in lots of web authenticatoin
libraries

## passport

passport has many authentication strategies available. the idea is that the
strategy decides how to implement authentication.

passport-local example:
  - don't store user passwords in plaintext (becaues we don't want to expose
    user passwords if we lose db)
  - so we salt passwords. good practice
  - there's another problem user.password != password is not a timing-safe
    string comparison

dbs get exposed all of the time - adobe got hacked in 2013 and the passwords
were encrypted, which isn't as good as salt+hash. ~150 million passwords
  - encryption is invertible. if you have the secret key and the cipher text,
    you can decrypt the data
    - compare to hashing. it's a one-way function, not inverible. salting
      prevents statistical analysis of the output in the database.
    - from the adobe example, over 2 million users used the same password
      - lol the leaked passwords from adobe db contained password hints

session management in http
- as a developer, you have some kind of session abstraction available to you
  provided by web framework.
  - two kinds of web sessions:
    - stateless sessions: all of session data is sent to client. flask and jwt
      do this
    - server-side sessions: store session data on server, just send a session
      ID to client (e.g. django)
  - session data, whichever type, is persisted on client, usually in an http
    cookie
    - cookies are wild. they've gotten a little better with stuff like
      'httponly' flag, and 'secure' flag
    - people also use localStorage for the same thing.
  - as we know, session data goes to server with every request
  - one problem: session data could be manipulated by the user
    - people use message authentication code (MACs) to get around this
  - another problem: sessions are signed, but not encrypted.
    - this doesn't often matter, but sometimes you don't want this session data
      to be read by anyone other than the server

back to timing-safe string comparison:
  - the time that it takes to run and equality function varies depending on how
    similar the two things being compared are.
  - if the attacker can measure the timing, they can find out how long the
    desired password could be (longer times mean it's closer to a match). this
    can vastly narrow the surface area that they'd need to guess on. in fact,
    they can get to a complete match this way
    - 2007 study says that you can get quite good accuracy even over the
      network.
  - solution: do a "constant time comparison"
    - xor the result and append the difference to what is returned if there's
      an error
    - node added `crypto.timingSafeEqual` which solves the problem. the code is
      slower but safer.
      - but one problem is that a JIT can optimize so that you lose the safety
        (it gets optimized away)
      - there's a way to "blind" the function to prevent the problem.

beyond passwords: users just use weak passwords.
  - password managers are awesome, but not a great solution fo rthe majority of
    people.
  - what if your password mgr is compromised?
  - SMS one-time passwords: nice idea, but if compromised, no good.
  - hmac one-time passwords: hardware token-based passwords

one of the speakers: u2f is a good thing
  - u2f is a sescure element that secures secret data on your behalf
  - challenge/response protocol
  - there's a tradeoff: you'd need buy-in from device mfgs, websites, and
    browsers to all use it
    - the devices are cheap and widely mfged
      - mobile devices (namely ios) don't support it yet though, so the
        separate device is currently necessary
      - people have made so-called soft-tokens  (e.g. github's softu2f)
    - browsers: chrome has it, just landed in firefox nightly
    - websites: list of sites that use it available at dongleauth.info

password-free login systems:
  - passwords are basically unusable by the everyman
    - 5% of americans use a password manager
  - 2fa is an improvement why not use the second factor as the primary way to
    log in?
    - yahoo tried this with the Yahoo Account Key - separate app on a mobile
      device. proof of authentication by possession.
  - passwords are only as secure as your email

is email secure in transit? no...
  - there's an expectation of reliability in email - email providers will send
    messages even if encryption is not negotiated.
  - but more and more email services encrypt by default

---

# Narrated Reality

## Let's Play a Game: Actual English Word or Invented Gibberish

- some real words, some fake words
- author made a bot called lexiconjure, recurring neural network - @lexiconjure
  or lexiconjure.tumblr.com

- speaker used to be a ghost writer for obama as a result of his connection to
  noam chomsky
  - ended up doing astroturfing, which was unscrupulous and morally
    reprehensible
    - it paid well because he found a way to cheat.
      - "horizontal writing" - let him do eight hours of work in about two
        hours
      - writing algorithms are actually not as new as speaker previously
        thought - 50 or more years old
        - aka computational creative writing
- screenplays only describe things that can be seen - never discuss inner
  thoughts of the actors
  - applied to computer-generated text, interesting results
  - the speaker trained models on poetry and prose and generated more results.
  - terrifically strange, surreal
- subscene - every subtitle from every show/movie you can think of, built by
  deaf community. great training data for neural networks

---

# rebuilding the cathedral - nadia eghbal

- speaker comes as an outsider, which has been helpful
- challenging her thinking around what open source can be - underlying
  assumptions.

- if you've ever tried to make an OSS contribution, you've had the xperience "I
  know how to fix this"
  - but then you wait for a merge...and forget about it
    - revisit the issue, why did the contribution get lost?
      - it's possible that the maintainer is fighting to maintain their sanity
      - "the more successful you are, the more you get punished with github
        notifications"
        - "efficiency breeds more work" - why does this happen?

- sourceforge only had 208k users (2001), github has 20m users (2017) - OSS is
  more popular than ever before
- 180k downloads of netscape , 21 million lodash downloads (two weeks)
- compounding the problem further: the percentage of surveyed companies who say
  they use OSS has doubled over the last few years
- equifax blamed OSS for one of their flaws

- notion of "keystone species" - a maintainer is the keyspone species of
  software development
  - keystone species such as a wolf - this species doesn't have a huge
    population, but it has effects on other populations that allow the
    ecosystem to survive

Curiously, users aren't becoming contributors and definitely not maintainers.
Why?
  - why do we believe that users will become contributors and contributors will
    become maintainers?

e. raymond rejected the idea of cathedral-style software. instead, embraced the
bazaar.
  - speaker argues that the bazaar model was the right argument for the time,
    but it doesn't seem to fit reality
  - now that open source is "cool", what do we do?
  - "there is no bazaar, there are just a few people maintaining a bazaar-sized
    workload"

back to keystone species metaphor: maintainers are the polar bears of open
source. they have big problems on the horizon.

  problem: many new developers today.
    - one aspect of this: 80% of contributors don't know how to resolve a merge
      conflict

software is actually getting smaller; many microcosms

less than 3% of widely-used projects are bazaars - most popular projects have
had fewer than 10 contributors _ever_. keep in mind that 50% of contributors
only contribute once, then never again, less than 2% of commits from these
people.

note: the vast majority of one-time contributors don't want to contribute
again; they just want to fix a problem and move on.

what does it cost to pursue the wrong strategy? what if we didn't have to ask
maintainers to choose between onboarding new contributors and writing code?

and how do we support maintainers?
  - indirect: encourage companies to allow employees to work on oss during the
    day. speaker sites example of zhu at behance and opensourcefriday.com
  - : raise money for open source work - consider permissive licenses with
    commercial plans available. also sponsorships via kickstarter, patreon,
    opencollective
  - infrastructure: treat OSS lke digital infrastructure. if we treat it like
    infra, we could fund it well. some companies have made bils off of OSS
    infra, they could take the carnegies and rockefellers as an example for how
    they bootstrapped the nonprofit infra. think about roads. roads are infra
    that needed work and maintainers (civil engineers, who were often burned
    out by local politics)
    - if we can quantify the value of open source, we will have a better chance
      at communicating the value of the work of maintainers

the ecosystem must evolve in order to survive - the old model needs rework!

---

# observability for emerging infra: the future of observability in complex systems

observability for next generation

monitoring: hasn't changed in 20 years. most companies run it in house.
visibiity is a specialized skillset: understanding what's going on in your
software is incredibly important.

speaker hates monitoring but loves debugging.

(honeycomb: not a monitoring company)

infrastructure has changed enough that we cannot rely on the same old
assumptions about where we'll be able to monitor our code.

observability: term taken from "control theory" - in a nutshell, "how much can
you understand about how your code works by looking at the code's output?
(where you've placed the relevant traces)"

observability is a superset of monitoring and instrumentation.
  - monitoring: how you operate a service. actionable checks, outputs without
    parameters, alerts, and dashboards.
  - instrumentation: how you develop a service to be monitorable and
    observable. timers, gauges, counters, events.

observability is about how you track operational failures and programmatic
bugs. is your system debuggable and observable? can you ask the system the
questions that you need the answers to without novel and complex incantations
each time you need to ask these questions?

speaker compares complexity from LAMP stack (2005) to Parse's stack in 2015)
  - the decade has made things way way more complex
    - they built a system that was fundamentally un-debuggable (or at least
      untenable in terms of the amount of energy necessary to debug a single
      problem - days, weeks for a single bug)
    - the electrical grid of the USA as an example of a complex system that we
      should strive to become (large and complex but also understandable)

speaker describes lots of bugs that aren't problems - instead, they're
symptoms.
  - reacting to _symptoms_ as though they're _the_ problems is not useful.
  - facebook moving parse onto scuba was incredibly helpful for the speaker.

the unknown-unknowns are the things that you'll be dealing with for the rest of
your life because system complexity is increasing so very much.

the health of the system is usually irrelevant. the health of each indivudal
request is of supreeeme consequence.

the hardest problem is finding which components to debug or trace, and the
system is not possible to model in your head.

aspects:

instrumentation: "we want distributed strace." - start at the "edge" (i.e.
network edge) and work your way down

events, not metrics: speaker says we want to track events instead. metrics
cannot be traced, which isn't great (no context).
  - don't want to trawl through a tremendous number of things to find the thing
    that's meaningful
  - high cardinality is a must-have, not a nice-to-have

structured data: if you're looking for a needle in a haystack, you'll need your
pieces of data to have meaningful names.

events: events tell stories (as opposed to metrics)

dashboards: the speaker hates them - "every dashboard is an artifact of a past
failure"
  - starting with answers instead of questions, which is bad.
  - instead, you want to interrogate your data to _find_ the answer.

don't aggregate, use raw requests.
  - if you do write-time aggregate of requests, you're killing details
  - black swan events are always outliers, you cannot find these if you're
    stripping the data
  - "slow query log"

users don't care what the "system" health is. nines don't matter if users are
unhappy.

test in production (as well as before production). staging ... maybe doesn't
matter?

speaker: "the future belongs to generalist software engineers."
  - "you cannot craft well-designed services without understanding how they
    work."
  - "don't hire software engineers who cannot take responsiblity for their
    services."
  - instrumentation is start of building software. watching your software while
    it's running in production is your job, because if you don't know what it
    looks like when it's normal, how will you know what to look for when things
    are going wrong?

"the way to think about observability is to think about distributed systems."
  - dist systems failures will help you to think about how to build
    observability into your systems.

---

# practical applications of the dickerson pyramid

- so what IS the dickerson pyramid?

- dickerson's hierarchy of reliability:
  - monitoring at the bottom
  - then incident response
  - then postmortems
  - then test and release
  - then capacity planning
  - then development
  - then ux

- speaker adds Communication as the facet that underpins all of these levels in
  the hierarchy

- communication fits into monitoring because monitoring only makes sense if
  you're monitoring your business needs.

- speaker: "grow monitoring to match your business needs."

now that we have monitoring, we can handle incident response.
  - incident response is two categories:
    - alerting - who and how quickly they must respond
    - inident communication - where is communication done, and who is in charge
      of the incident response? (i.e. first person who shows up is in charge
      until they delegate to someone else)
      - what constitutes an "outage", what's worth waking up for versus waiting
        until first thing in the morning?

incident response against noise:
  - hillary for america (one of speaker's previous employers) decided to redo
    all alerting - lots of their alerts were for issues that the org couldn't
    actually do anything about.
  - speaker's team created a document to help them evaluate alerts
  - this helped them get their pages from being 30% actionable alerts to 85%
    actionable alerts

postmortems: you want to communicate for future reference - comunication with
future people who you haven't met yet and may never work with.
  - can customer service team understand it?
  - can engineers who don't always work on the service understand it?
  - can your boss understand it?

  - chains of outages and repeat incidents in google - they started doing
    cross-subteam postportems.

testing + releasing: speaker's favorite place for automation
  - highly dependent on what the team's process is - so much room for
    differences between teams.
  - interesting question: "can the team release without an outage?"

capacity planning: a plan for the infrastructure's growth
  - this is easy to ignore given that capacity is generally only a click away,
    but dear lord can it be expensive if you aren't thinking about it. the
    communication here is really important because the business may have
    insight into expected growth (and should have insight into the budget for a
    piece of infrastructure)

development: how do you make it so that you're not doing the same exact thing
in six months?
  - interesting communication question: what is worth building internally
    versus using an externally-created solution for?
  - "have we fixed the lower layers enough that we can invest time on this?"
  - "are the things that are bothering you _actually_ bothering the company?"
    - speaker has had the situation where he thinks things are much larger
      problems than other people. "do we need to solve this _right now_?"

ux: so how are _users_ affected by reliability?
  - e.g. is the response fast enough?
    - _where_ are users experiencing your product? new zealand or virginia?
    - if it's just in US (or any particular region), you can follow the US
      diurnal cycle for some gains
  - can we have outages in a way that customers don't notice?

---

# how to _be_ a compiler - mariko kosaka

- as we already know, mariko's biggest hobbies are coding and knitting
  (glossing over this because although it's very interesting, I'm coding
  something else and I saw her do a talk on this two years ago :))

## an alternative introduction to building a compiler

- as a knitter, how would I build a compiler?
- step-by-step procedural explanation of knitting (and how it's like
  programming. kosamari is sooo tired of hearing about this)
  - knit: pull the yarn from the back of the loop into a heart-like shape chain
  - purl: push the yan from the back, creates a bumpy look
  - the combination of these two create beautiful knit patterns

- speaker: abstraction  is a tool, not magic. you must learn why you'd want to
  use it in order to make it powerful.

- speaker listened to a podcast interview by rui ueyama "how I wrote a
  self-hosting c compiler in 40 days"
  - rui ueyama just started by creating string expressions and implementing the
    spec
  - "maybe I can just start doing it."
  - speaker found book 'Design By Numbers' by John Maeda; DBN (the platform),
    apparently, was the precursor to Processing. this book goes through
    programming constructs very granularly from an outsider's perspective.
  - speaker was inspired to take the concepts in the DBN book and convert them
    to SVG

steps:

1. tokenization (lexical analysis)
2. parsing
  - create abstrct syntax tree (which is a map of your code)
3. transform into new lang
4. generate code (turn AST into non-abstract code)


takeaways:

1. it's ok to have unfamiliar things
2. be a good parser: don't write error messages that are hard to follow.
   pointers to the resource that might be helpful are useful to people who end
   up in error states.
3. context is everything. if you're good at transforming context for people,
   you can solve big problems, communication-wise and otherwise. (much like a
   compiler).

---

# scaling streaming data quality at twitter

goal: be grand central station for twitter.

- team known for the quality of data (at gnip, purchased by twitter)

testing/ops folks: quality engineering. as the team grew, their
responsibilities changed.

quality bar wasn't where they wanted it to be - they had outcomes where things
weren't where they wanted them to be.

what does QE do? write tests that behave adversarily to what the products are
supposed to do, and they use those tests to write guardrails for those things.
  - they don't just "ship stuff and see what happens"

soak period: see what will happen in production before stuff gets there.


end-to-end testing takes place between services
  - feature testing is different. e2e isn't a replacement for feature testing.
    - e2e is primarily for finding live interaction issues

powertrack: stream of all realtime twitter data (firehouse) gets injected,
applies custom ruleset to get _just_ the tweets, consumes them...and then
something?

book: polya's "how to solve it"
  - it's easier to solve problems in pieces than attacking the whole thing head
    on. sometimes go to first principles.

venn: nowledge of entire system; knowledge of testing tool; overlap: "how all
of the things actually work"

large systems are dynamic, complex - might have behavior is that correct but
undocumented because, perhaps, it is emergent.

see "The Limits of Correctness" paper by Brian Cantwell Smith

Sarah Mei: goddess of feature testing, says:
  - "Good tests don't just prove that code works". in addition, they:
    - prevent future regressions
    - document expected system behavior
    - provide usage guidance
    - allow for refactoring

---

# civic hacking: how do we patch the government?

- inequality is a bug. it's a **hard** problem.
- some: why is the system so broken?
  - others: the system works on my machine.
  - this is inequality. why should different people have a different user
    experience?
- what makes this hard? blockers:
  - the emotions of decision-makers: considering that there's a different user
    experience for different populations can be triggering and shut down the
    process for people.
  - unconscious bias: are we trained to think about the problem(s) a certain
    way?
  - lack of direction: big problems (like social inequality) are really hard to
    cut through

## we need a series of decisions to be made to help us get through these blockers.

- so in a nutshell this is a decision problem.


## intuition: tech _could_ help us to address inequality.

## hypothesis: tech enables oppressed populs to verify solutions to inequality in reasonable time.

instances of inequality:
- disparities of treatment by police (var t)
- internet access (var a)
- income disparity (var i)

- np complexity: deicision problems where 'yes' answers can be verified in a
  computationally deterministic manner (i.e. we can check our work quickly with
  code)
  - hypothesis all instances of inequality are in the set of NP

to solve in equality:

- speaker draws boundaries over earth...
  - everyone submits data for a chart

use tech as a feedback loop to see if problem is being solved

axiom of choice: we might start with income inequality because it's the
simplest to solve. but we can't use the same effort to solve some of these
other problems. What would we do in those cases? Different people can solve
different problems.

"How do we write unit tests for this?"
