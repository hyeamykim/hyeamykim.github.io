---
title: "How to Measure Bias in Word Embeddings"
date: 2024-12-02T16:51:15+01:00
draft: false
Description: ""
Tags: ["word embeddings","social bias", "debiasing"]
Categories: []
DisableComments: false
math: true
---

## Introduction
It has been well-known that word embeddings trained with human-written corpus reflect harmful biases (Bolukbasi et al., 2016).
Bias os social bias, in general, can be understood as an oversimplified view or prejudiced attitude towards a particular type of person. In the context of NLP, social bias includes disparate treatment or outcomes between different social groups (Gallegos et al., 2023). For example, stronger associations of certain groups of people (e.g., women) to certain attributes (e.g., domestic professions such as homemakers) that indicate such oversimplified views can be found in word embeddings. There have been many studies to inspect and mitigate bias in word embeddings since such bias can be propagated in various downstream tasks that use word embeddings as features and harm minorities in the decision-making process. In this post, various methods and datasets devised to measure such bias in word embeddings will be explained. These are mostly used to evaluate debiasing methods by comparing the bias metric before and after debiasing.

### Disclaimer
- This post covers static word embeddings, like Word2Vec or GloVe, that provide a fixed mapping between words and vector representations, regardless of the context.
- The metrics explained here came from literature covering debiasing gender bias in word embeddings, but they could be used for detecting other types of social biases.
- The metrics and datasets mentioned here are not comprehensive and are just a quick introduction to gender bias measurement in static word embeddings.
- This post was first published on [Medium](https://medium.com/@hyeamykim/how-to-measure-bias-in-word-embeddings-e48c705221db) 

## Bias Mitigation

Many debiasing techniques are based on geometric projection in the embedding space and use the cosine distances between words for detecting and removing bias. This indicates that bias is associated with geometric asymmetry between the words in the embedding space. (James and Alvarez-Melis, 2020) Specifically, words projected onto a gender subspace, which can be defined from a set of predetermined gender-definitional word pairs such as he-she, man-woman, king-queen, etc., are used to study the asymmetry between some target words and gender-definitional words.

## Bias Measurement Methods

### Word Embedding Association Test (WEAT) (Caliskan et al., 2017)

WEAT quantifies biases regarding gender, race, age, etc., using semantic similarities between word embeddings.

The score is calculated by comparing cosine similarity values between sets of target words X and Y (e.g., European and African names) with two sets of attribute words A and B (e.g., pleasant vs. unpleasant), and is defined as follows:

$$s(X, Y, A, \mathcal{B}) = \sum_{x \in X} k(x, A, \mathcal{B}) - \sum_{y \in Y} k(y, A, \mathcal{B})$$

$$k(t, A, \mathcal{B}) = \text{mean}_{a \in A} f(t, a) - \text{mean}_{b \in \mathcal{B}} f(t, b)$$

Where f is cosine similarity.

For all words x in a target group X, the mean value of cosine similarity values between x and all words a in an attribute group A and the mean value of cosine similarity values between x and all words b in an attribute group B are calculated. The difference between the cosine similarity values is then calculated. The sum of all the differences for all words in a target group X is the measure that indicates whether target group X words are more similar to — more associated with — attribute group A or B on average. The same values are calculated for all words y belonging to a target group Y. Then, the difference between the measure for target groups X and Y is the final score for indicating bias.

The score ranges between -2 and 2, and when the score is close to zero, we can say that neither target is more strongly associated with a certain attribute. A high positive score tells us target X is more strongly associated with attribute A, and a high negative score indicates target Y is more strongly associated with attribute A.

### Word Association Test (WAT) (Du et al., 2019)

In WAT, the gender information in a word association graph created with Small World of Words (SWOWEN) is calculated to get a sense of gender bias over a large vocabulary (Deyene et al., 2019).

The gender information vector for each word is represented as a 2-dimensional vector that contains the masculine and feminine orientations of a word. A set of gender-specific word pairs L, which consists of masculine and feminine words, is pre-defined.

P is the matrix containing vectors of all words. For the gender-specific words in L, the vector for masculine words is initialized with (1, 0) and feminine words with (0, 1). The vector for other words is initialized with (0, 0). Then, the gender information vector is propagated using a random walk until convergence

$$P_{t+1} = \alpha T P_t + (1 - \alpha) P_0,$$

$$T = D^{-\frac{1}{2}} S D^{-\frac{1}{2}}, \quad D = \text{diag}_i \left( \sum_{j=1}^N S_{ij} \right)$$

where T is the normalized adjacency matrix of the word association graph, S is the adjacency matrix, and α is a hyperparameter in range (0, 1) which balances global and local consistency. Small α indicates higher global consistency which values keeping the initial vectors of words and large α means higher local consistency, which values the smoothness between connected words (i.e. close words in a graph should have similar labels).

After the propagation, the bias score of a word is calculated as

$$b = \log \frac{\overline{b}_m}{\overline{b}_f}$$


### Neighborhood metric (Gonen and Goldberg, 2019)

The neighborhood bias metric quantifies bias as the percentage of male socially biased words among the k nearest socially biased male- and female-neighboring words in the embedding space, where words are projected onto a gender subspace.

It was proposed by Gonen and Goldberg (2019), based on the idea that bias still can be observed with the clustering of socially-marked gendered words. For example, even after debiasing, the word “nurse” might not be close to gender-definitional feminine words in the embedding space, but might still be close to other socially-biased female words such as “receptionist,” “caregiver,” and “teacher.”

In James et al. (2020), the 1,000 most socially-biased words (500 male and 500 female) in the vocabulary were used and a word’s bias was measured as the ratio of its neighborhood of socially-biased male and socially-biased female words. So a value of 0.5 in this metric would indicate a perfectly unbiased word, and values closer to 0 and 1 indicates stronger bias.

### Relational Inner Product Association (RIPA) (Ethayarajh et al, 2019)

RIPA was suggested as an alternative to WEAT. It quantifies bias as the inner product association of a word vector v with respect to a relation vector b, which is constructed from the first principal component of the differences between gender word pairs. In other words, RIPA is the scalar projection of a word vector onto a one-dimensional bias subspace defined by the unit vector b.

RIPA is an intuitive measure, which allows us to directly compare the actual word association in embedding space with what we would expect the word association to be, given the training corpus.

### WinoBias (Zhao et al., 2018a)

The WinoBias dataset was devised to evaluate coreference resolution tasks and contains winograd-schema-style sentences with entities corresponding to people referred to by their occupations. It consists of two types of sentences that link gendered pronouns to either male or female stereotypical occupations.

Type 1: [entity1] [interacts with] [entity2] [conjunction] [pronoun] [circumstances].

Type 2: [entity1] [interacts with] [entity2] and then [interacts with] [pronoun] for [circumstances].

In Type 1, co-reference decisions must be made using world knowledge about some given circumstances, and in Type 2, co-reference can be resolved with syntactic information and an understanding of the pronouns.

The dataset can be also categorized by pro-stereotyped sentences, which link pronouns to occupations dominated by the gender of the pronoun, and anti-stereotyped sentences, which link pronouns to occupations not dominated by the gender of the pronoun.

The bias in word embeddings can be measured by comparing the co-reference performance for pro-stereotyped and anti-stereotyped sentences both Type 1 and Type 2. Ideally, the co-reference decision should be made with the same accuracy.

### SemBias (Zhao et al., 2018b)

SemBias dataset was devised by Zhao et al. (2018b), inspired by SemEval2012 Task2, to study gender information in word embeddings. It contains three types of word pairs: (1) Definition, which are gender-definition word pairs (e.g., hero — heroine), (2) Stereotype, which are gender stereotype word pairs (e.g., manager — secretary), and (3) None, which are two other word-pairs (e.g., jazz — blues, pencil — pen). The designed goal is to identify the correct analogy he-she from these four pairs of words.

With the SemBias dataset, bias in word embeddings can be evaluated by comparing word embedding’s performance on identifying he-she analogy from the definition set and from the stereotype set, for example. Another way is to calculate the cosine similarity between the gender directional vector and the definition set and the stereotype set, respectively (Kaneko and Bollegala, 2021).

## References
- Man is to Computer Programmer as Woman is to Homemaker? Debiasing Word Embeddings, Bolukbasi et al., 2016, https://arxiv.org/pdf/1607.06520.pdf
- Semantics derived automatically from language corpora contain human-like biases, Caliskan et al., 2017, https://arxiv.org/pdf/1608.07187.pdf
- Gender bias in coreference resolution: Evaluation and debiasing methods, Zhao et al., 2018a, https://arxiv.org/pdf/1804.06876.pdf
- Learning Gender-Neutral Word Embeddings, Zhao et al., 2018b, https://arxiv.org/pdf/1809.01496.pdf
Understanding Undesirable Word Embedding Associations, Ethayarajh et al., 2019, https://arxiv.org/pdf/1908.06361.pdf
- Lipstick on a pig: Debiasing methods cover up systematic gender biases in word embeddings but do not remove them, Gonen and Goldberg, 2019, https://arxiv.org/pdf/1903.03862.pdf
- Exploring Human Gender Stereotypes with Word Association Test, Du et al., 2019, https://aclanthology.org/D19-1635.pdf
- The “Small World of Words” English word association norms for over 12,000 cue words, Deyne et al., 2019, https://link.springer.com/article/10.3758/s13428-018-1115-7
- Probabilistic Bias Mitigation in Word Embeddings, James and Alvarez-Melis, 2020, https://arxiv.org/pdf/1910.14497.pdf
- Dictionary-based Debiasing of Pre-trained Word Embeddings, Kaneko and Bollegala, 2021, https://arxiv.org/pdf/2101.09525.pdf
- Bias and Fairness in Large Language Models: A Survey, Gallegos et al., 2023, https://arxiv.org/pdf/2309.00770
