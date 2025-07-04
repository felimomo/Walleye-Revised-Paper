---
title: Response letter to reviews on "Using machine learning to inform harvest control rule design in complex fishery settings"
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
         output: response.pdf
---

We want to thank both reviewers and the editor for their insightful comments and recommendations on our paper.
These suggestions led us to significantly improve our results, as well as their presentation.

**Response to General Comments by Reviewer 1:**

- *A. Reviewer comment:* Results are shown for three “classes” of management objective, but (a) there is no explicit “risk objective” (other than “no fish = no catch”), and (b) there is no explicit recognition that most MSE-type analyses involve multiple objectives simultaneously.
In really, HCRs are selected to achieve some “weighted sum” of multiple (often conflicting) objectives. Either text needs to be added to recognize this, additional objective added, or (ideally) a multi-goal objective added.

*Authors response:* This was a crucial conceptual point to address in the paper. 
We have added an explanation of our choice to focus on simpler 'unmixed' utility models in the last paragraph of Sec. 2.3, page 7 lines 10-15.

- *B. Reviewer comment:* Data are made available to the control rules. I did not see any discussion of how these
were generated and accounted for measurement error – it would not be appropriate to
test HCRs under the assumption of perfect information.

*Authors response:* In our original draft we did not include noise in the observation process. 
Our new results include a 10\% noise on observations which we explicitly define in the paper in eq. (11) and the preceding paragraph (page 6, lines 11-15).

This change in the observation model incidentally fixed the nuisance of obtaining an under-performing 2-observation policy in the HARA utility case (mentioned in point G of the review).
In Fig. 3 it can now be observed that the 2-observation policy performs on par with single-observation policies.
The additional observation increases the search space for 2-observation policies, and without observational noise we did not find a 2-observation policy that matched the performance of single-observation policies (trying a variety of network structures mentioned in Footnote 3 in the paper).
These explorations seem to indicate that *perfect* information about the vulnerable biomass seems to be enough for the control problem in the HARA scenario.
Our hypothesis is that, in the case of noisy observations the increased complexity of a larger search space for 2-observation policies is counteracted by the fact that the *(noisy)* mean weight observation seems to provide complementary information about the state of the system that might be lost in the *(noisy)* vulnerable biomass observation.

- *C. Reviewer comment:* I would not say strategy “1” is FMSY and that strategy “2” depends on FMSY and BMSY
because those strategies have well-understood meanings, which is not what is
modelled here (it seems). There is nothing wrong with constant F and threshold
strategies – my concern is the implied link to FMSY and BMSY.

*Authors response:* We corrected our naming conventions for clarity. 
For strategy 1, we introduce it as a constant finite exploitation rate (or constant $U$) strategy and use the abbreviation ‘‘UMSY’’ for succinctness within figures. 
We explicitly explain this naming convention in Sec. 2.4, page 7 lines 25-27. 
We moreover correct the terminology used in the definition of the constrained precautionary policy (cPP; p. 7, lines 33-39). 
We now use $B^*$ as a reference biomass for this policy, defined in eq. (17), and we use $Y_2 = U^*$, where $U^*$ is the optimal constant finite exploitation rate (line 34).

- *D. Reviewer comment:* The cPP strategy involves pre-specifying x1, x2 and y2 but I could not see where the
values for these quantities were defined/set.

*Authors response:* We define these parameters in p. 7, lines 33-39.
Moreover we show our explicit calculation of policy parameters in the legend of Table 1 (p. 10, lines 1-3).

- *E. Reviewer comment:* Table 2. I struggle to understand why the optimized policy does not outperform the
constrained version – in fact I was not sure from Table 1 what is being constrained
anyway as the text suggests that x1, x2 and y2 would be pre-defined for that strategy.
Given Fig. 2, it is possible the results for cPP and oPP were swapped in Table 2 as the
order of results in Fig 2 makes more sense a priori.

*Authors response:* Thank you, as anticipated in the review this was a typo in the table.

- *F. Reviewer comment:* Philosophically, some of the strategies (especially 2 obs RL for trophy) lead to quite
bizarre results (e.g. the pulses)– I believe that the paper would benefit from a
discussion of how one might explain this to a decision maker / a set of stakeholders. I
am not sure any recreational stakeholder group would trade off a (slight?)
improvement in trophy fish catch with not being able to fish in many years. This
relates to point A above.

