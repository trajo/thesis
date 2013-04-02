
Expertise
---------

In this section we discuss the distribution, importance, and demands of expertise.  We use integration by numeric quadrature as a running example.

*The distribution of expertise with a particular domain is highly skewed*.  Many practitioners understand naive solutions while very few understand the most mature solutions.  In scientific and numerical domains mature solutions may require years of dedicated study.  For example the rectangle and trapezoid rules are taught in introductory college calculus classes to a large group of students.  Advanced techniques such as sparse grids and finite elements are substantially less well known.

*High expertise solutions can greatly improve performance*.  The cost of naive and mature solutions can vary by several orders of magnitude.  It is common for a previously intractible problem to be made trivial by engaging the correct method.  In quadrature for example adaptivity, higher order methods, and sparsity can each supply performance improvements of several orders of magnitude.

*Scientific computing problems touch many domains*.  A computational approach to a single research question may easily involve several scientific, mathematical, and computaitonal domains of expertise.  For example numerical weather prediction touches on meteorology, oceanography, statistics, partial differential equations, distributed linear algebra, and high performance array computation.

*The number of scientific problems that engage a particular domain generally exceeds the number of experts*.  E.g. far more questions use integration than there are experts in numerical integration.

Therefore we need to efficiently compare, distribute, and interconnect expertise.

*A single domain may be used by a wide set of projects; this set is rarely known by the domain expert*.  E.g. numerical integration is used in several fields which are unfamilar to numerical analysts.

An ideal software ecosystem selects and distributes the best implementation of a particular domain to all relevant problems.  Multiple implementations of a domain in stable co-existence is a symptom of a poorly functioning ecosystem.  It is a sign of poor reuse and fragments future development.