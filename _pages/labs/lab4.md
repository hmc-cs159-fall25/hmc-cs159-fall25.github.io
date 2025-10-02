---
permalink: /labs/lab4/
title: "Lab 4: Part of Speech Tagging"
---

## Starter Code

As always, grab the [starter code from GitHub Classroom](https://classroom.github.com/a/-WrRjCfN).

## Overview

This lab provides the following starter files:

- `HmmTagger.py`
- `evaluate.py`
- `read_tags.py`

We also provide a minimalist test suite for the classes you will be implementing:

- `test-tagger.py`

As always, the tests are mostly just to ensure that your code is following the specifications so that you don't end up doing analysis based on results that are way off from what we intended. They are not meant to catch every possible bug, and you should feel free to do your own testing (and, if you do so, talk about it in your journal). Also note that, compared to previous labs, this lab's tests will run a bit slower (taking about a minute to run to completion on the course server).

**IMPORTANT NOTE**: If at any point during this lab you encounter the error `ImportError: No module named 'en_core_web_sm'`, you can fix this by running the following incantation in the terminal:
```
python -m spacy download en_core_web_sm
```

## Introduction

This week, you’ll spend some time working with a Hidden Markov Model Part of Speech tagger. Rather than implementing it from scratch, you’ll just add a few key lines and make a series of modifications to the tagger. Along the way, you’ll analyze the impact of different configuration settings on the tagger’s performance.

This lab will also make heavy use of the numpy package. [Numpy](https://numpy.org/) is a very popular Python package designed to provide fast and efficient matrix and linear algebra operations using a MATLAB-like syntax. Some of you may have previously used numpy in another class, internship, or research. But if you've never seen Numpy before, I encourage you to take a quick read through the [Numpy beginners' guide](https://numpy.org/doc/stable/user/absolute_beginners.html) and make sure you understand the basic concepts and syntax. And remember: in addition to the course staff, your peers are also a resource if you need help!

**Hint**: Even for numpy "experts", it can be easy to lose track of the meaning of different array dimensions, and we can often still find ourselves surprised by the result of a matrix operation that doesn't do what we wanted it to. I often find it easier to "draft" my numpy operations by making some simple, small test matrices where I can check that operations are going in the direction I expect and resulting in the appropriate shapes and values. Doing these tests in IPython (or using a breakpoint in your code with ipdb) might seem like more work, but it can often make writing the correct line much faster.

## Part 1: Understanding (and commenting) the Starter Code

Just like in Lab 3, the starter code includes a lot of functionality already implemented for you. In fact, this week's starter code contains a _near-complete_ implementation of the Viterbi algorithm (that is, the dynamic programming algorithm we learned about in lecture for doing part of speech tagging)!

The implementation is split across three files:

- `HmmTagger.py` defines a class `HMMTagger` that implements a Hidden Markov Model (HMM) part of speech tagger. You can find most of the Viterbi implementation in the `predict` function, with one piece missing for you to fill in soon.
- `evaluate.py` runs the part of speech tagger on a corpus, and calculates the tagger's accuracy.
- `read_tags.py` contains helper functions for loading a corpus that contains part of speech labels.

### HMMTagger usage

The two main externally-facing functions of an `HMMTagger` object are `train` and `predict`. You can use an `HMMTagger` like this:
```
nlp = spacy.load("en_core_web_sm")

train_dir = "/courses/cs159/data/pos/wsj/train"
tagger = HMMTagger(nlp, alpha=0.1)
tagger.train(train_dir)

test_sentence = nlp("This is test input to the Part of Speech Tagger.")
tagger.predict(test_sentence)
print([token.tag_ for token in test_sentence])
```

(Of course, if you try to run this right now, you will get nonsense results since the implementation is not yet complete. Once you finish part 2, running this snippet should get you more reasonable looking results.)

`HMMTagger` is designed to integrate with spaCy, and implements its standard tagger interface. This means you can also treat an `HMMTagger` object like a function and "call" it directly instead of explicitly saying `predict`. This is just "syntactic sugar" and does not actually change the behavior, so you can use whichever syntax you prefer:
```
# This snippet does EXACTLY the same thing as the previous one!
nlp = spacy.load("en_core_web_sm")

train_dir = "/courses/cs159/data/pos/wsj/train"
tagger = HMMTagger(nlp, alpha=0.1, vocab_size=20000)
tagger.train(train_dir)

test_sentence = nlp("This is test input to the Part of Speech Tagger.")
tagger(test_sentence)
print([token.tag_ for token in test_sentence])
```

The `HmmTagger.py` file can also be run as a script from the command line. When you do that, it has the following interface:
```
usage: HmmTagger.py [-h] --dir DIR --output FILE [--alpha ALPHA]

Train (and save) hmm models for POS tagging

optional arguments:
  -h, --help            show this help message and exit
  --dir DIR, -d DIR     Read training data from DIR
  --output FILE, -o FILE
                        Save output to FILE
  --alpha ALPHA, -a ALPHA
                        Alpha value for add-alpha smoothing
```

Running `HmmTagger.py` as a script will train an `HMMTagger` object on all of the files in `dir`, then save the model for future reuse in [pickled form](https://docs.python.org/3/library/pickle.html). So for example:
```
python3 HmmTagger.py --dir /courses/cs159/data/pos/wsj/train --output model.pkl
```
Will train an `HMMTagger` on the `wsj` training set, and save the resulting `HMMTagger` object to the file `model.pkl`.

### Evaluation script usage

Once you have trained an `HMMTagger` using the `HmmTagger.py` script, you can run `evaluate.py` to test the model on a test set and compute the accuracy. The `evaluate.py` script has the following interface:
```
usage: evaluate.py [-h] --dir DIR --hmm FILE

POS Tag, then evaluate

options:
  -h, --help     show this help message and exit
  --dir, -d DIR  Read data to tag from DIR
  --hmm FILE     Read hmm model from FILE
```

So for example, to run the trained model previously saved as `model.pkl` on the `wsj` test set, we would run:
```
python evaluate.py -d /courses/cs159/data/pos/wsj/test --hmm model.pkl
```

### TASK: Comment HmmTagger.py

Take a moment now to familiarize yourself with the code in `HmmTagger.py`. Just like last week, your first task is to **add comments to `HmmTagger.py`**, including docstrings for every function. Once again, we will be reading your comments and they will contribute to your completeness and correctness scores! Therefore, you should aim to write comments that prove to us that you fully understand what the code is doing.

Also just like last week, you may encounter Python features or library calls that are unfamiliar to you (especially if you're not familiar with numpy). In particular, notice that we import the following functions from numpy:
```
from numpy import argmax, zeros, array, float32, ones, zeros, log
```

If any of these look unfamiliar to you, you may want to take a moment to look them up in the [numpy documentation](https://numpy.org/doc/stable/reference/index.html)!

In addition, you'll see two matrices populated in the `train` function. One is the _transition_ matrix, which describes the probability of going from one hidden state to another. The other is the _emission_ matrix, which describes the probability of a particular observation given a specific hidden state. (If this feels fuzzy, J&M Chapter 17.4 would be good to review and keep open for this lab). You should replace the TODO comment with information on which matrix is which.

There is no analysis question for this part of the assignment; your comments are the main deliverable. In addition, keep in mind that if you encounter syntax/functions that are unfamiliar to you and need to do some research to figure them out, that is something you should explicitly discuss in your journal!

## Part 2: Finishing the Viterbi Implementation

As discussed above, the starter code contains a near-complete implementation of the Viterbi dynamic programming algorithm, with only a single "hole" missing inside the `predict` function, which has been marked with a TODO. Conceptually, the missing part of the code is supposed to fill in a `costs` matrix so we can compute the log probabilities associated with transitions to each possible tag. To understand exactly what this means, it may help to review a bit of the material from the lecture.

### Lecture review

(If you already feel familiar with this, you can skip ahead to the section "Textbook terminology")

Recall that in lecture, we defined the _recursive_ version of part-of-speech tagging as follows:

$$
BEST(w_{1:n}, G) = argmax_{t_{n-1}} P(w_n \mid G)P(G \mid t_{n-1})BEST(w_{1:n-1}, t_{n-1})
$$

Here, $$BEST(w_{1:n}, G)$$ is a function that answers the following question: for a sequence of observations (words) $$w_1,\dots,w_n$$ (expressed in shorthand as $$w_{1:n}$$), what is the most probable sequence of states (part of speech tags) **such that the last state is the tag G**? As discussed in class, the recursive solution is to "unroll" the sequence backwards by one timestep, looking at the _second-to-last_ state. We consider _every possible tag_ $$t_{n-1}$$ that could come before $$G$$, and compute the probability of the resulting sequence by multiplying:
- $$P(w_n \mid G)$$, the _emission probability_ of observing word $$w_n$$ given state $$G$$
- $$P(G \mid t_{n-1})$$, the _transition probability_ of going from state $$t_{n-1}$$ to state $$G$$
- $$BEST(w_{1:n-1}, t_{n-1})$$, a **recursive call** to compute the probability of the rest of the sequence given that the second-to-last state is $$t_{n-1}$$

### Textbook terminology

The textbook uses slightly different terminology to express the same thing. We do, however, have to be careful, because in the textbook's notation some of the letters mean different things compared to the same letters in the lecture's notation. The textbook expresses the probability of transitioning to state $$j$$ at time $$t$$ (that is, seeing tag $$j$$ for the $$t$$th element in the sequence) as:

$$
v_t(j) = max_{i=1}^N [v_{t-1}(i) \cdot a_{ij} \cdot b_j(o_t)]
$$

We note that although the terminology looks different, there is an exact 1-to-1 correspondence between this equation and the one we saw in lecture:
- $$b_j(o_t)$$ represents the _emission probability_ of observing word $$o_t$$ given state $$j$$ (so this is equivalent to $$P(w_n \mid G)$$ above)
- $$a_{ij}$$ represents the _transition probability_ of going from state $$i$$ to state $$j$$ (so this is equivalent to $$P(G \mid t_{n-1})$$ above)
- $$v_{t-1}(i)$$ represents the probability of the rest of the sequence (that is, up until timestep $$t-1$$) given that the second-to-last state is $$i$$ (so this is equivalent to the recursive call $$BEST(w_{1:n-1}, t_{n-1})$$ above)

One important difference to note is that in the lecture's notation we treated states (tags) as literal strings (like "NNS" for plural noun), whereas in the textbook's notation we treat states as integer indices. You can think of these as indices into a list of possible tags (e.g., if "NNS" is the 10th tag in our list of possible tags, then a word that gets tagged as a plural noun has the tag $$j = 10$$).

In lecture, I preferred the more explicit probabilistic notation because it makes it more clear what's getting multiplied. For this assignment however, we'll use the textbook's notation because it more closely resembles the actual code you'll be writing, where we actually store the probabilities in _matrices_ (such that you can think of $$i$$ and $$j$$ as _indices_).

In our implementation, we do a further transformation to the equation. As briefly discussed in lecture, when we work with probabilities we often prefer to work in _log space_. This is because the probabilities involved are often very small, so working directly with probabilities runs the risk of floating-point precision errors. By contrast, if we take the log of the probabilities, those small probabilities just become large negative numbers. The main thing to keep in mind, when working in log space, is that _multiplication_ becomes _addition_ and _division_ becomes _subtraction_. So, the thing we are max-ing over (that is, the thing inside the brackets in the above equation) transforms into:

$$
\log v_{t-1}(i) + \log a_{ij} + \log b_j(o_t)
$$

Let us refer to the above term as $$cost_{ti}(j)$$. (We call it _cost_ because it is the thing we are max-ing over). 

### TASK: Finish the `predict` function

You should see that, inside the `predict` function, we have a loop over timestamps $$t$$ already, the only thing that's missing is to compute the _cost_ of transitioning from state $$i$$ to state $$j$$ at each timestamp. Thus, we maintain a matrix `costs` such that `costs[i,j]` = $$cost_{ti}(j)$$ (for current timestamp $$t$$). Then, the rest of the already-written code takes care of the rest of the dynamic programming, computing the max and populating a table of "breadcrumbs" to help us find our solution.

So, your goal is to fill in the marked TODO with code that computes $$cost_{ti}(j) = \log v_{t-1}(i) + \log a_{ij} + \log b_j(o_t)$$. **IMPORTANT**: you should avoid writing any further `for` loops beyond the provided one; instead, use numpy operations to compute the entire matrix at once (if you were to use `for` loops to do the same thing, your code would take forever to run!). The following illustration might be helpful for figuring out the relationship between the different provided matrices:

![Visual representation of the matrices]({{ site.baseurl }}/assets/images/viterbi_matrices.png)

**Hint**: Be careful about your matrix dimensions! In particular, notice in the image above that the code stores the Dynamic Programming matrix such that the _columns_ are states and the _rows_ are timesteps (i.e., words in the sequence). This is the _opposite_ of what you saw in both the lecture and the textbook, where rows were states and columns were timesteps. As long as you do your indexing correctly this should not affect the math at all (but we did it this way in the code to make the indexing _slightly_ easier for you).

**Hint**: all the existing code already stores log probabilities instead of raw probabilities, so you shouldn't need to call the `log` function. 

**Hint**: numpy can ["broadcast"](https://numpy.org/doc/stable/user/basics.broadcasting.html) operations, so you can do things like adding a vector to every row of a matrix:
```
>>> a = numpy.ones((3, 4))
>>> b = numpy.random.random(4)
>>> b
array([0.36427943, 0.49914536, 0.1735815 , 0.35321266])
>>> a + b
array([[1.36427943, 1.49914536, 1.1735815 , 1.35321266],
       [1.36427943, 1.49914536, 1.1735815 , 1.35321266],
       [1.36427943, 1.49914536, 1.1735815 , 1.35321266]])
```
You might find that you want to add a vector to each column instead of each row, in which case, a quick solution may be to either [transpose](https://numpy.org/doc/stable/reference/generated/numpy.transpose.html) the matrix or [reshape](https://numpy.org/doc/stable/reference/generated/numpy.reshape.html) the vector. Finally, keep in mind that your matrix is square (number of tags x number of tags), so it's possible it won't throw errors if you swap your rows and columns. It might be good to use a Python interpreter to play around with rectangular matrices to make sure you're doing operations the way you want to.

### Analysis Question #1

Train an `HMMTagger` on the Brown Corpus (`/courses/cs159/data/pos/brown`) and test it on the Wall Street Journal training data set (`/courses/cs159/data/pos/wsj/train`). In your journal, report the accuracy you got. **For this question only**, you **do not** need to do any further analysis of the accuracy (we will do some analysis in the upcoming analysis questions!)

## Part 3: Exploring Tag Set Collapsing

Right now, our code is set up to use the Penn TreeBank (PTB) tag set. This is a commonly-used set of part of speech tags for English, and you can find the full set in the textbook (Figure 17.2, on page 4 of Chapter 17). But this is not the only definition of parts of speech that exists. One other commonly-used alternative is the Universal Dependencies tag set, meant to be a minimalist but generalizable (across multiple languages) set of part of speech tags. You can see the listing of Universal Dependencies tags on [their website](https://universaldependencies.org/u/pos/). Given that Universal Dependencies is a less granular tag set than Penn TreeBank, we might wonder if it has an impact on how "difficult" the task of part of speech tagging is. That's the question we'll explore in this part of the lab!

To start, open up `read_tags.py`. At the top of the file, there is a dictionary called `universal_to_ptb`. The keys of this dictionary are Universal Tags, while the values are tuples of PTB tags that correspond to that tag. For instance, the tag `"VERB"` in the Universal Tag Set encompasses the tags `"MD"` (for modal verbs) as well as a number of tags starting with a `V` representing different verb forms (e.g. non-3rd person, gerunds, etc). 

Your **coding task** for this part of the lab is to add support for Universal Dependencies tags in the code, as an alternative to the Penn TreeBank tags. To do this, you will go through the following steps:

1. Add code to create a dictionary `ptb_to_universal`. This dictionary will have a key for each PTB tag, whose value will be the corresponding Universal tag. Values in this dictionary should just be strings, not lists of strings. While you can do this by just typing in the whole dictionary, it will be much easier (and better style!) to write a small snippet of code to _generate_ the `ptb_to_universal` dictionary from the existing `universal_to_ptb`.
2. Using this dictionary, modify the `parse_file` function to convert tags from PTB tags to Universal tags if `do_universal` is `True`.
3. Add a new flag `universal` to the interface for the `HmmTagger.py` file in the Argument Parser. You should keep the existing default functionality if this flag isn't used, so have `universal` default to `False` by making its `action="store_true"`. This will make it so calling the code with `--universal` will enable using this conversion, and not using the flag will default to the Penn TreeBank.
4. Add a new named parameter `do_universal` to the `HMMTagger` class’s constructor. `do_universal` should default to `False`. Inside the `HMMTagger` object, save the new parameter as a data member called `self.do_universal`.
5. Modify the `HMMTagger` constructor so that, when `do_universal` is true, the `self.tags` list is initialized with the Universal Dependencies tag set instead of the Penn TreeBank tag set.
6. Update the `main()` function in `HmmTagger.py` to pass the new command-line argument `args.universal` to the `HMMTagger` initializer.
7. Modify the `train()` function in `HmmTagger.py` to pass the value of `self.do_universal` to the `parse_dir` function. This will use the code you wrote for `parse_file`.
8. Add a `universal` flag to `evaluate.py` just like you did for `HmmTagger.py`.
9. Modify the `main` function of `evaluate.py` to pass the value of `args.universal` to `parse_dir`, just like you did for the training method of `HMMTagger`.

Once you are done with this process, use your newly modified code to answer the following analysis question:

### Analysis Question #2

You will repeat the experiment from Analysis Question #1, but this time using the Universal Dependenices tags (by specifying your newly-added `universal` flag when running both scripts). **Before you run any code**, write down your hypothesis in your journal: do you think that using the Universal Dependencies tags will result in better accuracy, worse accuracy, or no meaningful effect? Justify your hypothesis. Now, run the experiment and report the accuracy that you get. Does the result match your hypothesis? If it does not, or if it's surprising in any other ways, briefly speculate on why.

Now, make one further adjustment to your code: we want to be able to _train_ on the Penn TreeBank tags but _test_ on the Universal Dependencies tags. To enable this, modify the `main` function of `evaluate.py` so that after it calls `tagger`, but before it compares the tags, it changes each token’s `tag_` attribute to the right Universal Tag Set tag. If you leverage what you have imported from `read_tags.py`, this should only require a couple of lines of code. Then, use your newly modified code to answer the following analysis question:

### Analysis Question #3

Run the experiment again with this new configuration, training on Penn TreeBank tags and testing on Universal Dependencies tags. Report the accuracy that you get. Which previous result is the right one to compare to: the result from Analysis Question #1, or the one from Analysis Question #2? How does it compare?

**For the remainder of the lab, you should train using the full Penn TreeBank Tag Set and test using the Universal Dependencies Tag Set (i.e., use the same configuration as Analysis Question #3).**

## Part 4: Exploring Vocabulary Size Effects

The starter HMM model adds all of the words in the training set to its vocabulary. For the brown data set, that means it has a vocabulary of 47,703 words, plus the `<<OOV>>` or "Out Of Vocabulary" token. (Replacing tokens that aren't in the vocabulary of a corpus with a placeholder is a common and useful convention in NLP!)

In this section of the lab, you will explore some of the time, space, and performance trade-offs that come from varying the size of the vocabulary.

Your **coding task** for this part of the assignment is to modify the `HmmTagger.py` interface so that it can take a vocabulary size as a command-line argument. To do that, you should:

1. Add a `--vocabsize, -v` argument to the `HmmTagger.py` interface. The default value should be `None`, which will correspond to keeping all of the words in the vocabulary (in other words, the default behavior will be the same as the starter code behavior).
2. Modify `update_vocab` so that it only keeps `args.vocabsize` number of words from the vocabulary. Words should be picked in order of frequency, so the words that are kept are the most frequent words. If `vocabsize` is `None`, this behavior should be disabled and all words should be kept.

Now, train six models. All of them should be trained on the Brown corpus using the PTB tag set. They should vary in their vocabulary size: 1000, 2000, 5000, 10000, 20000, or 50000. Note that since 50000 is greater than the number of different word types in the Brown corpus, it should keep all of the tokens in the vocabulary (i.e., the behavior of `vocabsize=50000` on this corpus should be identical to the behavior of `vocabsize=None`). You will use these models to answer the following analysis question:

### Analysis Question #4

For each of the six models, report in your journal:
- How long it took to train the model on `/courses/cs159/data/pos/brown` (in seconds)
- How long it takes to test the model on the `/courses/cs159/data/pos/wsj` data (in seconds)
- How big the model is (in kilobytes or megabytes, as appropriate)
- The model's accuracy

Comment on any patterns you observe. Do the results surprise you or are they what you expected? Why?

**Tip**: The command-line `time` command can be used to time how long another program takes to run. For example, to see how long it takes to build `model.pkl`:
```
jpchang@chrysanthemum:~$ time python3 HmmTagger.py -d /courses/cs159/data/pos/brown -o model.pkl

real    1m13.679s
user    1m12.865s
sys	    0m0.487s
```
This output says that it took 1:13 in real (clock) time, 1:12 in processor time (aka user time), and 0.487 seconds of system time to run `HmmTagger.py`. You should report the processor (user) time for this lab. 

**Tip**: To find the size of a file, you can run `ls -lh` in the directory where the file is stored. For example:
```
jpchang@chrysanthemum:~$ ls -lh
total 39M
-rw-rw-r-- 1 jpchang jpchang 1.3K Oct  1 11:20 analysis.md
-rw-rw-r-- 1 jpchang jpchang  39M Oct  1 11:44 brown.pkl
-rwxrwxr-x 1 jpchang jpchang 1.3K Oct  1 11:50 evaluate.py
-rwxrwxr-x 1 jpchang jpchang 5.3K Oct  1 11:29 HmmTagger.py
drwxrwxr-x 2 jpchang jpchang 4.0K Oct  1 11:45 __pycache__
-rw-rw-r-- 1 jpchang jpchang 2.4K Oct  1 11:20 read_tags.py
```

Note the number next to the timestamp (date) of the file `brown.pkl`. It says `39M`. This means `brown.pkl` is 39 megabytes in size.

## Submitting

When done, please submit a link to your GitHub repo and a PDF copy of your journal on [the Gradescope assignment](https://www.gradescope.com/courses/1066898/assignments/6899941).