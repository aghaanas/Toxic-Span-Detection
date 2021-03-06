# Motivation

The primary objective of this repo is to understand the Toxic Span Detection problem and has used the implementation given in the paper: https://arxiv.org/pdf/2110.03730v1.pdf. You can find the source code here: https://github.com/erikdyan/toxic_span_detection.

# Toxic Span Detection

[Toxic Span Detection](https://sites.google.com/view/toxicspans) is a task at [SemEval 2021](https://semeval.github.io/SemEval2021/). It concerns the evaluation of systems that detect the spans that make a text toxic, when detecting such spans is possible.

## The Task

Systems will have to extract a list of toxic spans, or an empty list, per text. A toxic span is defined as a sequence of words that contribute to the text's toxicity. Consider the following text:

* "This is a <ins>stupid</ins> example, so thank you for nothing <ins>a!@#!@.</ins>"

It contains two toxic spans: "stupid" and "a!@#!@", which have character offsets from 10 to 15 and from 51 to 56 respectively. Systems are expected to return the following list for this text:

* [10, 11, 12, 13, 14, 15, 51, 52, 53, 54, 55, 56]

In some toxic posts the core message being conveyed may be inherently toxic and, hence, it may be difficult to attribute the toxicity of those posts to particular spans. In such cases, the corresponding posts may have no toxic span annotations. For example:

* "So tired of all these Portlanders moving to California and ruining the culture. When will it stop?!?"

## The Project

This project investigates several approaches for extracting toxic spans from text. Modifications to the loss function, the hidden output layer, and the classification layer were explored. A pretrained DistilBertForTokenClassification model was fine-tuned and used as a baseline. The different methods include:

* DistilBert + class weighting: Assigns a weight to each class ("B-toxic", "I-toxic", and "Other"). Larger weights result in harsher penalties when incorrectly predicting samples from that class
* DistilBert + TF-IDF: Appends the TF-IDF weight of each token to its hidden output vector from the base DistilBert model
* DistilBert + wordlist: Appends a wordlist feature to the hidden output vector of each token from the base DistilBert model. A value of "1" is appended if the token appears in the wordlist (`data/wordlist.txt`), otherwise a value of "0" is appended
* DistilBert + CRF: Adds a CRF on top of the fully connected layer

## Running the Project

`pip install -r requirements.txt` will install all the necessary libraries needed to run the project.

`run_ner.py` inside the `src` folder contains all code for this project. You can fine-tune, evaluate, and test a classification task as follows:

```
python run_ner.py data/tsd_train.csv --do_train --model_output_dir models/sample_model/
python run_ner.py data/tsd_trial.csv --do_eval --model_name_or_path models/sample_model/ --result_output_dir results/sample_model/
python run_ner.py data/tsd_test.csv --do_test --model_name_or_path models/sample_model/ --result_output_dir results/sample_model/
```

`NLP_Final_Project_Farzeem_Agha_Group2.ipynb` contains the above commands in a Google Colab environment along with detailed explanation of the implementation provided in the original paper, as well as a comparison analysis with another approach mentioned in this paper: https://arxiv.org/pdf/2103.09645v2.pdf.

### Additional Flags

* `--class_weights CLASS_WEIGHTS CLASS_WEIGHTS CLASS_WEIGHTS`: weights to assign to each class ("B-toxic", "I-toxic", and "Other", respectively)
* `--tfidf`: appends the TF-IDF weight of each token to its hidden output vector
* `--wordlist`: appends a wordlist feature (True/False) to the hidden output vector of each token
* `--crf`: adds a CRF on top of the fully connected layer
