---
permalink: /labs/lab3/
title: "Lab 3: Frequency and N-Grams"
---

## Starter Code

As always, grab the [starter code from GitHub Classroom](https://classroom.github.com/a/E3y434Yj).

## Overview

This lab provides the following starter files:

- `PCLDataReader.py`
- `pcl_main.py`

We also provide a minimalist test suite for the classes you will be implementing:

- `test-pcl.py`

As always, the tests are mostly just to ensure that your code is following the specifications so that you don't end up doing analysis based on results that are way off from what we intended. They are not meant to catch every possible bug, and you should feel free to do your own testing (and, if you do so, talk about it in your journal).

## Introduction

In Lab 2, you explored some sample data from the [Semeval 2022 shared task on Patronizing and Condescending Language Detection](https://www.aclweb.org/portal/content/semeval-2022-shared-task-patronizing-and-condescending-language-detection), and did some basic comparisons of condescending versus neutral language. But as we discussed in class, oftentimes we want to go beyond a _descriptive_ account of how two classes (e.g., condescending and neutral) differ from each other, and instead take a _predictive_ approach: given a piece of text, can we estimate the likelihood that this text is condescending? That is the problem you will tackle, with the help of the _full_ Semeval 2022 Patronizing and Condescending Language (PCL) dataset, in this lab!

To do this _text classification_ task, we will make use of the Naive Bayes model we learned about in class. But rather than having you implement Naive Bayes from scratch, you will make use of scikit-learn's implementation, [MultinomialNB](https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.MultinomialNB.html). Instead, the focus of this lab will be on the _science_ of text classification: how do we apply a classifier to (possibly messy) real-world data, and how can we draw conclusions from the results?

Reminder: the PCL dataset is provided in XML format. Individual examples in the dataset look something like this:
```
  <example id="@@1824078" category="poor-families" country="tz" condescension="true" score="4">Camfed would like to see this trend reversed . It would like to see more girls in school . Basic Education Statistics in Tanzania ( BEST 2010 ) show that only 18 percent of girls have completed secondary school education . This is why Camfed supports girls from poor families to obtain secondary education and its efforts have seen many go to university .</example>
  <example id="@@1921089" category="refugee" country="tz" condescension="false" score="0">Kagunga village was reported to lack necessary social services to meet the growing demand of refugees . The village has neither reliable , clean and safe water nor sanitation facilities that include latrines and critical medical services .</example>
```

You may want to manually look through some of the data before you begin, in order to familiarize yourself with its structure.

## Part 1: Understanding (and commenting) the Starter Code

In previous labs, the starter code has mostly just been a blank skeleton, and you've been asked to fill in most of the actual code. However, as we progress through the semester, we'll be covering more and more high-level concepts that depend on the more foundational concepts covered in the first few weeks (and labs). For example, pretty much every lab will depend on tokenization. But if you had to write the same tokenization function over and over again, that would be a very inefficient use of your time! Therefore, starting from Lab 3, there will be a bit more "meat" to the starter code. The starter code will come with several already-implemented or mostly-implemented functions. These functions will either implement basic concepts that you've already implemented before in the past, or act as helper functions that do stuff that is required by the code but is tangential to the main learning outcomes.

Of course, it's important to not just blindly use code that you find on the internet! Before you start any lab, you should make sure you have read the provided starter code and fully understand what it's doing.

To enforce this understanding, this week your first task will be to **add comments to `PCLDataReader.py`**, including docstrings for every function. We will be reading your comments and they will contribute to your completeness and correctness scores! Therefore, you should aim to write comments that prove to us that you fully understand what the code is doing.

The code may make use of Python features or library calls that are unfamiliar to you. This is by design&mdash;if you see syntax or a function that you haven't seen before, you should look it up. The ability to get comfortable with code that looks unfamiliar is very important for doing NLP research (and research in general)...and will also come in handy when it comes time for the final project. If you encounter things that are unfamiliar or confusing, be sure to make a note of it in your journal, and cite whatever resources you used to figure it out!

In particular, the following functions / syntax may be new to some of you, and you should take extra care to make sure your comments and journal demonstrate that you have achieved full understanding of them:

- `islice`
- `yield`
- `ABC`
- `@abstractmethod`
- scikit-learn `DictVectorizer`

After you've finished going through the starter code, use your understanding of the code to answer the following analysis questions:

### Analysis Question #1

Instead of directly returning a list of examples, `do_xml_parse` uses the `yield` keyword. What does this keyword do, and why have we chosen to use it? Your answer **must** directly address the issue of resource usage (e.g., memory, processing) and efficiency. Your answer may (but doesn't have to) make comparisons to the function `short_xml_parse`, which _does_ directly return a list of examples (and which is not actually used anywhere else in the code, because it's bad!).

### Analysis Question #2

In the `PCLFeatures` class, we initialize a scikit-learn `DictVectorizer` with the parameter `sparse=True`. This tells `DictVectorizer` to store our features in a _sparse matrix_. Take a moment now to [read up on sparse matrices](https://docs.scipy.org/doc/scipy/tutorial/sparse.html). What is the importance of setting `sparse=True`? 

_Hint_: your answer here should refer to very similar points you brought up in Analysis Question #1! 

**Note before you continue**: Notice that the process methods in `PCLFeatures` and `PCLLabels` take a `max_instances` optional parameter. In both cases, this argument is used to help determine the size of matrices they create, and they are used as an argument to the `do_xml_parse` function. When you are working on debugging your code, you should pass in a value for `max_instances` that is small to help you iterate quickly (see the provided tests for examples). For example, when you are first starting out, you might want to set `max_instances` to something very small, like 5 or 10. Once you’re a bit more confident, you can set `max_instances` to a value that is small enough for it to run quickly but large enough that you’re confident things are working, for example 500. If you set `max_instances` to `None`, it will read through the XML file and determine the largest possible value for `max_instances` (which in this case is just over 10,000).

## Part 2: Implementing your own labelers

Remember that a key part of text classification is that the data must have _labels_ telling us what class/category it belongs to. The PCL dataset includes such labels, formatted inside the XML structure. So, we will need to write some code to extract the label for each example. (For inspiration, you can look back to your Lab 2 code, where you were similarly asked to look up a specific attribute in an XML example node).

At the top of `pcl_main.py`, **define your own derived class that inherits from `PCLLabels`**. (If you need a refresher on Python classes and inheritence, you can check out the [Python documentation](https://docs.python.org/3/tutorial/classes.html#inheritance).) Your class should be called `BinaryLabels`. In your subclass, you will need to implement (override) the `_extract_label` function that is used by the `process` function in the base class. (As you may know, in Python, there's no formal version of the `private` keyword like you might find in Java/C++, but we conventionally add an underscore prefix to member functions that aren't meant to be called from external code) In this function, you should extract the `condescension` attribute stored in an example taken from the ground-truth XML file. The `condescension` attribute is stored as the string `"true"` or `"false"`, hence the name of your subclass.

The skeleton of this class should look something like this:
```
class BinaryLabels(PCLLabels):
    """docstring"""

    def _extract_label(self, example):
        """docstring"""
        # your code here
```

In addition to labels `"true"` and `"false"` for `condescension`, this data also has a categorization scheme for different topical categories of examples under the `'category'` attribute (e.g., `'refugees'`). This allows us to take a unique strategy to evaluation: rather than holding out a random sample of the data as the test set, we can pick a specific category to use as the test set! For example, we might train on examples from categories _other_ than `'refugees'`, and then test on the `'refugees'` data. This can be an interesting way to check for generalizability of your model!

To avoid writing redundant code, we can re-use our labeler code to extract the category for each example. Just below your `BinaryLabels` class, add another derived class that inherits from `PCLLabels`. This class should be called `CategoryLabels`, and should look very similar, but instead of pulling the true/false condescension label, it should pull the example's category.

## Part 3: Implementing your own feature extractor

In the same way you inherited from `PCLLabels` to create the `BinaryLabels` class, you should **add your own derived class to `pcl_main.py` to extract features, inheriting from the `PCLFeatures` base class**. Your class should be called `MyFeatures`. For now, this class should implement a **bag-of-words** feature set: that is, like described in class, you should treat each individual word in an example as a feature (we will expand beyond this later!). Importantly, this time around we are providing a _vocabulary_ that defines what should count as a word! Thus, you should implement `_extract_features` such that it returns a list of words (features) _that are in the input vocabulary_. **Words that are not in the input vocabulary should be ignored.** 

You may wish to take a moment to make sure you understand the `process` method of `PCLFeatures`, which will call your `_extract_features` method, to make sure that you are returning the right thing.

The vocabulary you should use to initialize your vocab is stored in `/courses/cs159/data/patronize/vocab.txt`. For the initial tokenization phase, you should split on whitespace (like you did in Lab 1); you should not do anything fancy like using spaCy. An `extract_text` helper function has already been provided for you that does this, so you don't have to re-implement your Lab 1 code.

_Note_: While you are only _required_ to implement functions that match the interface of the `PCLFeatures` class, you’re _encouraged_ to add extra helper functions make your code more modular and have good style. 

_Hint_: be **very careful** when implementing the `_get_feature_name` method! As the provided comment notes, the purpose of this feature is to get the name (i.e., the text) of the `i`th feature in **the DictVectorizer's internal vocabulary**. This internal vocabulary may not exactly match the provided _initial_ vocabulary! It can differ for two reasons. One is that the DictVectorizer may sort the features differently. Another is that some vocabulary words may not appear at all in the training data (e.g., if you reduced `max_instances` to a small number, you will only ever see the words that happen to appear in those few documents), and the DictVectorizer only considers features it has actually seen. You may wish to consult the [DictVectorizer documentation](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.DictVectorizer.html) to find out how to access the DictVectorizer's internal vocabulary.

## Part 4: Experiment interface

To run prediction on the Patronizing Data task, you’ll set up code in the `pcl_main.py` program. As usual, you can get a summary of the how to use this code (i.e., what arguments are accepted and what they mean) by running the program with the `-h` flag:
```
$ python3 pcl_main.py -h
usage: pcl_main.py [-h] [-o FILE] [-v N] [-s N] [--train_size N] (-t TEST_CATEGORY | -x XVALIDATE) data_file vocabulary

positional arguments:
  data_file             Data file containing labeled training instances
  vocabulary            File containing vocabulary words

options:
  -h, --help            show this help message and exit
  -o, --output_file FILE
                        Write predictions to FILE
  -v, --vocab_size N    Only count the top N words from the vocab file
  -s, --stop_words N    Exclude the top N words as stop words
  --train_size N        Only train on the first N instances. N=0 means use all training instances.
  -t, --test_category TEST_CATEGORY
  -x, --xvalidate XVALIDATE
```

Once it has parsed the command-line arguments, `pcl_main` calls the function `do_experiment`, which has not yet been implemented. Your task now is to **implement `do_experiment`, following this outline**:

1. Create an instance of `PCLVocab`.
2. Create an instance of `MyFeatures`.
3. Create an instance of `BinaryLabels` and `CategoryLabels`.
  - Note: the features and labels are all read from the same file, stored in the `data_file` argument. You will therefore end up reading from this file multiple times. To do this, you will have to call `data_file.seek(0)` to "reset" your position to the top of the file in between the constructor calls for `MyFeatures`, `BinaryLabels`, and `CategoryLabels`.
4. Create an instance of scikit-learn's [MultinomialNB](https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.MultinomialNB.html) Naive Bayes classifier.
5. Using the `process` method of `MyFeatures` and `BinaryLabels`, create feature and target (`X` and `y`) matrices from the full data. Also extract the category of each example using the `process` method of `CategoryLabels`.
6. Depending on the value of `args.xvalidate` or `args.test_category` (which are mutually exclusive), either:
  - If a test category is given, then you'll use all of the examples from that category as test data, keeping only the examples without that category as training data. (You can use numpy array slicing or `np.where` to help do this.) You should fit your model to the data and labels for examples outside the category `test_category`, then get predictions (and probabilities) for each example matching the test category. 
  - If a number of folds `x` is given, perform x-fold **cross validation** on the full data, getting predictions (and probabilities) for every example. Hint: It may help to inspect what the `"method"` parameter does in [scikit-learn's `cross_val_predict`](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.cross_val_predict.html) function!
7. Regardless of which method was used to generate predictions, write out one line to `args.output_file` for each prediction. The line should have three values in order, separated by spaces:
  - The example's id
  - The predicted class, `true` or `false` (do not include the quotes around the string)
  - your model’s confidence&mdash;that is, the _probability_ of the predicted class

Please don't alter the order or format of the output file from the specification above, as `semeval-pcl-2022-eval.py` expects this format.

_Hint_: When writing the output to `args.output_file`, keep in mind that `PCLLabels` converts all labels to integers, so instead of `"true"` and `"false"`, you will have 0 and 1. Furthermore, the correspondence between original labels and their integer values may not be what you expect! To find out what the correspondence is, `PCLLabels` has already implemented a `__getitem__` method so you can use it like a dict. For example, if you have a `BinaryLabels` instance named `binary_labeler`, you can write `binary_labeler[0]` to find out what (string) label the integer 0 corresponds to.

_Hint_: If you're not sure how to get the model's confidence, you can start by looking at the [`predict_proba` method](https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.MultinomialNB.html#sklearn.naive_bayes.MultinomialNB.predict_proba) of MultinomialNB. But you should note that `predict_proba` won't _directly_ give you what you want (which is a single probability for the predicted class), and there's some additional work you'll need to do!

## Part 5: Run the experiments!

Now that you have implemented `do_experiment`, let's run some experiments! For all of the following analysis questions, you should be using the following experiment settings unless otherwise stated:

- Vocabulary size of 10,000 words
- 10-fold cross validation
- All other settings should be left blank/default

You can use the following command to run `pcl_main.py` with the above settings (outputting to a file called `testrun.hyp`, though you can choose to name the output whatever you want):

```
python pcl_main.py /courses/cs159/data/patronize/patronize_full.xml /courses/cs159/data/patronize/vocab.txt -o testrun.hyp -v 10000 -x 10
```

`pcl_main.py` will save its predictions to the file specified by the `-o` argument (in the above example, `testrun.hyp`). We provide a separate script, `semeval-pcl-2022-eval.py`, that reads these predictions and computes standard evaluation metrics (like precision, recall, F1, and accuracy). To compute evaluation metrics, you can run the following command:

```
python semeval-pcl-2022-eval.py -d /courses/cs159/data/patronize/patronize_full.xml -r testrun.hyp
```

### Analysis Question #3

Run the experiment using the above settings, and in your journal, **paste the output of `semeval-pcl-2022-eval.py` showing all the evaluation metrics.** Then, tell us what you think about these results. Are you satisfied? Unsatisfied? Based on these results, would you consider this model to be a "good" classifier?

### Analysis Question #4

Now, replace the MultinomialNB Naive Bayes Classifier with a scikit-learn [DummyClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.dummy.DummyClassifier.html). As the name implies, a DummyClassifier doesn't actually do any machine learning, and instead makes "dummy" predictions using simple heuristics. You can choose which heuristic to use by changing the `strategy` parameter in the DummyClassifier constructor. Try re-running the experiment with a DummyClassifier, trying at least two different heuristic strategies. Once gain, paste the evaluation metrics in your journal. Seeing the results of the DummyClassifier, does this change your answer to Analysis Question #3? (i.e., does it make you _more_ satisfied or _less_ satisfied with the Naive Bayes model)? Why or why not?

(Remember to change your code back to using MultinomialNB after you're done with this question!)

_Note_: When you run the experiments with the DummyClassifier, you may want to write the output to a different file, so that you don't overwrite the results of the original Naive Bayes experiment! We will still need those results for subsequent analysis questions.

### Analysis Question #5

From the original Naive Bayes classifier output, identify (by id) **three examples** that your model is confident **are condescending** (i.e., the model predicts the example is condescending, and the assigned probability is high / close to 1.0). Comment on the contents of the examples: What do you think makes your classifier so confident? Is your classifier right?

Now answer the same questions but for **three** examples that your model is confident **are not condescending.**

### Analysis Question #6

From the original Naive Bayes classifier output, identify (by id) **three** examples that your model is **not confident** about&mdash;that is, examples for which the classifier's assigned probability is very close to 0.5. Comment on their contents: what do you think makes these examples hard for your classifier? Do you find them hard to classify as a human? If not, what aspects of the examples do you take into account that are not captured by the features available to your classifier?

### Analysis Question #7

Now, run the Naive Bayes experiment again, but this time instead of using 10-fold cross validation, try holding out the `vulnerable` category. This can be done by changing the `-x 10` argument to `-t vulnerable`. All other setting should be kept unchanged.

From the resulting output, identify (by id) **three `vulnerable` examples** that your model is **not confident** about. Comment on their contents: what do you think makes these examples hard for your classifier? Do you find them hard to classify as a human? If not, what aspects of the examples do you take into account that are not captured by the features available to your classifier?

Based on your answers to the above, comment on whether this performed better or worse than cross validation. Did you find this result surprising?

## Part 6: Feature engineering!

As you were exploring the results and answering the analysis questions, you hopefully started to gain an intuition for what important aspects of the problem are not being captured by simple bag-of-words features. Now is your chance to try to make the classifier better! You will do this by changing the features that are extracted from the data. Remember that, as we discussed in class on Week 4 and practiced in the in-class activity, features can be more than just individual words; you can extract any information you think is important from the example, and add it to the list of features!

You will proceed by modifying the `_extract_features` function of your `MyFeatures` class to add (or remove!) features to the returned list of features. _For example_, suppose you wanted to extract a feature "does this text contain any all-uppercase words?". The way you could do that is:

1. Split the text by whitespace without lowercasing.
2. Loop through all the resulting tokens, and use Python's `isupper` to check if any of them are all uppercase.
3. If any words were all uppercase, append a new feature to your list of features with a meaningful name like `"CONTAINS_UPPERCASE"`. If none of the words were all uppercase, do not append this new feature.

This is just one example (and you don't have to use it), but hopefully it gives you an idea of what it means to add a new feature to the list of features. This part of the lab is open ended, and we encourage you to exercise your creativity! Your goal is to come up with, and implement, any additional features you can think of to (hopefully) improve the performance (accuracy, F1, etc) of the classifier. 

As you come up with new features, be sure to **document your thought process in your journal**&mdash;even if those features don't end up actually helping! _Every_ change you make to the feature set should be described and justified in your journal, otherwise you will lose points!

Here are some additional suggestions to get you started.

- In addition to adding new features, you can also consider _removing_ features. One way to do this _without_ modifying `_extract_features` is to edit the vocabulary. In particular, the `pcl_main.py` script contains an argument you haven't used yet, `stop_words`. If you supply this argument, it will tell the vocabulary to skip past the N most common words (where N is the number you supply as the argument). If you use this option, you should justify in your writeup why you think it would help (as a hint, think back to your analysis of word counts in Lab 1!)
- Furthermore, besides adding or removing features, you could also _modify_ existing features! For example, maybe you think that the sequence "it is" should be considered the same as the token "it's". One way to achieve this is by scanning through your list of features, and if you see "it" followed by "is", remove them from the list and replace them with "it's". You could also do the opposite, replacing "it's" with "it" and "is".
- Remember that a feature doesn't have to correspond to an actual word! Again: features can be _anything_. This can mean adding "symbolic" binary features like the `"CONTAINS_UPPERCASE"` example. Or, it could mean replacing actual words with "pseudo-words" as a way of approximating differences in meaning. For example, maybe you want to treat words inside quotations differently. You could mark such words by, for example, adding a prefix to them like `"QUOTE-"`, so that the word `"hello"` inside a quote becomes `"QUOTE-hello"`.
- For this part of the lab, you can also change how you do tokenization, if you want. Note that if you do this it may cause some of the tests to start failing, but that's okay (we don't grade you using the tests anyway).

Once you are happy with the list of features, rerun the experiment and **paste the final results in your journal.** Then, answer the following analysis questions:

### Analysis Question #8

Did your changes result in an improvement to the classifier performance? If so, was it as much of an improvement as you'd wanted? If not, do you observe any other changes / tradeoffs? Overall, are you satisfied with the performance of this classifier?

### Analysis Question #9

Identify (by id) **three** examples that the classifier classifies **differently** now with your new features than it did before (e.g., the original classifier predicted condescending but not it predicts not condescending). Which (if any) of your modified features do you think made the difference? Do you think this change was a good thing? Does it better capture your intuitions as a human?
