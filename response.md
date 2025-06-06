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

*A.* This was a crucial conceptual point to address in the paper. 
We have added an explanation of our choice to focus on simpler 'unmixed' utility models in the last paragraph of Sec. 2.3, **Lines X**.

*B.* In our original draft we had indeed failed to include noise in the observation process. 
Our new results include a 10\% noise on observations which we explicitly define in the paper in eq. (10) and the preceding paragraph (**lines X**).

This change in the observation model incidentally fixed the nuisance of obtaining an under-performing 2-observation policy in the HARA utility case (mentioned in point G of the review).
In Fig. 3 it can now be observed that the 2-observation policy performs on par with single-observation policies.
The additional observation increases the search space for 2-observation policies, and without observational noise we did not find a 2-observation policy that matched the performance of single-observation policies (trying a variety of network structures mentioned in Footnote 3 in the paper).
These explorations seem to indicate that *perfect* information about the vulnerable biomass seems to be enough for the control problem in the HARA scenario.
Our hypothesis is that, in the case of noisy observations the increased complexity of a larger search space for 2-observation policies is counteracted by the fact that the *(noisy)* mean weight observation seems to provide complementary information about the state of the system that might be lost in the *(noisy)* vulnerable biomass observation.

*C.* We corrected our naming conventions for clarity. 
For strategy 1, we introduce it as a constant exploitation rate strategy and keep the abbreviation ‘‘FMSY’’ for succinctness within figures. 
We explicitly explain this naming convention (with an acknowledgement that it is not wholly accurate, but rather convenient) in Sec. 2.4, **Lines X**. 
We moreover correct the use of $F_{MSY}$ and $B_{MSY}$ in the definition of strategy 2 in the same section, **lines X**.

*D.* We conceptually define how these parameters are obtained for cPP in Sec. 2.4, **lines X**. 
The equations used to compute these quantities are presented in Table 1, with the values obtained being part of the body of Table 1. 

*E.* Thank you, as anticipated in the review this was a typo in the table.

*F.* This was an important point to address in our new version of the paper.
This point led us to create two new figures (Figs. 8 and 9), and an appendix (app. A). 
Figure 9 is dedicated to understanding the average behavior of each optimized policy in the timesteps following a large recruitment year.
Figure 8 zooms into the time-series for policies optimized for trophy fishing---here provide insight into the timing of the fishing pulses obtained by the 2-observation RL policy, and how these relate to the timings of large recruitment years.
We expand on the tradeoff between fishing pulses and more stable fishing strategies in Appendix A specifically for the trophy fishing utility case, in which fishing pulses are used by 2-observation RL as a strategy to outperform other policies.
In that appendix we observe that averaging observations over 3 timesteps can enable RL to not only slightly increase the average utility obtained, but also to generate smoother fishing time-series.

*G.* Thanks for this comment, we now expand on the network geometries we use for 2-observation policies in Footnote 3.
As mentioned above, however, with noisy observations, the paradoxical underperformance of the 2-observation RL policy in this case disappeared.


**Response to General Comments by Reviewer 2:**

We have rewritten our results section including 2 new figures and an appendix to clarify the significance and limitations of our results, as well as the dynamical patterns that lead to them.
Our new results include a noise process on observations (addressed in Point B. of the response to reviewer 1 above).
Beyond adding realism to the optimization problem we study, this change crucially simplified the relative performances between different models (as seen in Figure 3 and Table 2). 
We believe that this more straightforward pattern in the relative performance of different policies enables clearer conclusions to be drawn.
We discuss these conclusions in paragraphs 2-4 of our results section.

In Figures 8 and 9 we provide a more detailed picture of the behavior of policies in response to large recruitment years.
Figure 9 addresses the differences in average behavior within a controlled setting: a setting in which year 1 is a large recruitment year and the following years are ‘‘normal’’ recruitment years.
This Figure allows us to tease apart how different policies lead to different average responses to an isolated large recruitment event.
With Figure 8 we aim to clarify the role of fishing pulses in the trophy fishing scenario, where we find that mean fish weight information can be used in policy decisions to outperform single-observation policies.
There we can see two aspects: a) the compound effect of sequences of large recruitment years, and b) the dynamical response to these by the 2-observation RL policy.

In Appendix A, we moreover explore the tradeoff between pulse fishing and harvest stability.
We find that policies that use averaged observations over short windows of time can lead not only to increased utility obtained, but also to fishing patterns that are smoother over time. (We restrict our exploration to averaged observations over 3 timesteps.)

We have significantly improved our mathematical notation in the new version of the paper.
Moreover, we now include definitions of quantities such as the vulnerability-at-age and the spawning stock biomass which, in the original version of the paper, the reader was referred to a citation for their definition.
The vulnerability at-age as well as the weight-at-age are now plotted in Figure 2.
In our definitions we now include units as well.

Tables 1 and 2 correspond to different aspects of our results: Table 1 lists the optimal parameter values for fixed policies, while Table 2 provides summary statistics for the utilities obtained by the different policies.
We now provide a clearer description of the quantities expressed in these Tables in their corresponding captions and in the first paragraph of the Results section.
We moreover have used a clearer layout for Table 1.
The average rewards displayed in Table 2, as well as the reward distribution depicted in Figure 3, corresponds to episode rewards as anticipated by the reviewer.
We now mention this fact in the second sentence of the results section (**lines X**).

