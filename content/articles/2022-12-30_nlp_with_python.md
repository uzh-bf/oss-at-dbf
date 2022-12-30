---
title: NLP with Python
date: 2022-12-30
author: Francesco Tinner
---

# NLP with Python

This file contains an overview of methods for NLP with Python in a first part, and a second part that discusses the specifics of the experiments which evaluate the possibilities of automatical grouping of open answers.

Natural Language Processing (NLP) is a field of computer science and linguistics that deals with how computers can understand, interpret, and generate human language. It involves using computers to process and analyze large amounts of natural language data (e.g., text, speech) in order to perform tasks such as language translation, text summarization, and named entity recognition.

In Python, there are several libraries and frameworks that can be used for natural language processing tasks. Some examples include:

- NLTK (Natural Language Toolkit) [https://www.nltk.org/](https://www.nltk.org/): A library for processing natural language data that includes tools for tokenization, part-of-speech tagging, stemming, and more.
- spaCy ([https://spacy.io/](https://spacy.io/)): A library for advanced natural language processing that includes support for named entity recognition, dependency parsing, and text classification.
- Gensim ([https://radimrehurek.com/gensim/](https://radimrehurek.com/gensim/)): A library for topic modeling and document similarity analysis, which can be useful for tasks such as text summarization and recommendation systems.

To use these libraries and frameworks for natural language processing tasks, you typically start by preprocessing the raw text data by performing tasks such as tokenization and stemming. Then, you can apply various NLP techniques and algorithms to extract useful information from the text, such as identifying named entities or analyzing the sentiment of a piece of text. Finally, you can use this extracted information to perform a specific task, such as translation or text classification.

## Text Representation Techniques:

Methods that allow to encode text to numerical features.

### Word Embeddings

The idea behind word embeddings is to capture the meaning of a word in a way that is more expressive and meaningful than just representing words as a one-hot encoded vector. Word embeddings are used for mapping words to vectors of real numbers. A word embedding vector should reflect the structure of the word in terms of morphology (\[Enriching Word Vectors with Subword Information\]([https://arxiv.org/pdf/1607.04606.pdf](https://arxiv.org/pdf/1607.04606.pdf) )), word-contexts (\[word2vec Parameter Learning Explained\]([https://arxiv.org/pdf/1411.2738.pdf](https://arxiv.org/pdf/1411.2738.pdf) )), global corpus statistics (\[GloVe: Global Vectors for Word Representation\]([https://nlp.stanford.edu/pubs/glove.pdf](https://nlp.stanford.edu/pubs/glove.pdf) )) or word hierarchy based on WordNet terminology (\[Poincaré Embeddings for Learning Hierarchical Representations\]([https://arxiv.org/pdf/1705.08039](https://arxiv.org/pdf/1705.08039) )).

In order to represent words or phrases in a vector space with several dimensions. Word embeddings can be generated using various methods such as neural networks, co-occurrence matrix, probabilistic models, etc. Depending on the method, the dimensions are interpretable (Bag-of-words (BOW), or one-hot encoding), but most often this is not the case, especially for pre-trained embeddings.

Word embeddings are a way of representing words in a numerical format that can be fed into a machine learning model. A pre-trained word embedding is a set of word embeddings that have been trained on a large dataset and can then be used as a starting point for training on a new task. Pre-trained word embeddings have the advantage of already having captured the meaning of words in a useful way, so they can be used to help improve the performance of a machine learning model on a new task.

There are several popular pre-trained word embedding models available, including word2vec, GloVe, and FastText. These models can be used to convert words into numerical vectors, which can then be used as input to a machine learning model. For example, you might use a pre-trained word embedding to convert a sentence into a sequence of numerical vectors, which could then be fed into a neural network for language translation or text classification.

### Pre-trained Word Embeddings

A pre-trained word embedding is a set of word embeddings that have been trained on a large dataset and can then be used as a starting point for training on a new task. Pre-trained word embeddings have the advantage of already having captured the meaning of words in a useful way, so they can be used to help improve the performance of a machine learning model on a new task.

There are several popular pre-trained word embedding models available, including word2vec, GloVe, and FastText. These models can be used to convert words into numerical vectors, which can then be used as input to a machine learning model. For example, you might use a pre-trained word embedding to convert a sentence into a sequence of numerical vectors, which could then be fed into a neural network for language translation or text classification.

#### Word2Vec:

first neural embedding model, learns word embeddings by predicting a blanked out word following a given context.

Good, visual explanation: [https://jalammar.github.io/illustrated-word2vec/](https://jalammar.github.io/illustrated-word2vec/)

![](https://lh6.googleusercontent.com/IhosNhVplUxqWckj14KS9EdQKgVLFxxa0yHG-MOvPoEODF1AvAgpt2vgcMvPPak-IYzHUuQ8B7s1aVmzRFa7xcPZQcFuw75JK3SQTlr7DxekpNM7LkT3Uy7j14psitGhCfXVAByEoMOg0vb2CFN7mAVEjKdcrZ9yHBm1fMqrCnf_WEPqKCjAu_dHyqYH-A)

####

#### Sentence Transformers

Pre-trained sentence embedding models: [https://www.sbert.net/docs/pretrained_models.html](https://www.sbert.net/docs/pretrained_models.html)

Encoding of textual answers that uses a transformer based neural network that takes the answer text as input and outputs a sentence embedding consisting of 768 features. Fine-tunes a BERT-like language model on the tasks of sentence similarity prediction ([https://www.sbert.net/docs/training/overview.html](https://www.sbert.net/docs/training/overview.html)). The implementation is available as a python package ([https://pypi.org/project/sentence-transformers/](https://pypi.org/project/sentence-transformers/))

## Clustering

In the context of natural language processing (NLP), clustering refers to the process of grouping a set of texts or documents into categories based on their similarity. This can be useful for various purposes, such as document classification, information retrieval, and topic modeling.

There are various clustering algorithms that can be used for this purpose, such as k-means clustering, hierarchical clustering, and density-based clustering. These algorithms work by comparing the similarity between the texts or documents and assigning them to clusters based on that similarity.

For example, in the case of k-means clustering, the algorithm begins by randomly selecting a set of k "centroids," or representative points, for the clusters. It then assigns each text or document to the cluster with the nearest centroid, based on a similarity measure such as cosine similarity. The algorithm then iteratively adjusts the position of the centroids and reassigns the texts or documents to clusters until convergence.

Clustering can be useful in NLP for tasks such as classifying documents into predefined categories, finding similar documents, or identifying the main topics in a collection of texts. It can also be used as a preprocessing step for other NLP tasks, such as information extraction or sentiment analysis.

In the case of clustering sentence embeddings, we can group similar answers together by assigning sentence vectors that are similar to, while keeping a significant portion of the high-dimensional local structure in the lower dimensionality.

### UMAP (Uniform Manifold Approximation and Projection)

[https://umap-learn.readthedocs.io/en/latest/#:~:text=Uniform%20Manifold%20Approximation%20and%20Projection%20(UMAP)%20is%20a%20dimension%20reduction,three%20assumptions%20about%20the%20data](<https://umap-learn.readthedocs.io/en/latest/#:~:text=Uniform%20Manifold%20Approximation%20and%20Projection%20(UMAP)%20is%20a%20dimension%20reduction,three%20assumptions%20about%20the%20data>)

UMAP is a dimensionality reduction algorithm that is used to reduce the number of dimensions in a dataset while preserving as much of the inherent structure of the data as possible. It is particularly well-suited for visualizing high-dimensional datasets and for finding patterns in the data that may not be immediately apparent in the high-dimensional space.

UMAP works by constructing a low-dimensional representation of the data that is both faithful to the structure of the original data and has a lower number of dimensions. It does this by finding a continuous map from the high-dimensional data to the low-dimensional space that preserves the distances between points as much as possible. This is achieved through the use of techniques from Riemannian geometry and topological data analysis.

To use UMAP, you need to specify the number of dimensions you want in the reduced representation, as well as a few other parameters such as the distance metric to use and the desired balance between local and global structure in the resulting map. Once these parameters are set, UMAP will construct a low-dimensional representation of the data by finding a continuous map that preserves the distances between points as much as possible. This map can then be used to visualize the data in the low-dimensional space, or to perform further analysis on the reduced data.

In our case, in order to group similar high dimensional answer representations together, we use a dimensionality algorithm that takes the sentence embeddings of 768 dimensions as input and reduces the representation to much fewer dimensions, in order to apply a clustering algorithm.

### HSBSCAN Clustering Algorithm

[https://hdbscan.readthedocs.io/en/latest/how_hdbscan_works.html](https://hdbscan.readthedocs.io/en/latest/how_hdbscan_works.html)

HDBSCAN (Hierarchical Density-Based Spatial Clustering of Applications with Noise) is an algorithm for clustering that is based on density. It is a density-based clustering algorithm, which means that it is able to identify clusters of different shapes and sizes, as well as handle noise and outliers.

The HDBSCAN algorithm works by building a tree-like structure called a dendrogram, which represents the hierarchy of clusters in the data. It then uses this dendrogram to identify clusters in the data by finding "clumps" of data points that are closely connected to each other.

One of the main advantages of HDBSCAN is that it does not require the user to specify the number of clusters in advance, as is the case with many other clustering algorithms. Instead, it is able to automatically determine the number of clusters based on the data itself.

HDBSCAN is often used for tasks such as cluster analysis, data mining, and anomaly detection. It is particularly useful for data sets that have a large number of noise points or outliers, or for data sets with clusters of different shapes and sizes. Furthermore, it works well with the dimensionality reduction algorithm UMAP, as it maintains the local structure in low-dimensional space (see paragraph before).

## Grouping of Similar Texts

Allows the computation of the similarity between multiple sentences or paragraphs by

1. Encoding the texts to numerical features, using sBERT.
2. Dimensionality reduction from 768 to e.g. 30  features per sentence / paragraph, or whatever the constraints of the clustering algorithms are.
3. Clustering using the reduced features, each point represents a sentence, and is assigned a cluster ID. Semantically identical or similar sentences are grouped together into a cluster and assigned an ID.
4. Dimensionality reduction to two or three dimensions, while keeping the assigned cluster IDs of the higher dimension allows the visual inspection of the clusters. For visualization bokeh: [https://docs.bokeh.org/en/latest/](https://docs.bokeh.org/en/latest/) is used.

## Feed-forward neural network, generalities and learning procedure

A feedforward network is a type of neural network that consists of layers of interconnected nodes, or "neurons," that process and transmit information. In a feedforward network for classification, the input data is passed through the network layer by layer, and the final output of the network is a prediction of the class to which the input belongs.

Here is a simple example of how a feedforward network for classification might work:

1. The input data is fed into the input layer of the network.
2. The input data is then processed by one or more hidden layers of the network, which apply a series of transformations to the data using weights and biases.
3. The output of the hidden layers is passed to the output layer of the network, which produces a prediction of the class to which the input belongs.
4. The prediction is compared to the true label of the input data, and the error is calculated using a loss function.
5. The weights and biases of the network are updated based on the error, using an optimization algorithm such as stochastic gradient descent.
6. The process is repeated for multiple input examples, and the network continues to learn and improve its performance as it processes more data.

In summary, a feedforward network used for classification is a way of training a machine learning model to predict the class of an input example based on its features. It does this by processing the input data through a series of hidden layers, which apply transformations to the data using weights and biases, and producing an output prediction based on the output of the hidden layers. The network is trained using an optimization algorithm that adjusts the weights and biases based on the error between the prediction and the true label of the input data.

### Pytorch general

[https://pytorch.org/docs/stable/index.html](https://pytorch.org/docs/stable/index.html)

PyTorch is an open-source machine learning framework for Python that is widely used for training deep learning models. It is designed to be easy to use and flexible, with a focus on providing good performance and high-quality implementations of popular deep learning algorithms.

One of the key features of PyTorch is its use of a dynamic computational graph, which allows the user to modify the graph on the fly and makes it easy to implement complex models with a high degree of flexibility. It also includes support for automatic differentiation, which allows the user to compute gradients of functions with respect to their inputs, making it easy to implement gradient-based optimization algorithms.

In addition to its core machine learning capabilities, PyTorch also includes a variety of tools and libraries for tasks such as data loading and preprocessing, model deployment, and visualization. It is widely used in both academia and industry, and has a large and active community of users and developers.

### Grading Prediction Implementation Details 

More info about classification: [https://scikit-learn.org/stable/modules/multiclass.html](https://scikit-learn.org/stable/modules/multiclass.html)

# Experiment Overview

## **Grouping of Similar Answers**

`src/notebooks/cluster_embeddings_approach1_and2.ipynb` compares clustering of embeddings of full open answers and embeddings of shortened open answers, where answers are split into sentences. The data is not modified, as the shortened answers are just encoded in the notebook. Both appraoches reduce the dimensionality of the data in a first step with UMAP, which improves the clustering results. Then clustering is performed using HDBSCAN. Finally, the dimensionality of the data is reduced to two, and the results are visualized.

### **Approach 1**:

In a first step, the embeddings of the full open answers are clustered using the stored embeddings in the dataframe. The clustering results seem not to be optimal enough to be used for the final model, as the quality of the results depends on the average text length of an answer.

### **Approach 2**:

In a second step, the answers are split into sentences. This also generates more data, which improves results naturally. The embeddings of the shortened open answers are clustered and the results are compared.

![](https://t4648007.p.clickup-attachments.com/t4648007/41109e6d-2b2a-4e9e-b5dd-5711ddeb1ac4/sentence_clustering.png)

## **Grading Decision Prediction**

Two approaches are implemented, both use the embeddings of the entire answer, but different machine learning models are used. Approach 1 uses a continuous multiputput regression model, which performed insufficient. Approach 2 uses a multi-layer feed-forward network, and turned out to be more promising.

1\. `src/notebooks/ans_grading_vector_prediction_mlpreg.ipynb` trains a continuous multioutput regression model to predict the grading decision vector based on the embedding of the entire answer. Requires a dataframe object generated using \`src/notebooks/helper_functions/parse_ans.py. This architecture was not used in the final model, as it performed worse than approach 2.

2\. `notebooks/grading_vector_prediction.py` trains a multi-layer feed-forward network to predict the grading decision vector based on the embedding of the entire answer. Requires a dataframe object generated using \`src/notebooks/helper_functions/parse_ans.py\`. Uses supervised multiclass-multioutput classification for learning grading predictions. In contrast to the explanations above, the setting is multiclass, and a class prediction is not binary, but the values need to be respected, as these represent the amount of points. The text is represented through sBERT features. The network then learns a non-linear mapping from the 768 dimensional embeddings to the grading decisions. These are represented by a vector, with each element being  a discrete number representing the amount of points that were given for a specific criterion / rubric. Evaluation of the model is based on a pairwise comparison of accuracy between the predicted grading decisions and the ground truth decisions on a  held-out test set.

## **Evaluation of OCR Quality and the possibility of Post-Correction**

`src/notebooks/ocr_text_post_correction.ipynby` requires an output of an OCR engine. The digitalized handwritten answer is then corrected for grammar and spelling mistakes using libre office's open-source spell checker.