*Authors response:* This was an important point to address in our new version of the paper.
This point led us to create two new figures (Figs. 8 and 9), and an appendix (app. A). 
Figure 9 is dedicated to understanding the average behavior of each optimized policy in the timesteps following a large recruitment year.
Figure 8 zooms into the time-series for policies optimized for trophy fishing---here provide insight into the timing of the fishing pulses obtained by the 2-observation RL policy, and how these relate to the timings of large recruitment years.
We expand on the tradeoff between fishing pulses and more stable fishing strategies in Appendix A specifically for the trophy fishing utility case, in which fishing pulses are used by 2-observation RL as a strategy to outperform other policies.
In that appendix we observe that averaging observations over 3 timesteps can enable RL to not only slightly increase the average utility obtained, but also to generate smoother fishing time-series.

- *G. Reviewer comment:* I wonder if another reason for the unexpected performance of the 2 obs RL vs 1 obs
RL strategies for total harvest and HARA may be that with more complexity (aka data
inputs) more layers may have been appropriate.

*Authors response:* Thanks for this comment, we now expand on the network geometries we use for 2-observation policies in p. 8, lines 5-10.
As mentioned above, however, in the scenario of noisy observations, the paradoxical underperformance of the 2-observation RL policy in this case disappeared.


**Response to General Comments by Reviewer 2:**

- *Reviewer comment:* They compare different harvest control rules, however find minor
differences between the different harvest rules (depending on utility function). I appreciate the
uptake of machine learning methods in fisheries, as an interesting and novel way to build harvest
control rules. However, I do find the results in this study a bit confusing, underwhelming, and
only superficially reported. There is no real pattern to which policy or HCR performs the best,
and it is not really clear from the manuscript what leads to these results. The manuscript needs to
explore in greater detail what effects are leading to differences in results between the different
scenarios and episodes.

*Authors response:* We have rewritten our results section including 2 new figures and an appendix to clarify the significance and limitations of our results, as well as the dynamical patterns that lead to them.
Our new results include a noise process on observations (addressed in Point B. of the response to reviewer 1 above).
Beyond adding realism to the optimization problem we study, this change crucially simplified the relative performances between different models (as seen in Figure 3 and Table 2). 
We believe that this more straightforward pattern in the relative performance of different policies enables clearer conclusions to be drawn.
We discuss these conclusions in paragraphs 2-4 of our results section.

In Figures 8 and 9 we provide a more detailed picture of the behavior of policies in response to recruitment pulses.
Figure 9 addresses the differences in average behavior within a controlled setting: a setting in which year 1 is a large recruitment year and the following years are ‘‘normal’’ recruitment years.
This Figure allows us to tease apart how different policies lead to different average responses to an isolated large recruitment event.
With Figure 8 we aim to clarify the role of fishing pulses in the trophy fishing scenario, where we find that mean fish weight information can be used in policy decisions to outperform single-observation policies.
There we can see two aspects: a) the compound effect of sequences of large recruitment years, and b) the dynamical response to these by the 2-observation RL policy.

In Appendix A, we moreover explore the tradeoff between pulse fishing and harvest stability.
We show that policies that use averaged observations over short windows of time can lead not only to increased utility obtained, but also to fishing patterns that are smoother over time. (We restrict our exploration to averaged observations over 3 timesteps.)

We have significantly improved our mathematical notation in the new version of the paper.
Moreover, we now include definitions of quantities such as the vulnerability-at-age and the spawning stock biomass which, in the original version of the paper, the reader was referred to a citation for their definition.
The vulnerability at-age as well as the weight-at-age are now plotted in Figure 2.
In our definitions we now include units as well.

- *Reviewer comment:* The mathematical notation is very inconsistent in the manuscript. Units are notoriously lacking,
and there is no consistency in subscripts (e.g, sometimes age is denoted U_a and other times
U(a)).

*Authors response:* Thank you for pointing out the challenges with the mathematical notation used in the original manuscript.
We have significantly improved the mathematical notation used in the paper, and include units in the definition of relevant quantities (for example, in p. 4, lines 45-49).
We have now also corrected the consistency of when subscripts and parentheses are used.

- *Reviewer comment:* The values in table 1 and table 2 does not really seem to match, so I am wondering if
either they are different units or how they are defined? The utility scores are about 100x higher
than the modeled biomass. Is that because the utility sums over all episodes?

