---
title: Using machine learning to inform harvest control rule design in complex fishery settings
authors:
  - name: Felipe Montealegre-Mora
    affiliations: 
      - id: dse
        institution: Eric and Wendy Schmidt Center for Data Science and Environment\\ University of California,  Berkeley
        city: Berkeley
      - id: espm
        institution: Department of Environmental Science, Policy, and Management\\ University of California,  Berkeley
        city: Berkeley
  - name: Carl Boettiger
    affiliations:
      - espm
  - name: Carl J. Walters
    affiliations:
      - id: ubc
        institution: Institute for the Oceans and Fisheries,\\ University of British Columbia, 
        city: Vancouver
  - name: Christopher L. Cahill
    affiliations:
      - id: msu
        institution: Quantitative Fisheries Center, Department of Fisheries and Wildlife,\\ Michigan State University, 
        city: East Lansing
exports:
       - format: pdf
         template: arxiv_nips
         output: fisheries.pdf
abstract: |
  In fishery science, harvest management of size-structured stochastic populations is a long-standing and  difficult problem. 
  Rectilinear precautionary policies based on biomass and harvesting reference points have now become a standard approach to this problem. While these standard feedback policies are adapted from analytical or dynamic programming solutions assuming relatively simple ecological dynamics, they are often applied to more complicated ecological settings in the real world. 
  In this paper we explore the problem of designing harvest control rules for partially observed, age-structured, spasmodic fish populations using tools from reinforcement learning (RL) and Bayesian optimization. 
  Our focus is on the case of Walleye fisheries in Alberta, Canada, whose highly variable recruitment dynamics have perplexed managers and ecologists. 
  We optimized and evaluated policies using several complementary performance metrics. 
  The main questions we addressed were: 1. How do standard policies based on reference points perform relative to numerically optimized policies? 2. Can an observation of mean fish weight, in addition to stock biomass, aid policy decisions?
keywords: 
  - Machine learning
  - Reinforcement learning
  - Fishery science
  - Walleye 
  - Spasmodic fishery
bibliography:
  - refs.bib
---

# Introduction


Over the past two decades a wealth of literature has emerged that is aimed at solving sequential decision problems in diverse fields such as engineering, robotics, and control theory.
This work, collectively referred to as Reinforcement Learning (RL), has now advanced to the level needed to outperform human experts in many fields [@sutton;@bertsekas2019reinforcement].
For example, the application of RL has revolutionized games like chess, where the world’s top chess engines now almost always defeat elite players.
Beyond outperforming experts, RL offers a fresh perspective on previously unsolvable problems—and in the case of chess, top players now incorporate strategies discovered by engines into their repertoires that were once considered nonsensical by humans. 
Lessons like these seem relevant to fisheries scientists, as a number of sustainability problems lie at the intersection of age-structured population dynamics and sequential decision making under uncertainty [@walters1978ecological], and for which methods like dynamic programming or analytical approaches break down due to the “curse of dimensionality.”
It is in these decision-making contexts that RL shows promise, and could serve as a useful guide for improving our collective intuition surrounding the design of feedback policies. 

In the absence of tractable analytical or dynamic programming solutions to feedback policy design in fisheries, simulation-based approaches like Management Strategy Evaluation (MSE) have been used to evaluate trade-offs among alternative feedback policies or harvest control rules [@punt2016management]. 
In MSE, analysts first specify policies to test a priori, and only after the specification of a policy set is simulation then used to quantify the relative performance of those policies against explicit objective(s). 
While extensive application of the MSE approach has shown it to be a useful tool for informing harvest policy (e.g., [@edwards2016management]), it remains limited to select among the a priori policy set chosen by analysts. 
Phrased differently, if a particular policy is not included a priori due to a lack of creativity on behalf of analysts or perhaps due to the constraints of national legislation (e.g., see [@dfo2006]), then it simply is not possible to learn whether some alternative harvest control rule might outperform those tested. 
In the context of feedback policy design this may be problematic, because feedback policy design is often counterintuitive, and because analysts typically limit themselves to 1-dimensional control rules based on stock biomass [@punt2022framework;@sainsbury2000design]. 
For multidimensional population dynamics models (e.g., age-structured models, or community models), there is no theoretical guarantee that a single quantity (e.g. stock biomass) is sufficient to specify the optimal harvesting rate (see, e.g., discussion in @walters2002stock).

