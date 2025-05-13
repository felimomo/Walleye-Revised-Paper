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
Phrased differently, if a particular policy is not included a priori due to a lack of creativity on behalf of analysts or perhaps due to the constraints of national legislation (e.g., see [@dfo2009]), then it simply is not possible to learn whether some alternative harvest control rule might outperform those tested. 
In the context of feedback policy design this may be problematic, because feedback policy design is often counterintuitive, and because analysts typically limit themselves to 1-dimensional control rules based on stock biomass [@punt2022framework;@sainsbury2000design]. 
For multidimensional population dynamics models (e.g., age-structured models, or community models), there is no theoretical guarantee that a single quantity (e.g. stock biomass) is sufficient to specify the optimal harvesting rate (see, e.g., discussion in @walters2002stock).