*Authors response:* Tables 1 and 2 correspond to different aspects of our results: Table 1 lists the optimal parameter values for fixed policies, while Table 2 provides summary statistics for the utilities obtained by the different policies.
We now provide a clearer description of the quantities expressed in these Tables in their corresponding captions and in the first paragraph of the Results section.
We moreover have used a clearer layout for Table 1.
The average rewards displayed in Table 2, as well as the reward distribution depicted in Figure 3, corresponds to episode rewards as anticipated by the reviewer.
We now mention this fact in the results section, p. 9, lines 49-50.

- *Reviewer comment:* I also think the HARA utility function is incorrectly annotated. The authors state in the text that
the model takes into account the interannual variability, but this is not reflected in the
mathematical notation, so I am not sure what is correct. It is odd to me that the HARA function
as it is written in the manuscript outperforms the yield maximizing function almost two fold in
utility. How would a function that simply discounts the reward have so significant different
results from a function aimed at maximizing yield?

*Authors response:* We now inlcude a description of how HARA utility favors policies with stable harvests over time over policies with pulsed harvests in Sec. 2.3 (p. 6, lines 37-47), even though the utility is defined for each timestep independently.
Moreover, in lines 47-48 we address the reason for HARA utility to have significantly higher absolute values than yield utility.
Given that these utilities naturally 'live' in different scales (and in fact have different units), a direct comparison between utility values is not possible.

- *Reviewer comment:* Figure 2 (**now Figure 3**). I do not understand why there is a second column with just one harvest rule in this figure. Why is
2 obs RL separated from the other distributions?

*Authors response:* Thanks, the presentation in the original version of the paper was indeed awkward, with the 2 observation policy separated from all others.
We now group the two RL policies in the bottom row, to facilitate the comparison of their performance to fixed policies, displayed in the top row.

- *Reviewer comment:* Figure 3 (**now Figure 4**). I think the shape of these curves could be explored a bit more in the manuscript. I
don’t find all of them intuitive and I think it warrants a more thorough discussion on what leads
to these shapes (in particular the RL rules).

*Authors response:* Thank you for this important comment. 
We now provide a fuller discussion of these functional shapes obtained in the Discussion section, p. 17 lines 9-17 and 34-59.
There, we acknowledge the counter-intuitive nature of the 2-observation RL policies, and connect the policy shapes to their dynamic behavior which is explored in Figures 8 and 9.

- *Reviewer comment:* Figure 4 (**now Figures 5-7**). Is this the output of one specific episode? I am wondering in particular why there is a
massive biomass peak 150 years into the episode. It seems to me that the operating model is
driving the results quite a lot more than the harvest control rules.

*Authors response* These time-series are the output for one specific episode, we now state this in Sec. 2.7 (p. 9, lines 34-39).
While in spasmodic populations, large recruitment pulses are the main drivers of the system's dynamics, in Figure 8 we now give some intuition about the fact that several recruitment pulses can happen within intermediate amounts of time leading to biomass increases.
Thus, while the population's dynamics is strongly influenced by large recruitment years, within any given episode there are many configurations of sequences of large recruitments which are likely to happen (e.g., as seen in Figure 8), so that policies are evaluated over a rich variety of possible recruitment histories.


Here it is important to note that, in making our mathematical definitions explicit and complete, we also realized that there was a minor bug in our code's implementation of the model's stochastic dynamics.
In the old version of the paper, the stochastic deviations in normal recruitment years had an expected value of about 0.25 rather than of about 1.0. 
This led to a stark under-recruitment in ‘‘normal’’ years which increased the relative importance of large recruitment years and decreased the relative importance of the policy response in the two decades following a recruitment pulse. 
(The latter effect was due to the under-recruitment which led to a quicker convergence to a biomass ‘‘equilibrium,’’ and a quicker rate convergence to that equilibrium.)

- *Reviewer comment:* Spasmodic recruitment. I find the way the spasmodic recruitment is modeled a bit questionable, and I would suggest that
the authors show somehow that their modeled recruitment provides somewhat realistic estimates
(e.g., by comparing the simulated to the historical estimated recruitment). When using log-
normally distributed residual error, recruitment is already prone to being spasmodic in some of
the simulated data. Adding the Bernoulli trials, I suspect that these recruitment numbers become
inflated, and I am assuming can potentially drive a large fishery for years (which appears true in
the episode plotted later).

*Authors response* We now provide a discussion of our modelling choices regarding the spasmodic recruitment model in the Methods section, p. 5 lines 9-16 and 22-27.


**Response to specific comments by reviewer 2:**

