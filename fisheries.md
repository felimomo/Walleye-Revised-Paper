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

```{figure} figures/conceptual.jpeg
:name: conceptual
:label: conceptual
:width: 70000px
:align: center

Conceptual framework for our methodology. a) The dynamics of a Walleye population is simulated using a size-structured stochastic model with 20 size classes (right box). 
The state of the population is specified by the biomass in each size class, i.e. the system's state is 20-dimensional. 
Policy decisions are based on either one or two observations are gathered out of this 20-dimensional vector (middle box). 
These observations are gathered through a simulated survey conducted by the fishery management. 
Vulnerable biomass: the total biomass vulnerable to the survey gear, mean fish weight: the mean weight of fish caught in the survey. 
A quantitative policy uses these observations to fix an instantaneous fishing mortality (left box). b) We optimize policies in three scenarios with differing utility functions. 
Total harvest: long-term yield. Hyperbolic additive risk-averse (HARA): a risk-averse utility function which penalizes inter-annual variability. 
Trophy fishing: a utility function which only values large fish, while small fish do not contribute to the utility. c) Four types of policies are optimized. The first three policies—FMSY, Precautionary Policy, and 1-Observation ML—use only the vulnerable biomass observation, whereas 2-Observation ML uses both observations.
```

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

# Methods

A collection of 15-30 walleye lakes in Alberta are managed using a Special Harvest License (SHL), which enables managers to assign a Total Allowable Catch (TAC) to limit harvest, and which is unique for inland recreational fisheries in North America. 
Presently, managers use an indicator-based approach to set TACs in any particular year based on standardized gill-netting surveys occuring in fall. 
However, @cahill2022unveiling showed that harvestable surplus existed in many systems even though many management plans stated the goal of seeking to harvest for maximum sustainable yield. 
Consequently, we aim to improve the scientific defensibility of this harvest allocation process in the Alberta SHL walleye program. 
To do so, we extended a standard age-structured population dynamics model of walleye described in detail in @cahill2022unveiling and use it to simulate realistic population dynamics. 
Briefly, we model population processes such growth in numbers at age through time as a function of Beverton-Holt stock-recruitment (see below), von Bertalanffy somatic growth in length-at-age (see @cahill2022unveiling), and total mortality as an additive process assuming total instantaneous mortality Z is equal to instantaneous natural mortality M plus fishing mortality rates imposed by recreational harvesters and which is modulated by vulnerability at age. 
Unless stated otherwise parameter values for all relationships are drawn from average values estimated in @cahill2022unveiling.

## Population dynamics

A finding of @cahill2022unveiling was that recruitment dynamics were highly variable and/or spasmodic (see also @caddy1983historical). 
Thus, we model a Walleye fishery population using a discrete-time, age-structured stochastic model with 20 age classes $(N_1,\dots,N_{20})$. 
Recruitment was modeled via the Beverton-Holt equation 
\begin{alignat}{2}
N_1(t+1) &= \alpha\, r_t SSB_t / (1 + \beta SSB_t), \quad &&\\
N_a(t+1) &= s\times (N_{a-1}(t) - H_{a-1}(t)), \quad && 1 < a <20,\\
N_{20}(t+1) &= s\times \left(N_{20}(t) - H_{20}(t) + N_{19}(t) - H_{19}(t)\right). \quad &&
\end{alignat}
Here we use the following notations: 
The harvest-at-age is $H_a(t) = F_t V_a N_a(t)$, where $F_t$ is the harvest mortality rate, $V_a$ is the *harvest vulnerability at age*,
\begin{align}
  V_a &= 
  \frac{1}{1 + e^{-(a-a_{hv})/2}}\, ,