The dynamics of age-structured populations occur in high dimensional spaces, and thus in some situations it is possible for different states to correspond to the same total stock biomass. 
For example, in a standard  age-structured model, many small fish can have the same total biomass as a few large fish. 
Not surprisingly, managers might prescribe different management actions  in  these different contexts (see similar arguments in @hilborn2002dark). 
Intuitively, a dimensionality problem such as this makes it difficult  to specify policies to test a priori. While complexity due to age-structure makes it difficult to specify effective policies a priori, it also implies that managers have access to more information than total biomass in some situations—this additional information might be useful for informing harvesting decisions. 
For instance, knowing the mean fish weight in addition to total stock biomass might help the manager distinguish between a population with many small fish and one with a population composed of mainly large fish. 
Sources of additional information could be especially useful for managing fisheries with complex population dynamics such as populations with spasmodic recruitment dynamics.

Fish populations with highly variable, spasmodic, recruitment are some of the most perplexing systems to learn from and in which to inform fisheries management [@hjort1914fluctuations; @caddy1983historical], and thus serve as a useful test case in which to explore the utility of RL methodologies for improving feedback policy design. 
In this paper we define highly variable or spasmodic recruitment to mean that a fish population exhibits infrequent large year classes of at least 10-50 times the long-term average recruitment level (see @caddy1983historical). 
While by definition such events are rare in any one system, a cursory review of the literature reveals that these types of fluctuations occur with some regularity in fisheries throughout the world (e.g., see @fisch2019comparison; @licandeo2020management). 
For example, while better fisheries management practices helped rebuild Northeast Atlantic fish stocks, it appears that of the stocks that displayed the strongest recoveries, record large year classes occurred at low abundance that drove stock productivity upward and out of low, collapsed states (see @zimmermann2019improved). 
Similarly, Atlantic Redfish Sebastes fasciatus stocks off the eastern coast of Canada exhibit spasmodic recruitment fluctuations that make the application of standard stock assessment techniques and MSE challenging [@licandeo2020management]. 
In inland systems, Walleye Sander vitreus in Lake Erie increased from low abundance due to a large recruitment event in 2003, and this cohort has continued to support the bulk of one of most economically valuable recreational fisheries in the world for nearly a decade [@schmitt2020does]. 
In these examples, large recruitment events affected both population status and the users who rely upon those populations; however, such events are almost always written off as “environmental effects” and are notoriously difficult to predict with the reliability needed to inform feedback policy design (see @myers1998environment; @punt2014fisheries).

Little work has examined the implications of infrequent large recruitment events on the performance of feedback policies in age-structured populations, even though feedback policies are now considered the de facto standard for managing fisheries exploitation worldwide (see @free2023harvest; @silvar2022empirical; however, see @licandeo2020management). 
This is noteworthy because nearly all of the theoretical work underpinning harvest control rule or feedback policy design has assumed populations exhibit uncorrelated recruitment anomalies originating from standard, stationary statistical distributions (see @walters1975optimal; @walters1978ecological; @reed1979optimal; however see @parma1990experimental; @hawkshaw2015harvest).
In this paper, we apply RL and Bayesian optimization to the problem of designing harvest control rules (HCRs) for partially-observed age-structured populations exhibiting highly variable recruitment dynamics (see Fig. 1a). 
Specifically, we use RL methodologies to explore whether multi-dimensional control rules—particularly rules depending on the total stock biomass and the mean fish weight—might be helpful for managing age-structured, spasmodically recruiting, populations. 
We focus our case study on a recreational Walleye fishery managed via harvest lottery in Alberta, Canada (see @sullivan2003active), as recent work showed these populations recovered from collapse due in part to large positive recruitment anomalies [@post2002canada; @cahill2022unveiling]. 
We also compare the policies obtained by numerical optimization with a rectilinear ‘default’ precautionary rule recommended by Canada [@dfo2006].

We evaluate HCRs with three types of utility functions: total harvest (i.e., yield maximizing), a risk-averse utility that prioritizes inter-annual consistency in catch, and a trophy fishing utility in which only sufficiently large fish are valued by harvesters (see Fig. 1b). 
We optimize and evaluate four classes of HCRs: 1) constant fishing mortality rate ($F_{MSY}$), 2) a rectilinear precautionary rule derived from $F_{MSY}$ and the biomass at which surplus production is maximized $B_{MSY}$ (see [@dfo2006; Fig. 1] for a visualization), 3) an unconstrained optimum rectilinear precautionary rule and 4) an HCR parametrized by a deep neural network using RL (see Fig. 1c).

We found that considerable gains can be achieved in performance by precautionary policies by optimizing policy parameters. 
By using an RL methodology we explored whether mean weight was a useful variable for policy decisions and found that the answer depends on the choice of utility function. 
Namely, we found that mean weight was useful in the trophy fishing setting and, surprisingly, not in the yield maximizing and risk averse settings.

```{bibliography}
:style: alpha
```