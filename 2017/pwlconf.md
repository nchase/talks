# Decoding The Sensuality in Music - Elaine Chew

---

# Music is really important. It 'moves' us

- there has been an explosion in 'digital music information' and
  "musical-information retrieval"
  - i.e. most music is distributed digitally now. The industry itself is
    digital.
  - We now have the means (and the impetus) to understand how humans understand
    music.

- Music creates emotions through how it handles expectations.
  - depending on whether or not or _how_ we fulfill expectations, we might
    evoke different emotions.
  - prominence (i.e. of certain notes), contrast, time, tension are all
    dimensions that we can affect to change how the listener might feel.

- she plays piano piece but then talks about the cello part for that - you can
  use vibrato technique on a cello. this changes the way the piece is
  interpreted by the listener (the emotions that the piece will evoke)

- portamenti controls the dimension along which a pitch changes

- representing tonality: tonality has this "wrap-around" quality that maps to a
  Spiral Array.

  (after this, the presenter showed some visual examples of how the spiral
  array maps to music by plugging in a midi keyboard)

- the "interesting" parts of music occur when something unexpected happens
  (i.e. a change)

- "sweet anticipation - music and the psychology of expectation" - she likes
  this text

- Mathematical and Computational Modeling of Tonality - a book on the topic

- Tonal Contrast - how can we use tonal contrast to produce interest (as
  opposed to just discordance?)

- Time: the use of time creates moments of anticipation and suspense
  - lingering notes - "What is the next thing coming? WHEN is the next thing
    coming?"
  - this maps to physical tipping points! which are a function of gravity
  - time is physics - tensile strain, momentum, diameter (space)
    - in games, if you expose a way to vary these aspects, you can evoke
      whichever emotions you're going for.


---

# My History with Papers - James Long

- James doesn't consider himself a papers-reader
  - In spite of this, he loves them because they've helped him in his career
  - Great to be able to take papers and "pull them up" into real-world
    applications
  - Delivered him a deeper insight into _computing_

---

## "College James" and Papers

- Paper 1: Studied techniques for real-time rendering of the sky and wrote a
  paper
  - Math heavy, which didn't mesh well with his compsci class
  - It built on a paper from ATI (hoffman and Peetham on rendering outdoor
    light scattering in real time)
    - Digested the paper, was able to render stuff
    - paper was from 2002? and then in 2006 when he wrote _his_ paper, shaders
      were starting to come into use in industry, so it was very timely
  - (short diversion into why the sky is blue and how light scattering in the
    atmosphere works to make the eye perceive blue)
  - implementation basically models how the sky's blueness works, via shaders
  - algorithm for "outscattering": initial color, attenuated, over distance,
    creates an out-scattering effect.
    - implement in shader lang - given distance and initial color, calculate
      the outscattering.
  - algorithm for outscattering: somewhat more complicated (so omitted), but
    similar concept
  - ultimately, the paper doesn't do either of these, it suggests very simple
    equations that can be implemented in shader lang to make this happen
  - Speaker: mind blown, and the value of seeing the eqns in the conlusion made
    everything so approachable
    - Translating ideas expressed in math into code is hard work, but
      incredibly powerful and valuable.
    - Value outcomes: don't give up even if you don't understand

---

- Paper 2: "incremental approach to compiled construction (2006)"
  - Speaker was intimidated by compilers, but was interested.
    - Paper author wrote a compiler in assembly, written in its own compiler
      language (with a bit of C)
      - Paper reminded Speaker of "Growing a Language" by Guy Steele
      - First step in paper: write a compiler for a lang that returns a
        fixed-size integer. steps:
        1. write a c-func that returns an integer
        2. compile this and look at the asm code produced to to learn how the
        hell asm works. Congrats
           - Trivial, but makes things like compilrs feel more approachable
           - Speaker: it's all about finding which part of the paper to connect
             with.
      - [Speaker walks through the procedure of building up the primitives
        necessary to build a compiler in asm]
        - e.g. to implement local variables, you have to pass an "environment"
          around with the stack and put variables in that environment where
          necessary
        - heap allocation: pass one large array into the entrypoint, put
          pointers into the heap as necessary. Later you can come in and
          improve the impl
        - on and on: 24 different steps overall, toward SELF-HOSTING: the lang
          you implement becomes powerful enough to run the compiler itself
  - Speaker learned how to build things using raw bytes. Building closers
    helped speaker to understand and appreciate how closures work (and the cost
    of closures at runtime)
    - When faced with a complex problem that he doesn't understand, he tries to
      find the source of how it works; just writing stuff to try to find out
      what he doesn't understand
    - helped him to write an interactive js debugger in the browser
      - "what can I write this thing in that'll let me write a JS program in
        the browser that'll get interpreted properly?"
        - original attempt was ... pretty wild
        - ultimately found continuations/continuation-passing style implemented
          in JS (paper)
          - w/ continuations: you either optimize for continuations
            (continuations always available, but all code is a bit slow) or
            optimize for normal code (fast generally, continuatinos are very
            expensive)
          - JS exceptions are used to make the whole thing work. everything is
            a try-catch, and in the catch, if we're in a continuationexception,
            copy everything to a new "frame"
            - problem with closures: needed to "unbox" variables to make this
              all work nicely (paper describes this solution)
          - This could have been slow, but the JIT made it fast enough that he
            could build a real-time webgl demo with stepthrough capabilities at
            60fps
          - "Delimited Continuations are the best abstractions"
          - 'unwinder' on github
          - This whole exercise taught the speaker the power of continuations

