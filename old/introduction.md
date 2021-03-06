
Introduction
============

Scientific Software 
-------------------

Modern science relies on computation.  The insight gained from scientific simulations and data analysis have elevated it along theory and experiment as the "third pillar of science."  An increasing number of research scientists approach their problems with computers.  Their solutions range from small data analysis scripts in wet-labs to large multidisciplinary high performance codes.  Due to the novelty of scientific research these programs are often custom built and encode a great deal of specialized scientific expertise.

The average scientific researcher is not trained as a software engineer.  As a result the cost of building performant scientific codes is high, requiring many hours of labor from highly specialized workers.  Because scientists are rarely incentivized to produce generally applicable code that spans domains their work is rarely reusable, greatly diminishing its social value.  High performance standards, a tradition of monolithic non-reusable solutions, and sparse training creates a situation where highly trained research scientists spend much of their time overcoming difficult tasks for which they are ill-suited.  Society loses productive hours from some of its most highly trained members.

The rise of multidisciplinary science and heterogeneous hardware compounds this problem.  A single scientific project may require expertise from several scientific, mathematical, and computational domains.  Performance requirements may constrain solutions to high quality approaches within each domain, requiring monolithic software developers to develop substantial expertise far outside of their original field of training.  Scientists spend less time doing science and more time learning and practicing foreign skills.  Monolithic scientific software ecosystems do not scale well and present substantial opportunities for efficiency gains.

Expertise
---------

In this section we discuss expertise in a single field.  We use integration by numeric quadrature as a running example.

*The distribution of expertise with a particular domain is highly skewed*.  Many practitioners understand naive solutions while very few understand the most mature solutions.  In scientific and numerical domains mature solutions may require years of dedicated study.  For example the rectangle and trapezoid rules are taught in introductory college calculus classes to a large group of students.  Advanced techniques such as sparse grids and finite elements are substantially less well known.

*High expertise solutions can greatly improve performance*.  The cost of naive and mature solutions can vary by several orders of magnitude.  It is common for a previously intractible problem to be made trivial by engaging the correct method.  In quadrature for example adaptivity, higher order methods, and sparsity can each supply performance improvements of several orders of magnitude.

*Scientific computing problems touch many domains*.  A computational approach to a single research question may easily involve several scientific, mathematical, and computaitonal domains of expertise.  For example numerical weather prediction touches on meteorology, oceanography, statistics, partial differential equations, distributed linear algebra, and high performance array computation.

*The number of scientific problems that engage a particular domain generally exceeds the number of experts*.  E.g. far more questions use integration than there are experts in numerical integration.

Therefore we need to efficiently share, distribute, and interconnect expertise.

*A single domain may be used by a wide set of projects; this set is rarely known by the domain expert*.  E.g. numerical integration is used in several fields which are unfamilar to numerical analysts.

An ideal software ecosystem selects and distributes the best implementation of a particular domain to all relevant problems.  Multiple implementations of a domain in stable co-existence is a symptom of a poorly functioning ecosystem.  It is a sign of poor reuse and fragments future development.


Modularity
----------

We consider the entire scientific software ecosystem as a single endeavor from the software design perspective and make the following observations

*   It contains incredible solutions to hard subproblems
*   It contains numerous medicore solutions to those same subproblems
*   Both good and bad solutions to different subproblems are woven into common codebases
*   A few common patterns recur very often

Software design principles advocate that such a project be refactored so that it is modular and composable.  Ideally this allows high quality solutions to migrate more freely and interoperate in a broader range of applications.

Modularity encourages the separation of code into multiple distinct pieces.  Ideally these pieces are split as finely as meaningfully possible so that each encodes exactly one area of expertise and each area of expertise is expressed in exactly one piece.  Composability encourages the development of standard interfaces to enable the independent connection of these pieces to form larger programs.

Modular and composable software possess the following virtues

*   Individual pieces are simpler to write, inspect, and verify in isolation
*   Alternative pieces may be more readily compared
*   Small pieces are more reusable and ensure longer-term applicability
*   Collaborative efforts require less inter-piece communication

Modular and composable pieces possess the following vices
    
*   They require ahead-of-time coordination on interfaces
*   Inter-piece communication must propagate through a restrictive interface
*   A critical mass of components must be developped before end-to-end solutions are feasible

In a scientific context modular design encourages growth and reuse at the cost of tight integration between domains.  Historically scientific computing was a practice of a few highly trained numerical analysts who pushed maximum performance out of specialized hardware.  In this context modular design inhibits high level information from influencing low-level design decisions, substantially limiting performance.  High performance remains a priority today but the problems and hardware have both grown in complexity and the distribution of skills of scientific programmers has broadened substantialy.  As the problem complexity exceeds the capacity of individual researchers expertise the benefits of modular design begin to outweigh the performance drawbacks.

*Increasing complexity in scientific computing motivates the increased use of modularity and composability in scientific software.  Substantial unclaimed efficiencies exist in both programmer and execution time across a wide range of problem scales.*


The Existing Scientific Software Stack
--------------------------------------

Traditional solutions to modularity include libraries, domain specific languages, and compilers.  These allow the work of a few expert developers to affect a broad base of domain scientists.  However as variety among hardware increases these solutions sometimes fail to adapt.  As projects become more interdisciplinary these solutions sometimes fail to interoperate.  The rising importance of interdisciplinary work and a changing hardware landscape pressure us to refactor our current ecosystem.

### Libraries

*Example: BLAS/LAPACK*

