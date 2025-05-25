---
title: "Appendix A: smoothed observations"
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
         output: appendixA.pdf
---

In the main text we analyze RL policies which use (noisy) observations of the stock biomass and mean fish weight.
Due to the stochasticity in the observation process, the policy responses (fishing mortalities) are correspondingly noisy (as seen in the time-series simulations presented in the main text).
We also explored the use of moving averages to smooth observations, i.e. of policies having access to quantities of the form
$$
  \overline{obs}_t = (obs_t + \dots + obs_{t-k}) / k,
$$
where $k$ is the size of the time window.

Here we will characterized a 2-observation RL policy trained on such observations with $k=3$ in the trophy fishing scenario, which we denote 2RL$_3$ here.
Such a small value of $k$ was deemed necessary in order to capture the quick observation fluctuations that arise from the spasmodic stochasticity of the system, although we did not systematically test different values of $k$.
While we did experiment with smoothing on other scenarios, only in this scenario did smoothing have a measurable (if small) effect on the reward distribution.

In {ref}`fig:app-rewards` we can see that 2RL$_3$ slightly outperforms 2RL.
This improvement is rather small and, on it's own, it would hardly be convincing enough to choose one policy over the other.
This change in policy, however, also leads to a smoother policy curve as a function of time, as can be observed in {ref}`fig:app-timeseries`.
Thus, this policy maintains (if, ever so slightly, improves) the utility obtained by 2RL, while reducing to an extent the interannual variability in fishing mortality present in the 2RL time-series.
We further visualize the two policies, 2RL and 2RL$_3$, as heatmaps over the 2-dimensional space of observations in {ref}`fig:app-normal-policy` and {ref}`fig:app-smoothed-policy`.

```{figure} figures/appendix/rewards.png
:name: fig:app-rewards
:label: fig:app-rewards
:width: 50000px
:align: center

Reward distributions for the RL policy trained on moving averages of observations with $k=3$, together with the reward distribution for the RL policy characterized in the main text.
The distributions are computed with $n=500$ episodes, and the average rewards for each distribution are shown as dotted lines (black for 2RL$_3$ and blue for 2RL).
```

```{figure} figures/appendix/time-series.png
:name: fig:app-timeseries
:label: fig:app-timeseries
:width: 40000px
:align: center

A timeseries for the 2RL$_3$ control. 
We show the two observations used by the policy (smoothed with $k=3$), together with the fishing mortality predicted by the policy.
```

```{figure} figures/appendix/normal-policy.png
:name: fig:app-normal-policy
:label: fig:app-normal-policy
:width: 50000px
:align: center

Heatmap for 2RL as a function of the two observations (biomass and mean weight).
```

```{figure} figures/appendix/smoothed-policy.png
:name: fig:app-smoothed-policy
:label: fig:app-smoothed-policy
:width: 50000px
:align: center

Heatmap for 2RL$_3$ as a function of the two observations (moving averages of biomass and mean weight).
```
