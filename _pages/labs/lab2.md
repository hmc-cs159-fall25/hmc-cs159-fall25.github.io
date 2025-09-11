---
permalink: /labs/lab2/
title: "Lab 2: Frequency and N-Grams"
---

## Starter Code

As always, grab the [starter code from GitHub Classroom](https://classroom.github.com/a/iA2FTVqQ).

## Overview

This lab provides the following starter files:

- `ngrams.py`
- `zipf.py`

We also provide corresponding (minimalist) test suites for each file:

- `test-ngrams.py`
- `test-zipf.py`

As always, the tests are mostly just to ensure that your code is following the specifications so that you don't end up doing analysis based on results that are way off from what we intended. They are not meant to catch every possible bug, and you should feel free to do your own testing (and, if you do so, talk about it in your journal).

### SpaCy

Remember in Lab 1 how much work you put into tokenization? Given that tokens are foundational to pretty much all NLP algorithms, wouldn't it be annoying if you had to repeat all that work for every lab? Well, the good news is that tokenization is such a common step in NLP that, unsurprisingly, there's open-source code that will do it for you.

Enter [spaCy](https://spacy.io/) (don't ask me about the capitalization, that's their "official" branding...): an open-source Python package that implements a number of common NLP algorithms. We'll be relying on spaCy for the rest of the semester to streamline our code and avoid repetitive work. This week, we'll be taking advantage of spaCy's built-in tokenization capabilities.

For some of you, this may be your first time using spaCy&mdash;and even if you have used it before, maybe it's been a while and you've forgotten some things. NLP as a field is highly dependent on libraries like spaCy, and being able to navigate your own way around libraries is a key skill we want you to practice. To this end, while we will provide hints and outlines regarding spaCy usage, we won't tell you _exactly_ what to write. If you run into issues or confusion regarding spaCy syntax, you are strongly encouraged to make use of all available resources, including the linked spaCy documentation pages, StackOverflow, search (whether old-fashioned or AI-powered), and of course grutoring and office hours. And don't forget to keep a log of that process in your journal!

## Part 1: Zipf's Law

In lecture, we talked about how even though word frequencies seem like a simple and crude tool, they can actually be super useful for discovering cool things about language! One early interesting discovery relating to word frequencies is known as Zipf's Law. First put to writing in 1932 by linguist George Zipf (hence the name), Zipf's law states, in plain English, that **the frequency of a word is inversely proportional to its rank in a frequency-ordered list**. Or, mathematically:

$$ C(w) \propto \frac{1}{K(w)} $$, or equivalently, $$ C(w) = \frac{S}{K(w)} $$

Where $$w$$ is a word, $$C(w)$$ is the frequency (count) of $$w$$ (as defined in class), $$K(w)$$ is its ran**k** in a frequency-ordered list (so the highest frequency word has rank $$K(w)=1$$, the second highest $$K(w)=2$$, etc.), and $$S$$ is an arbitrary constant (which you can think of as a **s**caling factor).

If you're curious to learn more, check out [Wikipedia's page on Zipf's Law](https://en.wikipedia.org/wiki/Zipf%27s_law).

In this part of the lab, we'll put Zipf's Law to the test! We will use [matplotlib](https://matplotlib.org/) to visualize the relationship between frequency and rank. Matplotlib is a plotting library for Python with syntax inspired by MATLAB, and is very commonly used in science writing&mdash;so if you've never used it before, this is your chance to learn and practice a very useful tool!

### Part 1a

Instead of writing our own tokenization functions, we will use spaCy to do the tokenization. Pause now and read a bit about [spaCy language processing pipelines](https://spacy.io/usage/processing-pipelines).

For this part, we only want spaCy to tokenize our text, so we will set the pipeline to have no steps using the empty list, `[]`. Since some of our documents will be long, but we’re not doing any memory-intensive processing, we will tell spaCy that it’s okay to load large documents all at once instead of a little bit at a time. To specify these instructions, add these lines to the top of your `zipf.py` file:

```
from spacy.lang.en import English

nlp = English(pipeline=[], max_length=5000000)
```

Next, **write a function** called `read_one(file_path)` that takes the location of a file as its input, and that returns a `Counter` object representing all of the tokens in the corresponding text file. (Please lowercase all of the text!)

_Hint_: You can get the `Token` objects in a spaCy document by iterating over a spaCy `Doc` object, e.g. using Python's `for...in` loop syntax. You can get the text of a `Token` (as a string) using the `.text` attribute of the `Token` object.

_Hint_: The following is a standard Pythonic way to open a latin1-encoded file with name `filename` as read-only (`'r'`). This is a nice alternative to having to call both `open` and `close` on a file.	
```
with open(filename, 'r', encoding='latin1') as fp: 
    # do processing...
```

Next, **write a function** called `read_all(dir_path, extension=None)` that takes the location of a directory as its input, and returns a Counter object representing all of the text of all of the (lowercased) files in the corresponding directory whose file extention is `extension`. If `extension` is None, then `read_all` should include every file in the directory. For example, `read_all("/courses/cs159/data/gutenberg", ".txt")` should return a `Counter` that counts all of the tokens in all of the text files saved with the `.txt` extension in the `/courses/cs159/data/gutenberg` directory, while ignoring all other files (e.g., `.md` files) in that directory.

Hints: You will want to use the [os.walk](https://docs.python.org/3/library/os.html#os.walk) function to recursively search for files in the directory. You can get a file’s extension with [os.path.splitext](https://docs.python.org/3/library/os.path.html#os.path.splitext). It may also be helpful to know that two `Counter` objects can be added together to create a new `Counter` object!

### Part 1b

Although Zipf's Law was originally defined in terms of absolute frequency (count), in NLP it's often more common to reason in terms of _relative_ frequencies, $$R(w)$$ as defined in class. (you can take a moment to convince yourself that, assuming a fixed corpus, this shouldn't affect the proportionality relationship at all!)
The reason we prefer relative frequency is that the scale of absolute counts depends on the corpus size (bigger corpus means bigger numbers), whereas relative frequency is always between 0 and 1, making it easier to compare across corpora. 

Let $$R(w)$$ be the relative frequency of $$w$$ as defined in class (e.g. if "the" occurs 1642 times out of 35652 tokens, then its relative frequency is 1642/35652 = 0.04606), and $$K(w)$$ be the rank as defined above. To visualize the relationship between rank and frequency, we will create a log-log plot of $$K(w)$$ (on the x-axis) versus $$R(w)$$ (on the y-axis). For these plots, we will use the [pyplot](https://matplotlib.org/stable/tutorials/pyplot.html) library, part of matplotlib.

By default, matplotlib will try to open a window to display figures as soon as they’re created. That won’t work over ssh (unless you’re using window forwarding) or in VS Code, but we can stop matplotlib from trying to open the plot in a new window by changing which backend it uses; that is, what it does with information about a plot once it's rendered. This needs to be done before we import pyplot using the following incantation:
```
import matplotlib
matplotlib.use('Agg')
from matplotlib import pyplot
```

You will **write a function** called `do_zipf_plot` that takes two parameters:
- A `Counter` object with the counts of words from one or more files.
- A string `label` that can be used to title the figure.

The starter code includes two implemented functions that call `do_zipf_plot`:
- `plot_one`, which calls `read_one` and `do_zipf_plot` to generate a visualization of data from one file, and
- `plot_all`, which calls `read_all` and `do_zipf_plot` to generate a visualization of data from an entire directory.

Your `do_zipf_plot` function should start by creating a figure object:
```
fig = pyplot.figure()
```

It should then use the `Counter` argument to create data in the right form for a call to [pyplot.loglog](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.loglog.html#matplotlib-pyplot-loglog).

Be sure to label your axes and plot with `xlabel`, `ylabel`, and `suptitle` functions [in pyplot](https://matplotlib.org/stable/api/pyplot_summary.html#module-matplotlib.pyplot). Add a legend to the lower left of the plot (you can explore the pyplot documentation for how to do this), and then save the resulting figure:
```
pyplot.savefig('zipf_{}.png'.format(label))
pyplot.close()
```

Before moving on, confirm that calling `plot_one('/courses/cs159/data/gutenberg/carroll-alice.txt')` generates a plot that matches the one below:

<div align="center">
  <img src="{{ site.baseurl }}/assets/images/zipf_carroll-alice.png" alt="Zipf plot for carroll-alice.txt" width=750px>
</div>

### Part 1c

Now we're ready to test how well Zipf's Law works! Per the equations above, the 50th most common word should occur with about three times the frequency of the 150th most common word (for example).

Add to your `do_zipf_plot` function so that in addition to plotting the empirical rank vs frequency data, it also plots the expected values using Zipf’s law. For the constant scaling factor $$S$$ in the formulation of Zipf's Law above, you should use:

$$ 
S = \frac{T}{H(n)}
$$

So that the computation for expected frequency works out to be:

$$
C(w) = \frac{S}{K(w)} = \frac{T}{H(n)K(w)}
$$

where $$T$$ is the number of word _tokens_ in the corpus, $$n$$ is the number of word _types_ in the corpus, and $$H(n)$$ is the $$n$$th harmonic number. (Remember: the number of _tokens_ is the total number of words in the document. The number of _types_ is the total number of unique words in the corpus.)

**Use this function to compute harmonic numbers.** It’s included in your starter code:
```
def H_approx(n):
    """
    Returns an approximate value of n-th harmonic number.
    http://en.wikipedia.org/wiki/Harmonic_number
    """
    # Euler-Mascheroni constant
    gamma = 0.57721566490153286060651209008240243104215933593992
    return gamma + math.log(n) + 0.5/n - 1./(12*n**2) + 1./(120*n**4)
```

To plot a second curve, you will add a second pyplot.loglog(...) line after you plot the empirical frequency data. Be sure to label each data line so that your legend will be informative!

**WARNING**: Be careful about the scales of your y-axis when plotting the two curves! Notice that the math gives you expected _absolute_ frequencies $$C(w)$$, but the curve we plotted in Part 1b was scaled to _relative_ frequencies $$R(w)$$. Some additional scaling will probably be needed, otherwise your two curves will look way different by orders of magnitude!

### Analysis Question #1

**Zipf’s Law and Alice**: How closely does the empirical data in `carroll-alice.txt` follow the theoretical relationship of Zipf's Law?

### Analysis Question #2

**Zipf’s Law and Other Texts**: Repeat the previous question for a few other texts of your choice included in the `/courses/cs159/data/gutenberg` directory. Are the results consistent? In your answer, please be sure to name the specific texts you chose and the plots for each of them.

### Analysis Question #3

**Zipf’s Law and All Texts**: Repeat the Zipf’s Law experiment with all of the text from all of the files in `/courses/cs159/data/gutenberg`. How many tokens are in this combined corpus? How does this plot compare with the plots from the smaller corpora? Once again, please make sure to include the resulting plot in your answer.

### Analysis Question #4

**Zipf’s Law Discussion**: Does Zipf’s Law hold for each of the plots your made? What intuitions have you formed? Does the length of a document have an impact on how well it does or does not follow Zipf’s Law? What else do you notice?

### Analysis Question #5

**Zipf’s Law for Random Text**: Generate synthetic (that is, "fake") random text by using `random.choice("abcdefg... ")`, taking care to include the space character in your text. You will need to `import random` first. Use the string `join` command to accumulate your random characters into a (very) long string. Then tokenize this string, and generate the Zipf plot as before, and compare the plot to the ones you got from your non-synthetic English data. What do you make of Zipf’s Law in the light of this? (Source: Exercise 23b, Bird, Klein and Loper, 2009) Once again, please make sure to include the resulting plot in your answer.

## Part 2: N-grams

This week, we learned about the problem of _sparsity_, or how to handle 0 counts in language modeling. If you were to train even a bag-of-words (unigram) language model on the fiction category of the Brown corpus and then try to calculate the probability of generating the editorial category, you’d end with a 0 probability: some words that occur in the editorial category simply don’t appear in the fiction category (e.g. “badge”).

In this part of the assignment, we’ll explore that problem in more depth. At the same time, we'll get some practice working with data that is formats more complicated than simple .txt.

You should put your code for this part in `ngrams.py`. Add the same lines to the top of this file for working with spaCy that you have in your `zipf.py`.

### Part 2a: Extracting data from XML files

In Lab 1, as well as in Part 1 of this lab, we provided you with data in the form of plain-text .txt files. Such files are easy to work with, because you can just read their contents directly as a string. But plain text also has a major downside: while it can easily represent the raw text, it can't easily include _metadata_: that is, additional information _about_ the data. Metadata is very important for NLP applications; we usually don't just care about the text itself, but also about properties such as who wrote it and when, or domain-specific information like "is this text toxic". Therefore, the vast majority of actual NLP datasets (like ones you will perhaps use for your final project) are stored in more complex formats; popular options include CSV, JSON, and XML. To make sure you get experience with these common formats, from this point forward the lab assigments will be working with this type of data instead of plain-text files.

We'll start this week with XML (and see other formats in future labs). If you're not used to XML, it's a general-purpose markup language that looks syntactically similar to HTML code. Core concepts of XML that you need to know for this lab (and future ones) include:
- **Nodes**: these can be used to represent individual data entries. To draw an analogy to HTML: the HTML code `<a href="https://cs.hmc.edu">` defines an anchor (link) **node**.
- **Attributes**: these are additional entries in a node, kind of like keys in a dictionary, that can be used to identify metadata. In the above HTML example, `href` is an **attribute** that signals that you are about to see a URL.
- **Values**: if each attribute is like a dict key, that key must also map to a specific value. In the above HTML example, the `href` attribute has the **value** `"https://cs.hmc.edu"`.

Specifically, for this lab and Lab 3, we're going to do some work on the "Don't Patronize Me!" dataset, in which data annotators have categorized passages of text based on whether they are "patronizing or condescending towards vulnerable communities" in the hopes of supporting analysis of unconscious bias. The dataset was part of a shared task (a sort of research mini-competition) at SemEval2022.

The data for this task is in `/courses/cs159/data/patronize`. There’s a single XML file that contains all of the labeled examples we’ll look at this week. Each labeled example has a `condescension` attribute that indicates whether it was annotated as condescending: `true` or `false`.

While this dataset is small, other datasets later this semester (including those you might be interested in for your final project) can be many gigabytes. Loading an XML file that big into memory is a recipe for trouble, though, so it’s best not to store the whole thing in memory at once if we can help it. Fortunately, the [lxml](https://lxml.de/parsing.html) library gives us a way to iteratively parse through an XML file, dealing with one node at a time. Here’s sample code that opens a file called `myfile.xml` and call a function called `my_func` on every example node:
```
from lxml import etree

fp = open("myfile.xml", "br")
for event, element in etree.iterparse(fp, tag=("example",)):
    my_func(element)
  	element.clear()
```

The starter code has a generator function called `do_xml_parse()` that uses `lxml.etree` to yield one node at a time. Look at that code and make sure you can explain how each line of it works before you move on.

**Write a function** called `get_unigrams` that takes as input a spaCy Doc(ument), and returns a list of all of the unigrams in the document. Like in Lab 1, `get_unigrams` should also take an optional argument `do_lower` whose default value is `True`. That argument should determine whether the text of each token is lowercased before returning the unigrams. **For all of the analysis in this lab, you SHOULD lowercase the tokens unless told otherwise.**

Next, **write a function** called `get_examples(args, attribute, value)` that returns a Counter. `get_examples` will use `do_xml_parse` to iterate through all of the examples passed in via `args.examples`. For each example whose attribute `attribute` has the value `value`, it will call `get_unigrams` on the text of the example. For example, `get_examples(args, 'condescension', 'true')` will call `get_unigrams` once for every example whose `condescension` attribute is `true`.

_Hint_: If you have an example element called `example` that contains only one piece of raw text (which is the case for all example elements in this dataset), you can access that text using `example.text`.

_Hint_: Some of the articles contain HTML entities that have been "escaped," or marked with special characters, to avoid interfering with the XML parsing. (Ampersands, or '&'s, are the biggest example of these.) You can un-escape those by doing `import html` and then calling the `html.unescape` function on the articles’ text before creating your spaCy Docs. 

Stop now and confirm that if you call `get_examples` on the `patronize_sample.xml` file for the case where `condescension` is set to `true`, you get the following counts:
```
the: 2638
opportunity: 22
zero: 1
```

We are interested in knowing how many of the unigrams in one category of text (e.g., `condescension='true'`) are not in another category of text (e.g., `condescension='false'`). Over the course of this assignment, you will explore several ways of grouping the text, so we’ll want to carefully organize our code for reusability. In the rest of this writeup, we’ll refer to the set of data we generate counts from as the _training set_, and the set of data that we check for zeros using those counts as the _test set_.

**Write a function** called `compare(train_counter, test_counter, unique=False)`. This function will check for words in the _test set_ that don't show up (i.e., have a zero count) in the _training set_. Moreover, this function can be configured to either count unique words (i.e., types) or word instances (i.e., tokens). Specifically, the three arguments to compare should be:
- `train_counter`: A `Counter` object representing counts from the training set
- `test_counter`: A `Counter` object representing counts from the test set
- `unique`: A boolean indicating whether to count _tokens_ (`unique=False`) that don't show up in the training set or the distinct _types_ (`unique=True`)

`compare` should return two numbers in a tuple:
- The count of tokens (or types) in the test set that have a zero count in the training set
- The total number of tokens (or types) in the test set

Confirm that if you call `compare(Counter(['a','b','c']), Counter(['c','d','d']), unique=True)` you get `(1,2)`, and if you call `compare(Counter(['a','b','c']), Counter(['c','d','d']), unique=False)` you get `(2,3)`.

The given code has a function called `do_experiment` that calls `get_examples` twice (once for the training data, once for the test data), and then prints the results from compare as a Markdown table. **Read through that function now** and make sure that you understand it, since you will add to it later in the lab.

### Analysis Question #6

What percentage of the tokens that appear in the condescending (`true`) examples don’t appear in the neutral (`false`) examples? Conversely, what percentage of tokens that appear in the neutral (`false`) examples don’t appear in the condescending (`true`) examples? How might you initially interpret these findings&mdash;could they say something interesting about condescension?

### Analysis Question #7

What happens if you look at _types_ instead of _tokens_? Does this change your interpretation of the results in any way?

## Part 2b: Beyond unigrams

What happens when you move to higher order n-gram models like bigrams and trigrams?

**Write a function** called `get_bigrams` that takes as input a spacy Document, and returns a list of all of the bigrams in the document. Remember: bigrams are consecuitive sequences of two tokens, like "Los Angeles" or "my cat".

Then, **write a function** called `get_trigrams` that takes as input a spacy Document, and returns a list of all of the trigrams in the document. Remember: trigrams are consecutive sequences of three tokens, like "Harvey Mudd College" or "my cat is".

_Hint_: Don’t try to manually generate the bigrams and trigrams from scratch. Instead, use your `get_unigrams` function along with Python’s built-in [zip](https://docs.python.org/3/library/functions.html#zip) function to save yourself some code-writing!

**Modify your `get_examples` function** so that it returns a tuple with 3 items: a `Counter` of unigrams, a `Counter` of bigrams, and a `Counter` of trigrams. Then **modify `do_experiment`** so that it generates three table rows with statistics for not only unigram zeros, but also bigram and trigram zeros.

### Analysis Question #8

What percentage of the bigrams (tokens, not types) that appear in the condescending-labeled examples don’t appear in the neutral-labeled examples? What percentage of the bigrams that appear in the neutral-labeled examples don’t appear in the condescending-labeled examples? Then, answer those two questions again but for trigrams. Do you notice any interesting patterns/trends as you go from unigrams to bigrams to trigrams? Do the results surprise you? In your answer, make sure to include the table generated by `do_experiment`.

## Part 2c: Randomized chunks

Perhaps in the previous parts, you noticed interesting differences that you thought could signal differences between condescending and neutral text. But are those differences actually meaningful? In science, a fundamental question we must always address is: are the trends we are seeing just the result of random chance?

Here's one way we could address that question: instead of finding which features distinguish one category (condescending) from another (neutral), suppose we break each of the categories in half. Then we could compare the feature distributions from our model using half of the condescending-labeled examples and half of the neutral-labeled examples as if they're from the same category. This can help us calibrate our sense of how much of the effect we saw in the last part is actually connected to the labels (instead of just a natural property of having lots of text). 

This relates to a concept called a _permutation test_ from statistics, a convenient way to reason about what a statistically significant result is when you don't have a clear indication of which probability distribution describes the variable you're interested in.

Randomly splitting data this way can also be useful when testing systems that try to predict something about each example using a concept called _k-fold cross validation_. In this scenario, you split your data into $$k$$ chunks, each of which takes a turn being the test set while the other $$k - 1$$ are the training set. 

In the sample data you used above, each of the examples has an attribute `randomchunk` that assigns it to either chunk `a` or chunk `b`. (since there are two chunks, this gives us 2-fold cross validation)

You shouldn’t need to write much (any!) code here. Instead of calling `do_experiment` for the `condescending` attribute, you can now call it with the `randomchunk` attribute.

### Analysis Question #9

Try training on `randomchunk=="a"` and testing on `randomchunk=="b"`. Then train on `randomchunk=="b"` and test on `randomchunk=="a"`. How are your results different from the previous question? Why? Does this change your previous interpretations at all? Why or why not? Your writeup should include a table of your results, which you can generate with your expanded `do_experiment` function from above. Report percentages, not raw counts.

## Submitting

When done, please submit a link to your GitHub repo and a PDF copy of your journal on [the Gradescope assignment](https://www.gradescope.com/courses/1066898/assignments/6725514).