\end{align}
and $a_{hv}=5$ is the age at half harvest vulnerability.
Notice that even for a high net fishing mortality rate, e.g. , the actual fishing mortality rate enacted on young age classes remains low, which is a common assumption in many recreational fisheries (e.g., see @golden2022focusing).
The spawning stock biomass is $SSB_t$, 
\begin{align}
  SSB_t &= \sum_{a=1}^{20} W_a N_a / 
  \left(
    1 + e^{-(a - a_{hm})/2}
  \right)\, ,
\end{align}
where $W_a$ is the average weight-at-age and $a_{hm}=6$ is the age at 50\% maturity (see @cahill2022unveiling for more details).
The parameters $\alpha$ and $\beta$ describe the juvenile survival as a function of $SSB_t$. 

The model has stochastic dynamics via the parameter $r_t$, which is independently sampled at each time-step. 
There are two types of events for the variable $r_t$: 
1) A normal recruitment year, in which $r_t$ is a log-normal variable $r_t\sim \mathrm{lognorm}(\mu=0, \sigma=1)$ (see, e.g., @quinn1999quantitative).
2) A large recruitment year, in which $r_t\sim \mathrm{unif}(10,30)$.
We use a Bernoulli trial with $\mathrm{Pr.}=0.025$ to decide whether a large recruitment event happens.
Large recruitment events are rare in any one fishery, however their occurrence happens at a rate much higher than would be predicted by the log-normal distribution alone [@cahill2022unveiling].
Our model for $r_t$ is a minimalistic description of this dynamic which has explicit control over the rate of large recruitment events.
We contrast recruitment timeseries for our model with timeseries using log-normal stochasticity in an appendix.

Simulations were run for 1000 time-steps in an attempt to capture the long-term effects of HCRs on population dynamics and performance criteria. 
Specifically, the expected number of large recruitment years (i.e. ‘‘successful’’ Bernoulli trials for $r_t$) over a period of 1000 time-steps is 25, which was judged to be high enough to capture the dynamics arising from a particular HCR. 
Using nomenclature from the RL literature, we also refer to these 1000 time-step simulations as *episodes*.

## Observations

We model observations by simulating a gillnetting survey carried out by a management agency tasked with monitoring and managing the fishery. 
These observations are then subsequently used by the HCR to set a fishing mortality for a specific  time-step. 
We consider two types of observations. 
The first is an estimate of the stock biomass vulnerable to the management agency’s survey gear,
\begin{align}
  B_{survey} = \sum_{a=1}^{20}
  S_a W_a N_a,
\end{align}
where $S_a$ is the vulnerability-at-age of the survey.
We model $S_a$ as increasing with fish length, and use a von Bertalanffy function to describe length-at-age,
\begin{align}
S_a \propto (1 - e^{\kappa a})^\varphi,
\end{align}
with $\kappa=0.23$ and $\varphi=2.02$ (see @cahill2022unveiling for more details).
A second observation we consider is the *mean weight* of fish in the survey,
$$
\bar{W}_{survey} = B_{survey} / N_{survey},
$$
with
$$
N_{survey} = \sum_{a=1}^{20} N_a S_a.
$$
Mean weight is easy for managers to observe and is important to consider in the context of spasmodically recruiting populations, since large recruitment events are correlated with dips in the mean weight of fish in the population.

We model observations as imperfect, with a multiplicative Gaussian observation error.
That is, policies do not observe $(B_{survey},\, \bar{W}_{survey})$ but rather, $(e_B B_{survey},\, e_W \bar{W}_{survey})$, where $e_B$ and $e_W$ are randomly sampled each time-step according to
$$
e_B, \, e_W \sim N(\mu=1,\, \sigma=0.1).
$$

