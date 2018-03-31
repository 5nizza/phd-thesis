Reactive synthesis:
branching logics and parameterized specifications

Introduction
------------------------------------------------------------------

# What is reactive synthesis? what dream it pursuits?

  - story about the designer and the client:
    [spec -> programmer -> module]

  - instead, RS suggests
    [spec -> module]


# Example of reactive synthesis

  - 'every request should be granted'
    [human-designed arbiter]

  - instead we could state this as a logical formula:
    [G(req -> F g)]
    then push the button and get the same result.


# Reactive synthesis problem

  _given_: specification in some __logic__
  _output_: system of some __kind__ that satisfies the specification

  We play with both ingredients---logic and systems---of the problem.
  But what is the problem with the current?


# Motivation for my research [PART I]

  - Consider the first example:
    [G(r -> F g)]

  - in reality the synthesizer produces:
    [lazy arbiter]

  - two options:
    1. write better specifications
       - but that is hard:
         - improve "user-friendliness": syntactic sugar, etc.
         - __use different language__
    2. don't, but predict the human intention
       and augment the specification (__uses a different language__)


# Motivation for my research [PART II]

  - find a context where developing systems is so difficult
    that writing specifications is worth it

  - e.g. concurrent distributed system

  - in our example: what if we have 10 request channels? or 100?
    [G(r -> F g)]

  - parameterized systems
    - synthesis of parameterized systems via cutoffs


# Bird-eye view of the thesis

  - reactive synthesis requires good specs, but that is hard

  - PART 1 develops new synthesizers for richer existing languages
    - contribution: two new approaches for CTL* synthesis

  - PART 2 studies synthesis of parameterized systems
    - contribution: new cutoffs and a case study


Part 1: approaches to CTL* synthesis
--------------------------------------------------------------------

# What is LTL?

  - machine maps inputs to outputs
    [arbiter]

  - LTL formulas [Phi_LTL] describe infinite computations
    [G (r -> F g)], [G (r -> X g)], [G (g -> g U !r)], [FG(~g)]
    [sys |= (A) G(r -> F g)]


# What is CTL*?

  - machine maps inputs to outputs
    [arbiter]

  - CTL* formulas can also describe 'possibilities', using [A] and [E]
    [E FG g], [E F(g&X~g)], [AGEF ~g]
    [sys |= AGEF~g]


# Why CTL* is useful?

  - recall the lazy arbiter that satisfies [sys |= A(G(r->Fg))]
    [lazy arbiter]

  - to avoid strange arbiters the designer can add (or automatically add) properties like
    [EFG~g], [AGEF~g]

  - fulfills the dream of 'declarative' ('what', not 'how') specifications


# Thesis contribution to CTL* synthesis

  - already known: Safra-full CTL* synthesis [62]

  - new:
    - bounded synthesis approach
    - reduction to LTL


# Bounded synthesis for LTL [SF]

  - encode model checking into SMT
  - synthesis: replace know functions with UFs
  - bounded synthesis [SF]
    [picture]


# Bounded synthesis for CTL* [KB17]

  - introduce new propositions
  - use a modified encoding from bounded synthesis


# Bounded synthesis for CTL* [KB17]

  - bounded synthesis [KB17]
    [almost the same picture]

  - bad at establishing unrealizability
  - requires development of a specialized synthesizer
  - relies on SMTs


# CTL* synthesis via LTL synthesis

  - ...also bad at establishing unrealizability
  - no need for specialized solvers
  - fast in the right contexts


# Idea of the reduction

  - synthesize proofs along the models
    - for each subformula \A\phi or \E\phi, introduce a new output
    - for every subformula, introduce an output that encodes the direction
      along which the formula \phi is satisfied

  - the LTL formula says:
    - [ G(p_af -> f) ]
    - [ G(p_ef & G d_ef -> f) ] 
    - [ p_top ]


# Example of the reduction

  - original CTL* formula
  - translated LTL formula

  [annotated system]


# Properties of the reduction

  - [<=>] reduction
  - size of LTL is 2^CTL*
  - complexity is 2EXPTIME (as before)
  - systems can get larger
  - fast when the E-formulas are small


# PART 1: conclusion

  - two approaches to CTL* synthesis
    - bounded synthesis approach
    - reduction via LTL (bounded synthesis, etc.)

  - possible future work
    - what else can be reduced to LTL synthesis? (rational synthesis?)
    - bounded synthesis for other logics? (hyperproperties? strategy logic?)


PART 2: Synthesis of parameterized systems
------------------------------------------------------------------------

# What is parameterized synthesis?

  - many systems are concurrent
    [picture: several components talking to each other]

  - many systems can be seen as uniform
   <!--straighten the picture components-->

  - such systems are arranged in some communication pattern
   <!--make it token ring-->

  - to specify behaviours of such systems, we can use quantifiers
    [forall i. G(r_i -> F g_i) & forall i!=j. G~(g_i & g_j)]
   <!--add r_i and g_i to the picture-->

  - we want to synthesize such a template


# Parameterized synthesis problem

  input: parameterized specification $\phi$, system architecture
  return: template process such that for any n: $sys(n) |= \phi$

  - already known:
    - huge work on PMCP
    - cutoffs reduction: [forall n>c: system(c) |= spec  <=>  system(n) |= spec]
    - PS is introduced in [BJ] and is undecidable for token rings

  <!--animation-->
  - thesis contribution:
    - optimizations for PS of token-rings
    - AMBA case study
    - guarded systems: new cutoffs (significant extensions of [37])


# Cutoffs [40] for token-rings

  - Process template [picture]
    - fair token-passing

  - Parameterized formula
    - $\forall i.\forall j.... \phi(i,j,...)$

  - Cutoff for parameterized formula and process template P
    is a number c such that: $\forall n \geq c: P^R(c) \models \Phi \Iff P^R(n) \models P^R(n)$.

  Theorem[40]. Consider a process template P and parameterized formula $\Phi$. Then:
  [those graphics]


# Parameterized synthesis [50] (semi-decision)

  Given: param specification \Phi.

  1. calculate the __cutoff__ <!--add animations here: challenges-->
  2. synthesize P^R(c) that satisfies \Phi <!--add animations here-->
     - use Bounded Synthesis to reduce the synthesis to SMT solving
     - if succeeds --> done, if not --> unknown


# Conclusion

  1. we developed two new synthesis approaches for CTL*:
     - using Bounded Synthesis (=> flexible but slow)
     - by reducing to LTL synthesis (often faster)

  2. new cutoffs results and tricks for practical parameterized synthesis
     - token-rings: small cutoff extensions, significant optimizations
     - guarded systems: new cutoffs

  - Future work:
    - further reductions to LTL synthesis
    - synthesis with parameterized data