- *Reviewer comment 1:* The model of the population dynamics is age-structured but the abstract and Fig. 1
include reference to a size-structured population dynamics model. I suspect (hope)
that the latter is a left-over from the draft of another paper.

*Authors response:* Yes, the references to size-structure were leftovers of an initial draft in which many of the model details had not been worked out. We have now changed them to refer to age structure.

- *Reviewer comment 2:* Pg 5, line 2. I can’t speak to Licandeo et al. (2020) but I suggest you look at the
South African MSEs for small pelagic species, which include several species with the
same type of episodic recruitment regimes as you consider in this paper.

*Authors response:*  We could not find a reference for this and would welcome a pointer if it can be relevant to include in our paper.

- *Reviewer comment 3:* I think the Nt,a-1 at the bottom of page 6 should be Ht,a-1.

*Authors response:* Yes, this was correct. We have significantly improved the clarity and consistency of the mathematical notation we used which should hopefully avoid similar typos in the new version of the manuscript.

- *Reviewer comment 4:* SSBt is not defined. Also, the equation for Ht,a is wrong because as defined (Pg 7, line
13), Ht,a is in weight on Pg 7 and the top of Pg 9, but its use in the dynamics equations
is in the form of numbers. Finally, F is not fishing mortality but fully-selected
exploitation rate.

*Authors response:* We now define SSB in eq. (5). As anticipated in the review, H has units of N per hectare as required by eqs. (1)-(3). Thus, in our previous version of the paper our definition H was incorrect since it included a weight term. Consequent with this correction of the definition of H, we have updated the definition of the utility functions so that $U_{yield}$ and $U_{HARA}$ have units of weight per hectare.

- *Reviewer comment 5:* I suspect it matters little, but I was intrigued by how productive (aka slope at the
origin) this simulated beast was as well as the variation of recruitment for “normal”
conditions.

*Authors response:* Our previous description of the stochastic model was incomplete, and this comment made us realize that. Now, in Eq. (5), we specifiy what the distribution for $r_t$ is on a ‘‘normal’’ year. 
From this we can obtain that the expected value for $r_t$ on such a year is approximately 1, and thus the year's average surplus production coincides with that predicted by the Beverton-Holt equation (eq. (1)). 
Moreover, one can see in Figure 9 the timescale at which the system equilibrates after a large recruitment year. 
(In that figure, the pink lines correspond to a policy of no fishing.)

- *Reviewer comment 6:* Fig 1. “total caught biomass observation” should be “survey estimates of biomass”.

*Authors response:* We now made the language regarding our observations more consistent and percise throughout the paper.

- *Reviewer comment 7:* (Pg 8, line 3). Could you indicate whether survey selectivity is asymptotic or dome-
shaped?

*Autors response:* We now show plots for survey and harvest vulnerability at-age, and for weight at-age in Figure 2. Both the survey as well as harvests have asymptotic selectivity.

- *Reviewer comment 8:* 200 episodes seems quite limited given the high process error – how comfortable are
you that this is sufficient as the text (Pg 22, lines 23+) is unclear on this point.

*Authors response:* We now provide a more detailed account for our reasoning to use 200 episodes for optimization in p. 8 lines 34-43, and in Footnote 2. 
In the main text this argument is short, but we expand on it in Appendix B, showing that distribution of episode surplus productions has approximately converged for 200 samples.
We expand on these arguments in Footnote 5, which gave us further heuristic evidence that 200 episodes were sufficient for optimization.

- *Reviewer comment 9:* Pg 13, line 9 (**now p. 9, lines 32-33**). Define the “total utility” – is this the expected values for the
performance measures over years and episodes?

*Authors response:* We now made that sentence clearer and more precise.

- *Reviewer comment 10:* Table 1. What are the units of BMSY? Also, what is a step reward?

*Authors response:* This led us to realize that in our original manuscript, the cPP policy (defined with respect to ‘‘BMSY’’) had a problem: indeed, in the HARA utility scenario, this quantity did not have units of biomass---as required for the policy's parameters to be correctly defined.
This together with general comment C. above led us to redefine the cPP policy. 
We now explicitly define the parameters $U^*$ and $B^*$ used to compute the cPP policy in Sec. 2.4 (p. 7 lines 25-40).
The procedure by which $B^*$ is computed, and the value obtained, is described in the caption of Table 1.

- *Reviewer comment 11:* Pg 15. Line 4. This should be Fig 3 not Fig. 2 I think.

*Authors response:* Figure references are now corrected, thank you.
