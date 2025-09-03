---
permalink: /labs/lab1/
title: "Lab 1: Tokenization and Segmentation"
---

## Starter Code

We distribute starter code using GitHub Classroom. This is the same system used by CS 70, so if you remember how things worked there, it's exactly the same here. If you need a refresher, here's the quick outline:

- Every lab assignment will have a link to accept the starter code on GitHub Classroom.
- GitHub Classroom will let you name your repository anything you want, but to make things easier for the course staff, please always name your repositories with the format `LabXX-firstname1-firstname2` where `LabXX` is the lab number and `firstname1` and `firstname2` are the first names of you and your partner (if you have chosen to work alone, you'll only put your own name).
- If you are pair programming, only one partner should click the link to accept the starter code. The second partner should then find the newly created group in the list of groups and join it. Note that for this to work, each partner should be logged in on their own separate GitHub accounts.

Ready? [Click here](https://classroom.github.com/a/yKJBpHDC) to accept the starter code on GitHub Classroom.

## Part 1: Tokenizer Basics

Put all of your code for this part in `tokenizer.py`. The starter code has an empty main function that you should fill in with code to demonstrate how your functions in this part work.

You should call your main function by placing this standard Pythonic pattern at the bottom of the file:
```
if __name__ == '__main__':
    main()
```
This pattern will be useful for you as you develop more complex python programs. It allows you to write functions that will can be imported into other programs while still having your `tokenizer.py` be runnable as a stand-alone program. If you want to run Python (or ipython) in interactive mode with a particular file, use the -i flag: `python -i filename.py` (or `ipython -i filename.py` if you prefer ipython). This will let you load the code in `filename.py` and then run those functions.

The main function should be the only place where you print anything.

For this part of the assignment, as well as Part 2, we provide a _minimal_ test suite that you can use to double-check that your functions are working as expected. You can run the tests as `python test-tokenizer.py`. Again, these are _minimal_ tests and their main purpose is to help you double-check that you're interpreting the instructions correctly, not to catch every possible bug! You are welcome to add your own tests&mdash;and if you do, that would be something to mention in your journal! (As a reminder, the journal is the only thing that is actually graded; the output of these tests is never directly factored into your grade for this assignment)

_Hint_: every function in Part 1 is possible to implement in 1-2 short lines of code using Python standard classes! If you find yourself writing many lines of complex logic, you may want to pause and peruse some of the documentation pages we have linked throughout.

### Part 1(a)

Write a function called `get_words` that takes a string `s` as its only argument. The function should return a list of the words (or to be more precise, the word instances) in `s`, in the exact order that they appeared. For the purposes of this question, we define a word to be _any space-separated item_. For example:
```
>>> get_words('The cat in the hat ate the rat in the vat')
['The', 'cat', 'in', 'the', 'hat', 'ate', 'the', 'rat', 'in', 'the', 'vat']
```

_Hint_: If you don’t know how to approach this problem, read about [str.split()](https://docs.python.org/3/library/stdtypes.html#str.split).

### Part 1(b)

Write a function called `count_words` that takes a list of the words of `s` as its only argument and returns a [collections.Counter](https://docs.python.org/3/library/collections.html#counter-objects) that maps a word to the count of how many times it occurred in `s`. For now, you can use the output of the `get_words` function as the input to this function (but your function should be generic such that it can be applied to any list of words, for example if&mdash;spoiler alert!&mdash;we later have a different tokenizer we want to test).
```
>>> s = 'The cat in the hat ate the rat in the vat'
>>> words = get_words(s)
>>> count_words(words)
Counter({'the': 3, 'in': 2, 'The': 1, 'cat': 1, 'hat': 1, 'ate': 1, 'rat': 1, 'vat': 1})
```

Notice that this is somewhat unsatisfying because "the" is counted separately from "The". To fix this, have your `get_words` function be able to lower-case all of the words before returning them. You won’t want to break any previous code you wrote, though (backwards compatibility is important!), so add a new parameter to `get_words` with a default value:
```
def get_words(s, do_lower=False)
```

Now, if `get_words` is called the way we were using it above, nothing will change. But if we call `get_words(s, do_lower=True)` then `get_words` should lowercase the string before getting the words. You can make use of [str.lower](https://docs.python.org/3/library/stdtypes.html#str.lower) to modify the string. When you’re done, the following should work:
```
>>> s = 'The cat in the hat ate the rat in the vat'
>>> words = get_words(s, do_lower=True)
>>> count_words(words)
Counter({'the': 4, 'in': 2, 'cat': 1, 'hat': 1, 'ate': 1, 'rat': 1, 'vat': 1})
```

### Part 1(c)

Write a function called `words_by_frequency` that takes a list of words as its only required argument. The function should return a list of (word, count) tuples sorted by count such that the first item in the list is the most frequent item. Items with the same frequency should be in the same order they appear in the original list of words.

`words_by_frequency` should, additionally, take a second parameter `n` that specifies the maximum number of results to return. If `n` is passed, then only the `n` most frequent words should be returned. If `n` is not passed, then all words should be returned in order of frequency.

```
>>> words_by_frequency(words)
[('the', 4), ('in', 2), ('cat', 1), ('hat', 1), ('ate', 1), ('rat', 1), ('vat', 1)]

>>> words_by_frequency(words, n=3)
[('the', 4), ('in', 2), ('cat', 1)]
```

## Part 2: Through the Rabbit Hole

We've built some functions for (very basic) tokenization&mdash;now let's do something interesting with them! For this part, you will explore some files from [Project Gutenberg](https://www.gutenberg.org/), a library of free eBooks for public domain texts.

We have already provided the files for you in a convenient, plain-text format. They can be found on the course server in the directory `/courses/cs159/data/gutenberg/`.

### Part 2(a)

In the Gutenberg data directory there are a number of .txt files containing texts found in the Project Gutenberg collection. First, you should load the text of Lewis Carroll’s _Alice's Adventures in Wonderland_, which is stored in the file `carroll-alice.txt`. Use your `words_by_frequency` and `count_words` functions from Part 1 to explore the text. For the rest of this lab, you will always lowercase when getting a list of words. You should find that the five most frequent words in the text are:
```
the      1603
and       766
to        706
a         614
she       518
```

As an additional check: if your `count_words` function is working _as we originally described it_, it should report that the word "alice" occurs 221 times. Confirm that you get this result with your code. Now, whether this result is "correct" is a much more debatable topic, and I encourage you to reflect on it a little before continuing!

**Note**: If your numbers were right in the previous part, but don’t match here, it may be because of how you’re calling `str.split`. Take a look at the [documentation for str.split](https://docs.python.org/3/library/stdtypes.html#str.split) to see if there’s a different way you can call it.

### Analysis Question #1

You may have noticed that the most frequent words look a little...boring. They're basically just common connective and filler words like "the" and "and"! How far down the list do you have to go before you find an _interesting_ word? Here, "interesting" is subjective and is entirely up to you, but be sure to at least give some justification for what you consider to be "interesting". What does this imply for someone who's trying to use this code for, say, a digital humanities project analyzing the writing style of Lewis Carroll? Are there any simple changes you might recommend to make the code more useful for that use case?

_Reminder: For any lab in this course, if a question (like the one above) asks you to discuss results, that always means both what the results were and what that implies about the world (i.e., your corpus, your method, etc.). A sadly common way to lose points in this class has been forgetting to interpret or analyze results. A good recipe for full points on this sort of question is a paragraph that goes something like:_

_"The result was X...specific interesting examples were X' and X"...this is/isn't surprising because it would imply P or Q...to address this it might be better to do Y / to evaluate Z to learn more"_

_Even if your hypothesis of what's going on turns out to be different from the truth, you'll usually get full points for a plausible and descriptive answer that engages with concepts in NLP._

### Part 2(b)

There is a deficiency in how we implemented the `get_words` function. When we are counting words, we probably don’t care whether the word was adjacent to a punctuation mark. For example, the word "hatter" appears in the text 57 times, but if we queried the `count_words` dictionary, we would see it only appeared 24 times. However, it also appeared numerous times adjacent to a punctuation mark, so those instances got counted separately:
```
>>> word_freq = words_by_frequency(words)
>>> for (word, freq) in word_freq:
...     if 'hatter' in word:
...         print('{:10} {:3d}'.format(word, freq))
...
hatter      24
hatter.     13
hatter,     10
hatter:      6
hatters      1
hatter's     1
hatter;      1
hatter.'     1
```

Our `get_words` function would be better if it separated punctuation from words. We can accomplish this by using regular expressions! We will be making use of `re`, Python's standard regex library, and more specifically the [re.split function](https://docs.python.org/3/library/re.html#re.split). Be sure to add `import re` at the top of your file so you can access the `re` library functions. Below is a small example that demonstrates how `str.split` works on a small text and compares it to using `re.split`:
```
>>> text = '"Oh no, no," said the little Fly, "to ask me is in vain."'
>>> text.split()
['"Oh', 'no,', 'no,"', 'said', 'the', 'little', 'Fly,', '"to', 'ask', 'me', 'is',
 'in', 'vain."']
>>> re.split(r'(\W)', text)
['', '"', 'Oh', ' ', 'no', ',', '', ' ', 'no', ',', '', '"', '', ' ', 'said', ' ', 'the',
 ' ', 'little', ' ', 'Fly', ',', '', ' ', '', '"', 'to', ' ', 'ask', ' ', 'me', ' ', 'is',
 ' ', 'in', ' ', 'vain', '.', '', '"', '']
 ```

Note that this is not exactly what we want, but it is a lot closer. In the resulting list, we find empty strings and spaces, but we have also successfully separated the punctuation from the words.

Using the above example as a guide, write and test a function called `tokenize` that takes a string as an input and returns a list of words and punctuation, but not extraneous spaces and empty strings. Like `get_words`, `tokenize` should take an optional argument `do_lower` that determines whether the string should be case normalized before separating the words. **You don't need to come up with a new regex**: just stick with `(\W)` and remove the empty strings and spaces after the `re.split` call.

To double check if things are working right, use your `tokenize` function in conjunction with your `count_words` function to list the top 5 most frequent words in `carroll-alice.txt`. You should get this:
```
'        2871      <-- single quote
,        2418      <-- comma
the      1642
.         988      <-- period
and       872
```

### Part 2(c)

Write a function called `filter_nonwords` that takes a list of strings as input and returns a new list of strings that excludes anything that isn’t entirely alphabetic. Use the [str.isalpha()](https://docs.python.org/3/library/stdtypes.html#str.isalpha) method to determine is a string is comprised of only alphabetic characters.
```
>>> text = '"Oh no, no," said the little Fly, "to ask me is in vain."'
>>> tokens = tokenize(text, do_lower=True)
>>> filter_nonwords(tokens)
['oh', 'no', 'no', 'said', 'the', 'little', 'fly', 'to', 'ask', 'me',
 'is', 'in', 'vain']
```

Use this function to list the top 5 most frequent words in `carroll-alice.txt`. Confirm that you get the following before moving on:
```
the      1642
and       872
to        729
a         632
it        595
```

### Part 2(d)

Iterate through all of the files in the gutenberg data directory and print out the top 5 words for each. To get a list of all the files in a directory, use the [os.listdir](https://docs.python.org/3/library/os.html#os.listdir) function:
```
import os
directory = '/courses/cs159/data/gutenberg/'
files = os.listdir(directory)
infile = open(os.path.join(directory, files[0]), 'r', encoding='latin1')
```

This example also uses the function [os.path.join](https://docs.python.org/3/library/os.path.html#os.path.join) that you might want to read about.

_Note about encodings_: This open function above uses the optional encoding argument to tell Python that the source file is encoded as `latin1`. Be sure to use this encoding flag to read the files in the Gutenberg corpus, as the default (Unicode) won't work!

### Analysis Question #2

Loop through all the files in the gutenberg data directory that end in .txt. Is 'the' always the most common word? If not, what are some other words that show up as the most frequent word (and in which documents)? What do you notice about these words?

### Analysis Question #3

If you don’t lowercase all the words before you count them, how does this result change, if at all? Discuss what you observe.

## Part 3: Sentence Segmentation

In this next part of the lab, you will write a simple sentence segmenter.

The `/courses/cs159/data/brown/` directory includes three English-language text files taken from the [Brown Corpus](https://en.wikipedia.org/wiki/Brown_Corpus):

- `editorial.txt`
- `fiction.txt`
- `lore.txt`

These files represent large strings of natural language text, with no line breaks nor other special symbols to annotate where sentence splits occur. In the data set you are working with, sentences can only end with one of 5 characters: period, colon, semi-colon, exclamation point and question mark.

However, there is a catch: not every period represents the end of a sentence. Many abbreviations (U.S.A., Dr., Mon., etc., etc.) that can appear in the middle of a sentence, and the period does not indicate the end of the sentence. (If you have a phone that uses autocomplete to type, you may already have had annoying experiences where it automatically capitalized words after these abbreviations!) These text also has many examples where colon is not the end of the sentence. The other three punctuation marks are all nearly unambiguously the ends of a sentence (yes, even semi-colons).

For each of the above files, I have also provided a file in the same directory containing the character index (counting from 0 for the first character) of each of the actual locations of the ends of sentences:

- `editorial-eos.txt`
- `fiction-eos.txt`
- `lore-eos.txt`

Your job is to write a sentence segmenter, and to output the predicted token number of each sentence boundary.

This part of the assignment will be more open-ended and thus there aren't as many opportunities to test for specific expected outputs. Nonetheless, we still provide some basic tests in `test-segmenter.py` that check that your functions are following the specified interface (i.e., take the right arguments and return the right type of output). Make sure you pass these basic tests, as the evaluation script you'll be using depends on your code producing correctly-formatted output!

### Part 3(a)

The given `segmenter.py` has some starter code, and can be run from the command line. When it’s called from the command line, it takes one required argument and two optional arguments:
```
$ python segmenter.py --help
usage: segmenter.py [-h] --textfile FILE [--hypothesis_file FILE] 

Sentence Segmenter for NLP Lab

optional arguments:
  -h, --help            show this help message and exit
  --textfile FILE, -t FILE
                        Unlabled text is in FILE.
  --hypothesis_file FILE, -y FILE
                        Write hypothesized boundaries to FILE
```

Make sure you understand how this code uses the [argparse](https://docs.python.org/3/library/argparse.html) module to process command-line arguments. In addition to the module documentation, you may also find the [argparse tutorial](https://docs.python.org/3/howto/argparse.html) useful.

As in the past, all print statements should be in your `main()` function, which should only be called if `segmenter.py` is run from the command line.

The `segmenter.py` starter code imports the `tokenize` function from the last section.

Confirm that your Python program can open the file `/courses/cs159/data/brown/editorial.txt` and that your code from the previous part splits it into 63,333 tokens.

_Note_: Do not filter out punctuation, since those tokens will be exactly the ones we want to consider as potential sentence boundaries!

### Part 3(b)

The starter code contains a function called `baseline_segmenter` that takes a list of tokens as its only argument. It returns a list of tokenized sentences; that is, a list of lists of words, with one list per sentence.
```
>>> baseline_segmenter(tokenize('I am Sam. Sam I am.'))
[['I', 'am', 'Sam', '.'], ['Sam', 'I', 'am', '.']]
```

Remember that every sentence in our data set ends with one of the five tokens ['.', ':', ';', '!', '?']. Since it’s a baseline approach, `baseline_segmenter` predicts that every instance of one of these characters is the end of a sentence.

Fill in the function `write_sentence_boundaries`. This function takes two arguments: a list of lists of strings (like the one returned by `baseline_segmenter`) and a pointer to a stream to write output (either an open write-enabled file or stdout). You will need to loop through all of the sentences in the document. For each sentence, you will want to write the index of the last token in the sentence to the filepointer on a new line. Remember that python lists are 0-indexed!

Confirm that when you run `baseline_segmenter` on the file `/courses/cs159/data/brown/editorial.txt`, it predicts 3278 sentence boundaries, and that the first five predicted boundaries are at tokens 22, 54, 74, 99, and 131.

### Part 3(c)

To evaluate your system, I am providing you a program called `evaluate.py` that compares your hypothesized sentence boundaries with the ground truth boundaries. This program will report to you the true positives, true negatives, false positives and false negatives, along with some other metrics (precision, recall, F-measure), which may be new to you. You can run `evaluate.py` with the `-h` option to see all of the command-line options that it supports.

If you had run `segmenter.py` on `editorial.txt` using the provided baseline segmenter and wrote the results to a file names `editorial.hyp`, you could then run `evaluate.py` like so:
```
python evaluate.py -d /courses/cs159/data/brown/ -c editorial -y editorial.hyp
```

Confirm that if you this, you get the following results:
```
TP:    2719	FN:       0
FP:     559	TN:   60055

PRECISION: 82.95%	RECALL: 100.00%	F: 90.68%
```

(A quick aside: this is a good case for why we like to think about F1 score to help reason about acceptable tradeoffs of precision and recall. If only 25% of the punctuation marks we retrieve were true sentence boundaries, we would get recall of 100% still, precision of 25%, and an F1 of 40%: much lower than the arithmetic average of 62.5% we would get from precision and recall. The further either precision or recall gets from 1, the more it affects the F1 score.)

### Part 3(d)

Now it’s time to improve the baseline sentence segmenter. We don’t have any false negatives (since we’re predicting that every instance of the possibly-end-of-sentence punctuation marks is, in fact, the end of a sentence), but we have quite a few false positives.

There’s a placeholder for a second segmentation function defined in the starter code. You will fill in that `my_best_segmenter` function to do a (hopefully!) better job identifying sentence boundaries. The specifics of how you do so are up to you. (If you need inspiration, you may think back to the in-class exercise where you reasoned about edge cases for a tokenizer; a lot of the same logic probably applies here!)

You can see the type of tokens that your system is incorrectly characterizing by setting the verbosity of `evaluate.py` to something greater than 0 using the command line arguments. **Setting it to 1 will print out all of the false positives and false negatives**, which will help you identify specific cases that your segmenter is still getting wrong so you can address them (and discuss them in your journal!).

### Analysis Question #4

Describe (using the metrics from the evaluation script) the performance of your final segmenter. Specifically, give at least 3 things your final segmenter does _better_ than the baseline segmenter, and at least 3 places where your segmenter still makes mistakes. What cases are you most proud of catching in your segmenter (be specific)? If you had another week to work on this, what would you change? What if you had the whole semester?

**IMPORTANT**: In part 3(e), you will be asked to run your segmenter "as is" with no further changes. In other words, you are not allowed to make any further changes to `my_best_segmenter` once you go past this point! If there's still any changes you're just itching to make, make them now; once you move on to Part 3(e), your code is considered "final"...

### Part 3(e)

**Did you read the above note?** At this point, you are not allowed to make any further code changes. Turn back now if you're not ready to commit! Once you're ready, come back here and continue...

In part 3(d), you developed rules for your segmenter by (presumably) looking at cases it got wrong on `editorial.txt`, `fiction.txt`, and `lore.txt`. But how generalizable are the rules you came up with? In this final part of the assignment, you'll explore this question by running your segmenter on _other_ parts of the Brown Corpus you haven't seen up until this point!

The directory `/courses/cs159/data/brown-hidden` contains additional categories from the Brown Corpus:

- `adventure.txt`
- `belleslettres.txt`
- `government.txt`
- `hobbies.txt`
- `news.txt`
- `romance.txt`

As before, each data file is accompanied by a `-eos` file that contains the ground-truth end-of-sentence locations.

Pick **two** of these additional categories and run your segmenter and the evaluation script on them. Just as a reminder of the command-line syntax, if you wanted to run on `adventure.txt`, you would first run the segmenter as:
```
python segmenter.py -t /courses/cs159/data/brown-hidden/adventure.txt -y adventure.hyp
```

Then run the evaluation script as:
```
python evaluate.py -d /courses/cs159/data/brown-hidden/ -c adventure -y adventure.hyp
```

Then, answer the following analysis question:

### Analysis Question #5

Please paste the metrics (i.e., the output of the evaluation script) for both of the two additional categories you ran on. How do the metrics look compared to the ones from Part 3(d)? Better? Worse? A mix of both? Are there any new edge cases that tripped up your segmenter here that you didn't see while you were developing the rules in Part 3(d)? What might this imply about the generalizability of your segmenter, or of rule-based segmenters overall?

## Submitting

When done, please submit a link to your GitHub repo and a PDF copy of your journal on [the Gradescope assignment](https://www.gradescope.com/courses/1066898/assignments/6602963).