High performance libraries like BLAS/LAPACK and PETSc provide a set of performant functions for numeric computing within a general purpose programming language.  As the numerics community develops novel methods the implementations of these libraries may be modified while maintaining a consistent interface for downstream users.  This buffering effect may break down under large changes in mathematical methods or hardware architecture.  Library developers may be pressured to change their interface by adding, deprecating or modifying funciton headers.  These inconsistencies cause friction and a lack of confidence downstream.

Well established and dependable interfaces like BLAS/LAPACK are valuable.  Confidence in the interface promotes synergistic development on both sides.  The original implementation has been modified and even completely reimplemented by several groups using a variety of disparate techniques (hand-tuning, automated-tuning, distributed computation).  Users of the interface can pick and choose their implementation and be relatively secure that they will be well supported into the future.  As new hardware comes online (e.g. GPGPU) that hardware community rapidly develops a BLAS/LAPACK implementation (cuBLAS/CULA/MAGMA) so their hardware is immediately relevant to existing scientific problems.

Libraries like BLAS/LAPACK form an excellent high-level interface to low-level computational hardware.  

Libraries have two important limitations.  First, they are statically compiled and all intelligence must be applied at runtime.  Second, libraries generally only target the lowest level of the stack of computational abstractions.  Some domains in scientific computing are purely high-level.  Compiled libraries are not appropriate for these situations.

### Languages and Compilers

Programming languages enable the precise definition of a computational problem.  The structure of the language determine the scope of easily describable problems.  Compilers transform examples of one language into another.  Along the way they may inspect the code and apply known simplifications.  For example `C` is a common language for low-level computation and explicit memory management.  `gcc` transforms `C` into machine code for a particular machine.  Along the way it performs optimizations like constant folding, common subexpression manipulation, etc....

This notion of languages and compilers extends beyond common compilers like `gcc`.  For example `f2c` transforms Fortran programs into C programs, and ADOL-C transforms C programs into other C programs which now additionally compute derivatives.

This thesis approaches the modular scientific computing problem through sets of languages and compilers.  


### Traditional Domain Specific Languages

*Example: FEniCS*

Domain Specific Languages traditionally form very high level interfaces appropriate for scientific users.  They are often associated to compilers that encode knowledge from the domain and transform the high-level input to a low-level language.  For example projects like FEniCS transform a very high level description of partial differential equations (PDEs) into performant low-level C++ code suitable for execution on distributed hardware. This approach has the following benefits

*   Domain specific simplification may be encoded into the compiler
*   The input language may be specialized to the target audience, increasing accessibility to communities unfamiliar with performant general purpose languages.
*   DSLs are traditionally more complete end-to-end packages

In the context of this thesis traditional DSLs exhibit the following design flaws.  

*   They are specific to a single community of end-users
*   Their inputs are often terminal in design, intended for use by humans but not for composition with other packages
*   They often specify a fixed set of computational steps
*   Their construction requires domain experts capable of language design

This combination limits the long term utility of traditional DSLs.  Explicit dependence on multiple stages of computation exposes them to changes in computational methods/architecture.  The specialization to a small community reduces the developer base available to adapt the code.  

### Intermediate Languages

*Example: Matrix Algebra*

While libraries traditionally target low-level architecture and DSLs traditionally source from high-level descriptions there is little development of isolated packages that strictly operate as intermediaries.  Yet many broadly applicable domains of expertise lie below domain science and above computational hardware.  

For example symbolic matrix algebra is a mathematical domain used by several high-level scientific domains and is influential in many low-level computational methods.  Matrix algebra has a clean theory amenable to a automation yet is reimplmeneted by a substantial fraction of scientific packages.

Include scicomp.stackexchange example?


### Side Transformations

*Example: Automatic Differentiation*

The top-down view of a sequence of transformations from high to low level is incomplete.  Domains like statistical uncertainty, differentiation, and distributed computing all provide transformations that start and end within the same intermediate representation. 

Automatic differentiation (AD) transforms a source code into a target that computes the derivative of the source.  Both the source and target codes are in the same language.  Automatic differentiation has found tremendous use in the computational community specifically due to its composability.  Source to source AD compilers operate on mature codes that were not written with AD in mind.  It allows research scientists to reuse previously developed code to develop novel solutions to important problems today.


Proposed Design
---------------

### Short and Wide Domain Specific Languages

This thesis focuses around DSLs that are *short* and *wide* .  These adjectives are explained below.

*   Short v. Long - These are measures of the conceptual distance between source and target languages.  The larger the distance the more sensitive a language is to external change.
*   Wide v. Narrow - These are measures of the applicability of the language across domains.  Wide projects interest a large and diverse population of users.  Narrow projects are more specialized to a small and specific community.

Long and narrow projects are *brittle*.  They are unlikely to adapt to external pressures (like GPGPU.) 

### Simple, Expressive, and Accessible Interfaces 

The composition of independent projects requires common interfaces.  The scientific ecosystem includes a wide variety of domains which exert a wide variety of pressures on the interface.  It is difficult to find a small set of interfaces which maximually satisfy all relevant projects.  We pose the following virtues

*   Simple - Simple interfaces have a small cardinality of types
*   Expressive - Expressive interfaces allow common problems to be described with relatively few terms
*   Accessible - Accessible interfaces use data structures that are familiar to the target community

Example: the UNIX set of tools uses a single "stream of text" interface.  This single interface allows all tools to interoperate.  This interface is 

*   Simple because it has only one data type, string
*   Not expressive in many structured contexts when a substantial amount of parsing/printing must be done to encode complex expressions in different languages
*   Accessible because strings are widely supported and understood by UNIX programmers

Example: JSON is a more structured interface that includes strings, lists, and dicts/maps.  This interface is 

*   Moderately simple because it has a few basic data types
*   More expressive
*   Fairly accessible because these types are widely supported in a variety of languages and converters are ubiquitous.