---

- Paper 3: A Prettier Printer (Philip Wadler)
- Speaker wanted a JS formatter that printed _real code_
  - found recast, which was a good start. syntax tree transformer and
    pretty-printer
    - forked and learned from it
    - then found above-named paper. written in haskell, which was unfamiliar,
      but digestible


---

# What Type of Thing is a Type? - Ron Garcia

Speaker has a love-and-hate relationship with Types.

There are multiple ways to look at Types. Here are two w/r/t type discipline:
1. discipline is good and makes you strong
2. discipline keeps you from doing wrong and makes you sad.

"the types will guide your programming, thinking in types helps you program
well" - case #1

Speaker first began to appreciate types in Racket - "How to program in types
without a type-checker"

This talk: "The Theoreticians point of view into how types work"

References: Types are not sets (1973 morris), a theory of type polymorism in
programming (1978 milner)

Types: syntax as symbols and symbol pushing. syumbol pushing is something that
computers are really good at. in programming, we're building symbolic
representations of ideas and computing on them. Syntax as symbols (as opposed
to stylistic quibbles)

Semantics: meaning.

What's the relationship between syntax and semantics?

Pragmatics: what do we need to do to get the job done? (as a theoretician as
opposed to a practitioner)


Back to papers: 1978 Milner's theory of type polymorphism in programming.
Milner did work on theorem proving, which begat ML programming Language.
Milner didn't have a Ph.D! Won turing award 1991.

In the community the paper is the hindley milner type inference paper.

The paper coalecesed conceptual blueprint for how theorists think about types

[speaker shows a language: abstract ML]

Syntax: just a data type

Semantics: what I can _do_ with a given data type.

[Speaker mentions that the stuff in this paper which is hard to read is super
similar to how we'd write this stuff in scheme, just needs to be translated.]

ML has some text that says "wrong" wherein you'd be checking to see if the
previous output was correct. Types omit the need for these checks - makes it
impossible to have certain kinds of errors.

Author separates types into two different kinds of types:
  1. monotypes: types that you'd see in any lang (i.e. integer, boolean)
  2. polytypes: things like type variables: types composed of types, types that
  transform into other, specific types (e.g. through functions)

Speaker notes that author gives _semantics_ about these types.
- throw in expressions, values come out. first, let's give meaning to the
  values that pop out.
  - Why value-first? because the values that come out of the prorgrams, the
    author asserts, is the meaning of the program, and the meaning is the most
    important part.
- Any non-terminating computation has any type (any infinite computation has
  any type - if you're always computing, how could you give it a defined type?
  could be anything.)
- If a function takes a value and produces a value of a certain type, the
  function itself has that same type.

- What about polytypes? If every concrete type produces a certain
  transformation (behaviorally), the polytype can be defined to have that
  behavior.

- expressions have types. the author doesn't dig into this too.

- what does it mean for a value-environment to respect a type-environment?

- types allow you to limit the solution space of an output through a type
  checker.


semantic type system: checks based on _behavior_ of a system syntactic system:
checks based on the _???_ of a system

- reasoning about the behavior of the code by reasoning about small pieces and
  thinking about what happens whey're put together (modually, then
  compositionally)

every proof about type assignment says something meaningfully about my code

the paper boils down to the theorem "well-typed programs don't go wrong." very
tasty.

"how do I build the data structure that says something about my code?" - that's
type-checking.

type-checker finds a tree that corresponds to the given piece of code.

Back to the start: What are types?
  programming langs have: syntax, semantics, and paragmatics
  types have: syntax, semantics, and pragmatics

So type systems are languages for talking about _properties_ of programs and
program fragments

--- 

programming data for display: the pdf story

there have been many ways to display data through history:
- telegraph/teletype
- dynamic display i.e. crt/oscillascope/raster display(lcd)
- printers
- cad/cam (what is this?)

page description language: any characterization of the layout and contents of a
page that is more efficient or expressive than a visually equivalent bitmap of
that page. example: LaTeX or PDF, but there are MANY other examples (author
notes 40+).

## ascii: the first page description language
the first 32 control chaactters make it possible to force a teletype to render a set of data in a way that is visually pleasing, practically consumable, and so forth.

## vector graphics were used in air traffic control systems (and we know them more intimately for other uses)

## PDF came to be through Evans and Sutherland 

PostScript and Interpress

- internally-developed page description language
- prior to Interpress (Xerox), a different page description language was written for
  every printer
- adobe invented postscript and eventually pdf
- PostScript and Interpress: a comparison (from a newsgroup)
  - there aren't many papers on these technologies (probably because this
    innovation happened in corps instead of in academic environments)
- camelot eventually became pdf. pdf was driven by fax machines - the data we were trying to send over fax was way too big for fax capabilities. I had no idea that fax and pdf were interrelated

- postscript is a stack-based interpreted language
  - embeds bitmaps, references fonts externally
- pdf is built on top of postscript...holy smokes
