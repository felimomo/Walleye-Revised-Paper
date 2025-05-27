---
title: "Appendix B: Productivity CDF"
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
         output: appendixB.pdf
---

In the main text we optimize fixed policy parameters using Bayesian optimization, a process in which we used 250 episodes to estimate the average episode utility obtained by a policy.
We may use the population's *episode producitivity*,
$$
  \pi = \sum_{t=1}^{1000} N_1(t),
$$
as a proxy for utility because, for any given policy, increasing the productivity of an episode leads to increasing utility obtained in that episode.
In {ref}`fig:app-cdf` we display a cumulative distribution function of episode productivities for a sample 250 episodes.
The empirical cdf curve can be visually inspected to see that it is approximately Gaussian.
We plot the maximum likelihood Gaussian distribution obtained from the data for comparison, with parameters 
\begin{align}
  \mu_{MLE} &= \sum_{i=1}^{250} \pi_i / 250,\\
  \sigma_{MLE}^2 &= \sum_{i=1}^{250} (\pi_i - \mu_{MLE})^2 / 250,
\end{align}
where $\pi_i$ is the episode productivity for the $i$-th episode.
This approximate Gaussian behavior suggests that the empirical cdf has approximately converged since $\pi$ is itself approximately Gaussian (it is the sum of 1000 independent and identically distributed random variables).

```{figure} figures/appendix/cdf.png
:name: fig:app-cdf
:label: fig:app-cdf
:width: 50000px
:align: center

Cumulative distribution function (cdf) for the empirical distribution of episode productivities $\pi$ over 250 episodes (blue line).
Additionally we plot the Gaussian maximum likelihood estimate for the productivity data (orange line).
```