Here we emphasize that the system is thus only *partially* observed [@memarzadeh2019resolving]: 
while the system dynamics unfold in the high-dimensional space defined by the biomass of each age class, most harvest control rules used to manage fisheries only observe some total measurement of stock biomass and apply a recommended Total Allowable Catch (TAC) based on this number. 
It is also worth noting that while the model dynamics are Markovian in the full state space, the dynamics of these two observed states are not, making this a so-called Partially Observed Markov Decision Process, or POMDP. 
This real-world problem that surveys provide imperfect information on overall abundance leads to a mathematical inconvenience that significantly increases the technical difficulty  of finding an optimal solution using classical tools like dynamic programming, and, as a result, the analysis of ecological POMDP problems has been restricted to models that make simpler ecological assumptions  (e.g., see @williams2022partial). 

## Utility models

We consider three utility models. 
1. *Total harvest utility* (i.e., *yield maximizing*): the utility at a time-step $t$ is given by the total harvested biomass at that time-step,
$$
  U_{HB}(t) = \sum_{a=1}^{20} H_a(t) W_a.
$$
2. *Risk averse utility*: a hyperbolic additive risk-averse utility function (also known as ‘HARA utility’) which values inter-annual stability in harvests,
$$
  U_{\text{HARA}}(t) = U_{HB}(t)^{\gamma},
$$
where the parameter $\gamma=0.6$ parametrizes the risk aversion (see, e.g., @collie2021harvest).
Notice that the power of $\gamma$ attenuates the utility derived from large harvests.
Thus, low-and-stable harvests can perform relatively better with respect to $U_{\text{HARA}}$ than large-but-sparse harvest peaks.[^opportunity]
We note that this is similar to using the natural log of catches as is commonly done in MSE.
Here the specific risk aversion $\gamma$ is set to a specific value for clarity, and we note that this parameterization converges on total harvest utility as  $\gamma\rightarrow1.0$.
3. *Trophy fishing utility*: here, harvested fish only contribute to the utility if they are above a certain size (age) class, $a > a_{thr.}$,
$$
  U_{trophy}(t) = \sum_{a>a_{thr.}} H_a(t) W_a,
$$
and we set $a_{thr.}=10$.

We optimize the performance of policies with respect to the total utility obtained over an episode, i.e.,
$$
U = \sum_t U(t).
$$


We considered utility functions one and two because they represent commonly acknowledged goals of fisheries management, that is maximizing yield and stabilizing harvests, which are to some degree at odds with one another and represent high harvest rates and high interannual variability in catches (maximizing yield) or low, but consistent harvest rates (maximizing risk-averse utility; see also @walters1996fixed; @collie2021harvest). 
Moreover we included the trophy fishing function to explore how our analysis would change for more complex, size-dependent, utility functions. 
Size dependence can be particularly relevant in cases where machinery to process harvests only operates within certain ranges of fish sizes, or perhaps when anglers only desire to retain large trophy-sized fish rather than valuing fish of any size equally (e.g., see @murphy1996fisheries; @licandeo2020management).

## Harvest control strategies

Here we describe the three classes of HCRs we consider in this paper.
We refer to them interchangeably as *control rules*, *rules* or *policies* throughout.

_**FMSY.**_ In this strategy, the agent applies the same instantaneous fishing mortality rate  each year that leads to the highest long-term yield on average [@hilborn1992harvest].

_**Precautionary policies.**_ A piece-wise linear HCR determined by three parameters: two stock biomass reference points $X_1$, $X_2$, and the fishing mortality at high stock biomass, $Y_2$. The HCR is given by the following equations:
\begin{alignat}{2}
F_t &= 0, \quad && \text{for } B_{survey}(t) < X_1,\\
F_t &= \frac{B_{survey}(t) - X_1}{X_2 - X_1}\times Y_2, 
  \quad && \text{for } X_1 \leq B_{survey}(t) \leq X_2,\\
