---
permalink: /projects/
title: Projects
---

## Feature Selection for Mixed Models
*With the guidance of Dr. Aleksander Aravkin.*

![skmixed](/assets/images/skmixed.png)

For my PhD thesis, I develop feature selection methods that work well on ill-conditioned data. Such data arises when the features are correlated or when they contain missing values and outliers. To stabilize model training on such data I use optimization methods that have implicit regularization properties. For instance, [SR3](https://arxiv.org/pdf/1807.05411.pdf) methods implicitly smooth the level-sets of the loss-functions via partial minimization. I used it to create a stable feature-selection method for linear mixed-effects models. Check out my implementation of it [here](https://github.com/aksholokhov/skmixed).

## COVID-Project
*Together with [IHME Math Sciences Team](https://github.com/ihmeuw-msca) and 130+ colleagues across IHME.*
![memoir](/assets/images/covid_projections.png)

Together with IHME Math Sciences Team, I worked on the math behind [IHME SEIIR COVID Model](http://www.healthdata.org/sites/default/files/files/Projects/COVID/Estimation_update_050420.pdf), which has been [in use](https://covid19.healthdata.org/projections) staring May, 5th, 2020. We made one of the first COVID-19 models that attracted attention of decision makers across the world and provided guidance for the distribution of ICUs during the first surge of the pandemic.

## MEMOIR
*Jointly with Yury Maximov, Massih-Resa Amini, and Anton Belyy.*

![memoir](/assets/images/memoir_comparison.png)

In this project we use novel similarity search techniques to bring multi-class SVM to the realm of extra-large datasets (billions or objects, hundreds of thousands of classes). In particular, we integrated maximal inner product search search techniques like SimpleLSH and SmallWorldGraph into the Pegasos SVM. It boosted the convergence speed and gave us a flexible memory-precision trade-off mechanism. See the [preprint](https://arxiv.org/abs/1811.09863) for more details.

## Bandits with Knapsacks for Demand Control in Power Grids
*Jointly with Yury Maximov, Michael Chertkov, and Michail Kretchetov*

![tcl](/assets/images/demand_response.png)

In this work we used reinforcement learning to control electricity demand of an ensemble of thermostatic appliances. Mathematically, we modelled the loads with Markov chains and then trained a multi-armed bandit to control them by sending centralized policies. The main challenge was that the bandit had a limited budget and he was charged by the loads for every consumption curtailment act. We adapted a linear contextual bandit with a knapsack to this Markov chains setting, and it showed a better performance comparing to existing solutions on synthetic experiments. More details are in my [bachelor thesis](https://github.com/aksholokhov/bachelor_thesis).

## RySearch
*Jointly with Anton Belyy, Mariia Selezniova, and Konstantin Vorontsov.*

![rysearch](/assets/images/rysearch.png)

We used a hierarchical topic modeling technique called [BigARTM](https://github.com/bigartm/bigartm) to create an exploratory search engine for large heterogeneous corpora of Russian scientific texts. We created a [prototype](https://github.com/AVBelyy/Rysearch/graphs/contributors) of such an engine, and we also created [several new metrics](http://www.dialog-21.ru/media/4562/belyyavplusetal.pdf) of quality for hierarchical topic models. You can play with the prototype [here](https://rysearch.retloko.org)!