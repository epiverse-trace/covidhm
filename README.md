# Modelling transmission and control on real-world networks.

This repository is currently under development. It is a fork of the [{covidhm} repository](https://github.com/biouea/covidhm), which in turn was adapted from the [{ringbp}](https://github.com/epiforecasts/ringbp/) package. The aim of this new repository is to allow development for more general use cases beyond COVID, with the original repo remaining as the accompaniment to the initial 2020 published COVID analysis.

Unlike {ringbp}, which simulates using branching processes, the model in this repo allows simulation of outbreaks on empirical networks, and hence accounting for clustering of contacts – although [{simulist}](https://github.com/epiverse-trace/simulist) also includes an option to approximate some clustering effects). This makes it particularly well suited to outbreaks in closed settings and small communities. However, given active development happening on ringbp, which informed this network model, this repository should be considered a placeholder for now.

These are the two main published applications of this model:
- Analysis of COVID control measures using UK community network interactions ([Firth et al, Nature Med, 2020](https://www.nature.com/articles/s41591-020-1036-8)).
- Analysis of COVID control measures using Singapore cruise ship event network data ([Pung et al, Nature Comms, 2022](https://www.nature.com/articles/s41467-022-29522-y)). There is also an [accompanying repository with code for this analysis](https://github.com/rachaelpung/cruise_networks).

The below are the set-up instructions from the original {covidhm} repo.

## Usage

### Set up

Install the analysis: 

```r
Sys.setenv(R_REMOTES_NO_ERRORS_FROM_WARNINGS=TRUE)
devtools::install("covidhm-master",dependencies = TRUE)
```


### Run a single scenario and plot a network

Run a single instance of the outbreak model over 20 days and overlay the infection data onto a network. See `?plot_network` for details of parameters

```r
library(covidhm)

#Load association matrices
load("data-raw/am_list.RData")

#First item in the list is data across all days
m <- am_list[[1]]

#Plot network
plot_network(
am = m,
day = 20,
num.initial.cases = 1,
prop.asym = 0.4,
delay_shape =  1,
delay_scale = 1.4,
prop.ascertain = 0.9,
presymrate = 0.2,
R = 0.8,
outside = 0.001,
testing = FALSE,
s = 333,
isolation = FALSE,
secondary = FALSE,
tracing = FALSE,
quarantine = FALSE)

```



### Run a single scenario multiple times

Run a single scenario for 10 simulations. Use `?scenario_sim` for an explanation of parameters.

```r
library(covidhm)
library(ggplot2)

res <- scenario_sim(net = m, n.sim = 10, num.initial.cases = 1,prop.asym=0.4,
                             prop.ascertain = 0.9, cap_max_days = 70,
                             delay_shape = 1, delay_scale = 1.4, R = 0.8, presymrate = 0.2, scenario = "nothing",
                             testing = FALSE, outside = 0.001, distancing = 0)

# Plot of raw cumulative cases
ggplot(data=res, aes(x=week, y=cumcases,col = sim)) +
geom_line(show.legend = FALSE, alpha=0.6, aes(group = sim)) +
scale_y_continuous(name="Weekly number of cases") +
  theme_bw()

```

### Run the full analysis

Run the analyses in the terminal with the following commands (NB - these take several hours):

```bash

Rscript inst/scripts/scenarios.R
Rscript inst/scripts/network.R
Rscript inst/scripts/distancing.R
Rscript inst/scripts/outside.R
Rscript inst/scripts/testing.R
Rscript inst/scripts/sensitivity_testing.R

```

### Generate figures

Render figures with the following:

```bash
Rscript inst/scripts/figures.R

```
