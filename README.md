# Reward Learning for Efficient Reinforcement Learning in Extractive Document Summarisation

This project includes the source code accompanying the following paper:

```
@InProceedings{gao:2019:ijcai:relis,
  author    = {Yang Gao and Christian M. Meyer and Mohsen Mesgar and Iryna Gurevych},
  title     = {Reward Learning for Efficient Reinforcement Learning in Extractive Document Summarisation},
  booktitle = {Proceedings of the 28th International Joint Conference on Artificial Intelligence {(IJCAI)}},
  month     = August,
  year      = {2019},
  address   = {Macao, China}
}
```

> **Abstract:** Document summarisation can be formulated as a sequential decision-making problem, which can be solved by Reinforcement Learning (RL) algo- rithms. The predominant RL paradigm for summarisation learns a cross-input policy, which requires considerable time, data and parameter tuning due to the huge search spaces and the delayed rewards. Learning input-specific RL policies is a more efficient alternative but so far depends on handcrafted rewards, which are difficult to design and yield poor performance. We propose RELIS, a novel RL paradigm that learns a reward function with Learning-to-Rank (L2R) algorithms at training time and uses this reward function to train an input-specific RL policy at test time. We prove that RELIS guarantees to generate near-optimal summaries with appropriate L2R and RL algorithms. Empirically, we evaluate our approach on extrac- tive multi-document summarisation. We show that RELIS reduces the training time by two orders of magnitude compared to the state-of-the-art models while performing on par with them.

Contact person: Yang Gao, yang.gao@rhul.ac.uk

https://sites.google.com/site/yanggaoalex/home

Don't hesitate to send us an e-mail or report an issue, if something is broken (and it shouldn't be) or if you have further questions

Disclaimer:
> This repository contains experimental software and is published for the sole purpose of giving additional background details on the respective publication.


# System Overview
APRIL is an interactive document summarisation framework. Instead of learning from reference summaries, APRIL interacts with the users/oracles to obtain preferences, learns a ranking over all summaries from the preferences, and generates (near-)optimal summaries with respect to the learnt ranking.

APRIL has three stages:
* Sample summaries (stage0): randomly generate some summaries and compute their rouge scores. The ROUGE scores are used to simulate users' preferences
* Active preference learning (stage1): actively query the oracle for multiple rounds, collect preferences and learn a ranking over summaries from the preferences.
* Reinforcement learning (stage2): read the ranking learnt in stage1 and generate the highest-ranked summary


## Prerequisties
* We suggest you to use IDEs like PyCharm to set up this project, so that the package paths are managed automatically.
* Python3 (tested with Python 3.6 on Ubuntu 16.04 LTS and MacOS Mojave)
* Install all packages in requirement.txt.

        >> pip3 install -r requirements.txt

* Download ROUGE package from the [link](https://www.isi.edu/licensed-sw/see/rouge/) and place it in the rouge directory

        >> mv RELEASE-1.5.5 summariser/rouge/

* Download the Standford Parser models and jars from the [link](https://nlp.stanford.edu/software/lex-parser.shtml)
and put them to summariser/jars

		>> mv englishPCFG.ser.gz germanPCFG.ser.gz summariser/jars/
		>> mv stanford-parser.jar stanford-parser-3.6.0-models.jar summariser/jars/


## Download and Preprocess data
* Download the DUC01/02/04 data from [the DUC website](https://duc.nist.gov/data.html) and extract the data to folder 'data/'.
* Run summariser/data_processer/make_data.py. Each run preprocesses one dataset. You can specify the dataset you want to process by setting the variable 'corpus_name' in the main function to appropriate values (e.g., 'DUC2001', 'DUC2002' or 'DUC2004'). The preprocessed data will be put in data/processed_data.

## Stage0: Generate Sample Summaries
* Run 'stage0_sample_summaries.py' will generate the samples
* Setting 'dataset' in the main function to the dataset you want to generate summaries for
* Setting 'summary_num' in the main function to the number of samples you want to generate for a dataset. Its default value is 10001.
* The generated summaries will be put in data/sampled_summaries


## Stage1: Active Preference Learning
* Run 'stage1_active_pref_learning.py'
* Setting 'dataset' to the dataset you want to run the experiment (DUC2001, DUC2002, DUC2004)
* You can select the following active learning algorithms, by setting 'querier_type' to appropriate values:
    * random sampling + BT (set querier_type to 'random'): randomly select two summaries to query the oracle in each interaction round; use the linear BT model to obtain the ranking
    * gibbs distribution + BT (set querier_type to 'gibbs'): select a relative good and a relatively bad summary to query the user in each round; ranking is obtained using the same linear BT method.
    * uncertainty sampling + BT (set querier_type to 'unc'): estimate the uncertainty level of each summary in the sampled summary pool, select the most uncertain pair to query and use BT to obtain the ranking.
* To write the learnt ranker for use in Stage2, you can turn on the writing functionality by giving 'write_learnt_reward' True. The learnt ranker will be written to directory 'learnt_ranker'.

## Stage2: Reinforcement Learning
* Since TD performs much better than LSTD, in the current implementation only TD is included. 
* choose reward type: 
    * hueristic (baseline, no interaction)
    * rouge (upper bound, because reference summaries are not available during interaction)
    * learnt (using the ranker learned in stage1 to give rewards). If you go for this option, you also need to sepcify which learnt ranker you want to use by setting 'learnt_rank_setting' to appropriate values. Example settings can be found in the source code.

## License
Apache License Version 2.0