We now inlcude a description of how HARA utility favors policies with stable harvests over time over policies with pulsed harvests in Sec. 2.3 (**lines X**), even though the utility is defined for each timestep independently.
Moreover, in Footnote 1 we address the reason for HARA utility to have significantly higher absolute values than yield utility.
Given that these utilities naturally 'live' in different scales, we include a comment discouraging direct utility comparisons between utility functions in Footnote 2.

**Figure 2 (now Figure 3):** Thanks, the presentation in the original version of the paper was indeed awkward, with the 2 observation policy separated from all others.
We now group the two RL policies in the bottom row, to facilitate the comparison of their performance to fixed policies, displayed in the top row.

**Figure 3 (now Figure 4):** We now provide a fuller discussion of the policy shapes obtained in paragraphs 2-4 of the Discussion section.
In these paragraphs we acknowledge the counter-intuitive nature of the 2-observation RL policies, and connect the policy shapes to their dynamic behaviors explored in Figures 8 and 9.

**Figure 4 (now Figures 5-7):** These time-series are the output for one specific episode, we now state this in Sec. 2.7 (**lines X**).
While in spasmodic populations, large recruitment pulses are the main drivers of the system's dynamics, in Figure 8 we now give some intuition about the fact that several recruitment pulses can happen within intermediate amounts of time leading to biomass increases.
Thus, while the population's dynamics is strongly influenced by large recruitment years, within any given episode there are many configurations of sequences of large recruitments which are likely to happen (e.g., as seen in Figure 8), so that policies are evaluated over a rich variety of possible recruitment histories.


Here it is important to note that, in making our mathematical definitions explicit and complete, we also realized that there was a minor bug in our code's implementation of the model's stochastic dynamics.
In the old version of the paper, the stochastic deviations in normal recruitment years had an expected value of about 0.25 rather than of about 1.0. 
This led to a stark under-recruitment in ‘‘normal’’ years which increased the relative importance of large recruitment years and decreased the relative importance of the policy response in the two decades following a recruitment pulse. 
(The latter effect was due to the under-recruitment which led to a quicker convergence to a biomass ‘‘equilibrium,’’ and a quicker rate convergence to that equilibrium.)


**Spasmodic recruitment:** We now provide a discussion of our modelling choices regarding the spasmodic recruitment model in the next-to-last paragraph of Sec. 2.1 (**lines X**).


**Response to specific comments by reviewer 2:**

1. Yes, the references to size-structure were leftovers of an initial draft in which many of the model details had not been worked out. We have now changed them to refer to age structure.

2. **I dont know what to say about this one, haven't found the small pelagic species paper**

3. Yes, this was correct. We have significantly improved the clarity and consistency of the mathematical notation we used which should hopefully avoid similar typos in the new version of the manuscript.

4. We now define SSB in eq. (5). As anticipated in the review, H has units of N per hectare as required by eqs. (1)-(3). Thus, in our previous version of the paper our definition H was incorrect since it included a weight term. Consequent with this correction of the definition of H, we have updated the definition of the utility functions so that $U_{yield}$ and $U_{HARA}$ have units of weight per hectare.

5. Our previous description of the stochastic model was incomplete, and this comment made us realize that. Now, in Sec. 2.1 (**lines X**), we specifiy what the distribution for $r_t$ is on a ‘‘normal’’ year. 
From this we can obtain that the expected value for $r_t$ on such a year is approximately 1, and thus the year's average surplus production coincides with that predicted by the Beverton-Holt equation (eq. (1)). 
Moreover, one can see in Figure 9 the timescale at which the system equilibrates after a large recruitment year. 
(In that figure, the pink lines correspond to a policy of no fishing.)

6. We now made the language regarding our observations more consistent and percise throughout the paper.

7. We now show plots for survey and harvest vulnerability at-age, and for weight at-age in Figure 2. Both the survey as well as harvests have asymptotic selectivity.

8. We now provide a more detailed account for our reasoning to use 200 episodes for optimization in the paragraph surrounding eq. (24) (**lines X**). 
In the main text this argument is short, but we expand on it in Appendix B, showing that distribution of episode surplus productions has approximately converged for 200 samples.
We expand on these arguments in Footnote 5, which gave us further heuristic evidence that 200 episodes were sufficient for optimization.

9. We now made that sentence clearer and more precise.

10. This led us to realize that in our original manuscript, the cPP policy (defined with respect to ‘‘BMSY’’) had a problem: indeed, in the HARA utility scenario, this quantity did not have units of biomass---as required for the policy's parameters to be correctly defined.
This together with general comment C. above led us to redefine the cPP policy. 
We now explicitly define the parameters $F^*$ and $B^*$ used to compute the cPP policy in Sec. 2.4 (**lines X**).
The procedure by which $B^*$ is computed, and the value obtained, is described in the caption of Table 1.

11. Figure references should now be corrected, thank you.