F_t &= Y_2, \quad && \text{for } B_{survey}(t) > X_2.
\end{alignat}
For a visual guide of this policy, see @dfo2006, Fig. 2. 
In the literature, the parameters of this policy are often fixed at some fraction of the biomass at which the fishery maximizes average surplus production, $B_0$. 
A common choice is  $X_1=0.4 B_{MSY}$, $X_2=0.8 B_{MSY}$ and $Y_2=F_{MSY}$. 
We call this specific realization of the HCR the *constrained precautionary policy (cPP)*. 
In contrast, when we refer to the precautionary policy whose parameters $(X_1,\, X_2,\, Y_2)$ have been numerically optimized as the *optimized precautionary policy (oPP)*. 
We evaluate both of these HCRs in relation to our chosen utility functions.

We collectively refer to the three policy types described above as *fixed policies*, in the sense that their functional form is fixed *a priori* before the optimization begins. 
The process of optimizing these fixed policies is loosely inspired by MSE, where several competing policies are evaluated using dynamic simulations [@punt2014fisheries]. 
However, with the exception of the cPP our approach differs from MSE in that we explicitly search across a continuous  space of potential parameter values using Bayesian optimization procedures (see below).

_**Reinforcement learning policies.**_ This policy uses a neural network to express the harvest control rule. 
We explore two cases: one where only the stock biomass observation is used, and one where an additional mean weight observation is used. 
Mathematically,
$$
  F_t = f_\theta(\text{Obs}_t),
$$
where $f_\theta$ is a neural network with parameters $\theta$, and $\text{Obs}_t$ are the observations obtained at time-step $t$.

We optimized two different scenarios: one single-observation RL policy (*1RL*) in which only the biomass observation is used,
$$
  \text{Obs}_t = B_{survey}(t),
$$
and a two-observation RL policy (*2RL*) in which biomass and mean weight were both used
$$
  \text{Obs}_t = (B_{survey}(t),\, \bar{W}_{survey}(t)).
$$
In the 1-observation scenario, we used a 3-layer feed-forward network with layer sizes [64, 32, 16],
while in the 2-observation scenario, we used layer sizes of [256, 64, 16].[^networks]

## Bayesian optimization of ‘‘fixed policy’’ controls

We used the framework of Bayesian optimization to find optimal parameters for the fixed policy HCRs. 
A general introduction to this type of algorithm may be found on [@frazier2018tutorial;@skopt]. 
Bayesian optimization algorithms solve the problem of minimizing an unknown objective function  which one can only query with some stochasticity, i.e. where instead of being able to compute $f(x,\, y,\, z,\, \dots)$ exactly, we are only able to compute $f(x,\, y,\, z,\, \dots)+r$, where $r$ is random noise. 
Thus, this type of algorithm is designed to be able to approximate a solution to the minimization problem
$$
  \mathrm{argmax}_{x,y,z,\dots}\, f(x,\, y,\, z,\, \dots)
$$
even when one can only evaluate $f$ ‘‘imperfectly.’’
These algorithms are most useful in scenarios where evaluating $f$ is computationally expensive because they tend to require much fewer function evaluations of $f$ than brute force optimization approaches.

In our case, the arguments $(x,\, y,\, z,\, \dots)$ are the parameters in the HCR function. 
That is, for the precautionary policy, the arguments are $(X_1,\, X_2,\, Y_2)$, whereas for the $F_{MSY}$ policy, the only argument is the mortality rate $F$. We maximize the average utility obtained by a policy by optimizing over these parameters. For example, for the constant mortality mortality policy,
$$
  \mathrm{argmax}_F\, \mathbb{E}[U(F)],
$$
where $U(F)$ is the utility obtained by simulating an episode using a constant mortality $F$. 
Note that $U(F)$ is thus a random variable since the dynamics are stochastic.

Because of our system's high stochasticity, evaluating the mean episode reward afforded by any policy requires taking the average across many episodes. 
In  our case we used 200 episodes. 

We used the Gaussian process minimizer algorithm from the *scikit-optimize* Python package to perform this optimization, and allowed the algorithm to evaluate the objective function at 70 different points. 
The number of points was chosen heuristically as it appeared high enough for the optimization to converge, but remained low enough to provide reasonable runtimes. 
Further details of how this optimization procedure is performed may be found in the companion open-source code.[^tune]

## Harvest control via neural network: reinforcement learning optimization

Here we give a small overview of the RL methods we used to optimize neural network policies. 
For a broader introduction to RL, its methods, and applications in quantitative ecology, see [@lapeyrolerie2022deep]. 
Reinforcement learning is a class of strategies to approach sequential decision problems in which a simulated *‘‘agent’’* interacts with a simulated *‘‘environment’’*. 
The agent here represents the decision-maker, who observes and acts on the environment. 
In the context of our paper, the agent is a manager tasked with making a quota decision each year, while the environment is an age-structured model of the fish population. 
In RL, the agent can be restricted to only observe partial information about the state of the environment. 
Here we consider observations of the total stock biomass and the mean fish weight, but emphasize that additional observations could be passed to the agent.

The simulation is broken down into time-steps. At the beginning of each time-step, the agent prescribes an action that it applies to the environment. 
Subsequently, the environment changes its internal state due to this action taken by the agent, and outputs an observation and a reward back to the agent. 
The observation is used by the agent to take the next action. 
A visualization of this is shown in @lapeyrolerie2022deep, Fig. 1.

In the classic example of an Atari video-games,[^atari] the agent fills in the role of the person playing the game, while the environment is the game itself. 
In the context of this work, one can think of the manager prescribing a TAC as the RL agent making decisions given some partially observable fish population (or environment).
We call *training* the process by which the agent optimizes the values of the neural network weights $\theta$ (i.e., the process by which the RL algorithm optimizes the HCR). 
During training, the agent collects data on time-step interactions with the environment, slowly learning which actions to avoid given an observation, and which actions to encourage. 
This is done, in broad terms, using a variation of *stochastic gradient descent*, which takes advantage of the fact that gradients of the reward space can be efficiently computed on neural networks using *back propagation* (for an introduction to back propagation, see @rojas2013neural. 

A variety of algorithms exist for training RL agents, each with its strengths and drawbacks. 
Here we focus on the *Proximal Policy Optimization* algorithm (PPO), which has been shown to perform well over a broad set of benchmark environments [@schulman2017proximal].[^tqc]
It moreover has also been shown to have a strong performance in problems related to population dynamics [@lapeyrolerie2022deep; @montealegre2023pretty]. 
As mentioned, the neural networks we use in our results are rather modest in size—with only a few thousand parameters. 
The training times were also modest, comprising 6 million time-steps, or a bit under two hours of training on a commercial GPU.

## Policy evaluation

After optimizing each HCR, we simulated $n=500$ episodes and recorded the total utility received in each episode. 
Moreover, to get a more detailed comparison of the dynamics induced by each HCR, we simulated an additional episode where we recorded the stock biomass, mean fish weight and fishing mortality. 
To improve comparisons between policies, we used the same time-series of stochastic deviations $\{r_t\}_{t=1, \dots, 1000}$ across all of the latter set of simulations.

# Results

In {ref}`tab:fixed-params` we show the parameter values obtained for the optimized fixed HCRs. 
The episode utilities obtained by these HCRs are displayed in {ref}`tab:rew-table` and {ref}`fig:rewards`. 
With respect to the harvested biomass and HARA utilities, we find that *oPP* consistently outperforms the other HCRs tested, with *1 obs. RL* trailing close behind.
In particular, we observe the counter-intuitive result that with respect to HARA utility, 2 obs. RL underperforms with respect to both its single-observation counterpart and *oPP*.[^counter-intuitive]
With respect to trophy fishing we find the opposite trend: the *2 obs. RL* policy receives about 60\% more utility than all other HCRs.

The optimized HCRs are visualized in Fig. 3, where we plot fishing mortality as a function of stock biomass. 
We discuss these plots in the following paragraphs.


:::{table} Optimal parameter values for fixed policies for each of the three utility models. Here we compute $B^* = \mathrm{rew}(F^*)/F^*$ where $\mathrm{rew}(F^*)$ is the average step reward obtained by the constant mortality policy policy, and where $F^*$ is the constant mortality rate that maximizes surplus production (i.e., $F^*$ is optimal with respect to $U_{HB}$). As seen in the table, $F^*=0.0714$, and by using Table 2 we find that $\mathrm{rew}(F^*)=72.5/1000$. Thus $B_{MSY}=1.015$.
:label: tab:fixed-params
:align: center

| **Policy**    | **Harvested Biomass** | **HARA**        | **Trophy Fishing** | 
| ---       | ---               | ---         | ---            |
| $F_{MSY}$ | $F=0.071$        | $F=0.060$   | $F=0.060$      |
|           | ---               | ---         | ---            |
|           | $X_1=0.368$       | $X_1=0.000$ | $X_1=0.000$    |
| oPP       | $X_2=1.925$       | $X_2=3.408$ | $X_2=9.323$    |
|           | $Y_2=0.8$         | $Y_2=0.502$ | $Y_2=0.549$    |
|           | ---               | ---         | ---            |
|           | $X_1=0.418$       | $X_1=0.418$ | $X_1=0.418$    |
| cPP       | $X_2=0.836$       | $X_2=0.836$ | $X_2=0.836$    |
|           | $Y_2=0.071$       | $Y_2=0.060$ | $Y_2=0.060$    |
:::

```{csv-table} Summary statistics of the reward distributions shown in Fig. 2. In each column, the highlighted results are within a standard deviation of the best performing policy.
:label: tab:rew-table
:header: Policy,Biomass Harvested Util.,HARA Util.,Trophy Fishing Util.

1 obs. RL,**100.8** +/- 19.7,**186.8** +/- 26.9,35.7 +/- 6.9
2 obs. RL,**101.4** +/- 19.4,154.9 +/- 24.7,**61.7** +/- 12.0
FMSY,72.5 +/- 18.8,**174.7** +/- 26.6,35.9 +/- 7.7
oPP,**108.2** +/- 19.7,**188.0** +/- 27.6,36.0 +/- 6.6
cPP,84.6 +/- 17.7,157.6 +/- 27.5,37.3 +/- 7.8
```


```{figure} figures/rewards.jpeg
:name: fig:rewards
:label: fig:rewards
:width: 75000px
:align: center

Reward distributions for each of the five optimized policies in each of the three scenarios. 
These distributions were interpolated from the total utility obtained in n=500 simulated episodes. 
See associated Jupyter notebooks for details on this.
```

```{figure} figures/policies.jpeg
:name: fig:policies
:label: fig:policies
:width: 75000px
:align: center

Optimized policy functions for each of the scenarios. 
Top row: policies which only use the vulnerable biomass observation to inform fishing mortality. 
Bottom row: 2-Observation RL policy, where fishing mortality is informed by the vulnerable biomass observation as well as the mean fish weight observation.
The dependence of fishing mortality on mean weight is displayed with the color code (a gradient between green at high mean weight and violet at low mean weight).
```
*Harvested biomass utility ({ref}`fig:policies`, left column)*. 
In this scenario, both precautionary policies share a very similar $X_1$ threshold value (see {ref}`tab:fixed-params`), and the single-observation RL policy has a similar threshold. 
The two-observation RL control, exhibits a similar behavior, with the value of the threshold varying with mean weight.
At high mean weight $\bar{W}_{survey}(t)$, harvest mortality becomes high even at low biomasses $B_{survey}(t)$ (yellow hue), whereas at low mean weight harvests are suppressed (purple hue).

*HARA utility ({ref}`fig:policies`, middle column)*. 
Here, the threshold behavior which characterized HCRs in the previous paragraph disappears for all policies except for *cPP*.
This fits the intuition that low-and-stable yield performs better with respect to HARA utility than sparse fishing peaks.
Both highest-performing policies here, *oPP* and single-observation RL, converge to a linear control with similar slopes and similar intersection with the axis $F=F_{MSY}$.
Moreover, the 2 obs. RL control rule seems to learn that mean weight does not play an important role in the decision problem, and thus harvest variations as a function of $\bar{W}_{survey}$ are comparably small.

*Trophy fishing utility ({ref}`fig:policies`, right column)*. 
Two things are note-worthy in this scenario.
First, that the significant difference in HCR shapes for single-observation rules lead to negligible differences in episode utility obtained (see {ref}`tab:rewards` and {ref}`fig:rewards`).
Second, the high variability that the 2RL rule has as a function of observed mean weight.
In contrast to single-observation rules, whose harvest mortalities vary in the range of 0-0.25 for typical observed biomasses, notice that that the two-observation policy reaches harvest mortalities up to $F\approx 0.75$ for high mean weight observations.
This is mirrored in {ref}`fig:eps-um3`, where we can see that fishing is performed in pulses the 2RL, in contrast to the smooth variation in harvest mortality as a function of time observed in other policies.
Although the 2RL timeseries in {ref}`fig:eps-um3` is characterized by peaks and fishery closures, notice that prolonged fishery closures are also prominent in the cPP and oPP timeseries.

```{figure} figures/eps-um1.jpeg
:name: fig:eps-um1
:width: 75000px
:align: center
:number: 4

First 400 time-steps for an episode simulated with each of the HCRs optimized for *Total harvest utility*. 
```

```{figure} figures/eps-um2.jpeg
:name: fig:eps-um2
:label: fig:eps-um2
:width: 75000px
:align: center

First 400 time-steps for an episode simulated with each of the HCRs optimized for *HARA utility*. 
```

```{figure} figures/eps-um3.jpeg
:name: fig:eps-um3
:label: fig:eps-um3
:width: 75000px
:align: center

First 400 time-steps for an episode simulated with each of the HCRs optimized for *Trophy fishing utility*. 
```



<!-- --------- -->
<!-- Footnotes -->
<!-- --------- -->

[^opportunity]: In other words, the opportunity cost of a large harvest plays a smaller role the lower $\gamma$ is. Notice, further, that the exponent of $\gamma$ modifies the units of the utility (as opposed to other utility functions, $U_{HARA}$ does not have units of mass), as well as the scale at which utility varies. As we will see show in the results section, the biomass harvested at each time-step is typically $U_{HB}(t)<1$, and thus one can generally expect that $U_{HARA} > U_{HB}$.

[^networks]: We experimented using a [64,32,16] feed-forward network for the two-observation case, as well as other network geometries including thinner networks, wider networks, and deeper networks with 4 or 5 layers. Among the geometries we tested, we found that all policies either performed equally well or worse (in terms of average utility) to the geometries we present here. Because of this, we will not describe in detail these explorations. This paper's companion open-source code at https://github.com/boettiger-lab/rl4fisheries facilitates this exploration for the interested reader.

[^tune]: Found at https://github.com/boettiger-lab/rl4fisheries/blob/main/scripts/tune.py

[^atari]: Atari video-game environments have established themselves as a benchmark with which to test RL algorithms. For more information on these environments, see https://gymnasium.farama.org/environments/atari/

[^tqc]: We tested certain other RL algorithms, such as the *Truncated Quantile Critics (TQC)* algorithm, as well. However, we found it hard to match the performance of PPO. Because of this, we do not include the analysis of policies obtained with these other algorithms here. The companion source code allows the user to easily reproduce our analysis for PPO and other RL algorithms.

[^counter-intuitive]: It is counter-intuitive that the 2-observation RL policy performs worse than its 1-observation counterpart since the space of 2-observation policies contains the space of 1-observation policies. We address this point in the discussion section.

```{bibliography}
:style: alpha
```