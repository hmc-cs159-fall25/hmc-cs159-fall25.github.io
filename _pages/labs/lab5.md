---
permalink: /labs/lab5/
title: "Lab 5: Information Retrieval and Vector Semantics"
---

## Starter Code

As always, grab the [starter code from GitHub Classroom](https://classroom.github.com/a/GwrA2BRj).

## Overview

This lab provides the following starter files:
- `WikiDataReader.py`
- `wiki_search.py`
- `wiki_word_lookup.py`

## Introduction

In 1998, a tiny startup called Google launched a search engine and changed the world. But I mean...come on, how hard can it be to make a search engine, right? Is Larry and Sergey's fame and fortune deserved? Could you do better? Let's find out!

In this lab, you'll try your hand at building a (very basic) search engine. To keep things manageable, we won't be having you search the entirety of the Internet. Instead, your search engine will simply search Wikipedia articles; specifically, it will search a corpus of over 120,000 Wikipedia articles, which we have provided for you in the directory `/courses/cs159/data/wikipedia/enwiki_sampled` (as you might imagine, even though this is the largest corpus we've had you work with on a lab, it's still a tiny fraction of the total number of articles on Wikipedia!). Along the way, you will also investigate how **vector semantics** can (or can't) help you with this information retrieval task.

One other thing to know about this lab: believe it or not, this is the last lab of the semester! From here on out, we'll start to transition to thinking about the final project. Therefore, in addition to having you practice information retrieval and vector semantics, another learning goal of this lab is to give you some experience with what it "feels like" to work on a final project. Specifically, this lab has the following properties that make it different from previous labs (but which make it more closely resemble a final project):
- The data you will be working with has no labels of any kind, which means that you cannot do any sort of empirical evaluation. Instead, you will need to interpret your results qualitatively, based on your own human intuitions and knowledge of NLP. This is a common situation faced by many final project teams, since teams often want to work with real world data which likewise typically isn't labeled.
- Likewise, there are no provided tests. You will need to use your intuition to judge whether the results you are seeing look reasonable. Again, this is something you will encounter in the final project; NLP algorithms don't always behave the way we expect and "correct" results can sometimes still look strange, so making this judgment can be harder than it sounds! When in doubt, try running multiple different searches/inputs. If they _all_ look weird, then something is probably wrong, but if at least some look clearly reasonable you are probably ok.
- There will be a "choose your own adventure" aspect at the end of this lab where you're given full freedom to explore Python libraries of your own choosing with no guidance. Once again, this is meant to simulate the final project experience, where teams typically need to do their own research to find resources that will help them achieve their goal.

**Hint** (useful for the entire lab): at several points in this lab, you may find yourself wanting to do matrix multiplication. There are two things to be careful about regarding matrix multiplication in numpy:
1. You might think that `*` does matrix multiplication, but you'd be wrong! The exact behavior of `*` varies depending on the shape of the matrices, and it can be hard to predict since numpy will always try to interpret it in some way that "works" regardless of the shapes. To do actual matrix multiplication, of the kind that you learned about in Linear Algebra, such that the code will throw an error if the shapes don't work, you shoud instead use the `@` operator (so, `M @ N` instead of `M * N`).
2. A vector is not a matrix, even though both are considered "arrays" in numpy! A vector with `N` elements has shape `(N)`, whereas a matrix with one row and `N` elements (which sounds like it _should_ be the same thing) has shape `(1,N)`. You can't multiply a vector with a matrix, you can only multiply two matrices! To turn a vector into a 1x`N` matrix, you can call `reshape(1,-1)`. Another nice trick: if you want the vector to become "vertical" (i.e., a matrix with one column, with one element per row) you can instead call `reshape(-1,1)`. Finally, if you ever want to take such a "flat" matrix (1xN or Nx1) and turn it _back into_ a vector, you can do that by calling `flatten()`.

## Part 1: Understanding (and commenting) the starter code

Once again, we have provided you with starter code that implements much of the information retrieval functionality already. There are `TODO` comments in all three files indicating "holes" in the starter code that you will need to fill in.

### TASK: Comment the starter code

Just like the last two labs, your first job is to add comments throughout the provided code, including docstrings for every function. Once again, we will be reading your comments and they will contribute to your completeness and correctness scores! Therefore, you should aim to write comments that prove to us that you fully understand what the code is doing. 

There is no analysis question for this part of the assignment; your comments are the main deliverable. In addition, keep in mind that if you encounter syntax/functions that are unfamiliar to you and need to do some research to figure them out, that is something you should explicitly discuss in your journal!

### TASK: Working with JSON

Way back in Lab 2, we introduced you to the notion that real datasets typically aren't stored as plain text and instead are stored in _structured_ formats, which enable metadata to be added alongside text. We showed you one common structured format, XML, which you worked with in Labs 2 and 3.

This week, we're introducing you to another commonly used format: JSON. JSON stands for **JavaScript Object Notation**, and as the name implies, it's meant to store data in a format that looks a lot like JavaScript objects. If you've never used JavaScript before, don't worry&mdash;a JavaScript object is _basically_ the same thing as a Python `dict`. So, one way to think about JSON is that it's a way of saving `dict`s to a file!

The directory `/courses/cs159/data/wikipedia/enwiki_sampled` contains our Wikipedia dataset, split across multiple `.jsonl` files. Note that a `.jsonl` file is _slightly_ different from a `.json` file. The `l` stands for "list" or "lines", and it indicates that the file contains _multiple_ JSON objects (i.e., `dict`s), one on each line (by contrast, a regular `.json` file contains only one object). Each JSON object represents the full contents of one Wikipedia article.

Your **coding task** for this part of the assignment is to finish the currently-incomplete JSON loading code in the `WikiArticles` class, found in `WikiDataReader.py`. Specifically, you need to fill in to `TODO`s in the `_read_wiki_file` function such that the function will do the following for each line in the file `fp`:
1. Load the line as a JSON object (i.e., dict) using Python's `json` library
2. Extract the `name` attribute from the object (this represents the name of the Wikipedia article)
3. Extract the `abstract` attribute from the object (this represents the abstract, which is the summary text you see at the top of the article; this is the text we'll be searching over)

**Hint**: There are three `TODO`s in `_read_wiki_file`, and each one can be addressed using only one line of code. So, for each line, the only thing you should need to do is replace `= None` with some actual code that does what the `TODO` describes.

## Part 2: Information Retrieval with the Term-Document Matrix

Now that we've finished handling the data loading, it's time to dive into some information retrieval!

For this part of the lab (and Part 3), you will be working with the `wiki_search.py` file. This file can be run as a script on the command line with the following arguments:
```
usage: wiki_search.py [-h] -q Q [-n N] data_dir

positional arguments:
  data_dir              Directory containing the Wikipedia corpus files

options:
  -h, --help            show this help message and exit
  -q, --query Q         The search query to run
  -n, --num_articles N  Only keep the first N articles parsed from the corpus files
```

So for example, to run a search for "liberal arts college in southern california", I would run:
```
python wiki_search.py /courses/cs159/data/wikipedia/enwiki_sampled/ -q "liberal arts college in southern california"
```

Of course, if you try to run this now, it won't do anything, since we haven't yet implemented the bits that do information retrieval. Let's fix that!

_Note_: Notice the `--num_articles` parameter. When debugging, it may be a good idea to use that parameter to limit the size of the data you read in, so that the code can run faster and you can debug faster. However, **when answering analysis questions, you should not use the `--num_articles` parameter**, so that the script reads in all of the articles.

### TASK: Create a `WikiDocumentVectors` subclass that represents documents using a term-document matrix

`WikiDataReader` provides an _abstract base class_ `WikiDocumentVectors`, which specifies a standard interface for working with vector representations of documents. The advantage of this approach is that it will let us write generic code where we can swap out what vector representation we are using for IR. 

Just so we have something to start with, let's implement the most basic type of vector representation: a term-document matrix, like the one we learned about in class. Recall that a term-document matrix is a matrix $$T$$ which is indexed by documents on the rows and vocabulary words on the rows, such that $$T[i,j]$$ is the count of word $$j$$ in document $$i$$.

As described, it sounds like computing a term-document matrix is a lot of work: we would need to tokenize text, build a vocabulary, then compute counts for all words in all documents. That sounds...doable, but tedious! Thankfully, we can avoid the tedium, because scikit-learn provides a _single_ class that does all that work for you: [`TfidfVectorizer`](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html). That means, unlike in previous labs, you won't need to think at all about tokenization or vocabulary building; `TfidfVectorizer` will do it all for you and then just give you the term-document matrix! Aren't libraries amazing???

(Okay, I lied a little bit; the term-document matrix returned by `TfidfVectorizer` is slightly different from the one we defined in class. It is still based on word frequencies, but it then reweights those frequencies. For the purposes of this class, you don't need to know the details of this reweighting, you just need to know that it downweights super common words like "is" or "the" and that this ends up being empirically useful for IR.)

At the top of `wiki_search.py`, you should **write a subclass of `WikiDocumentVectors`**, which you should call `CountDocumentVectors`. This subclass should use `TfidfVectorizer` to both build a term-document matrix for the Wikipedia corpus (the `train` function), and create vector representations for any new search queries that come in (the `get_document_vector` function).

When initializing a `TfidfVectorizer` instance in your implementation, **you must use the following settings**:
- Set the maximum vocabulary size (`max_vocab`) to 10,000.
- Ignore any words that appear in more than 70% of the documents. This can be achieved using the `max_df` parameter. This is the same thing as the `stop_words` argument you saw in `PCLVocab` in Lab 3&mdash;a way of excluding the most common words from the vocabulary so that they don't add noise to the process.

**Hint**: if you use `TfidfVectorizer` properly, every function in your new subclass (the constructor, `train`, and `get_document_vector`) should only require **one line** of code. This is because your new subclass basically just acts as a thin wrapper around `TfidfVectorizer`.

### TASK: Finish the implementation of `WikiSearchEngine`

We have provided a near-complete implementation of an information retrieval engine in the `WikiSearchEngine` class. This class is a _generic_ implementation of information retreival: you give it the vectors to use, and it does the task of computing cosine similarities to find the most similar documents to a query. 

Right now, most of the code is complete but it is missing the most important part: the part that actually takes the query vector and computes its cosine similarity with all document vectors. This missing part is marked with a `TODO`, and your job is to **fill in the missing code to compute cosine similarities**. Just like in Lab 4, you should **avoid using `for` loops**. Once again, it is possible to compute all the cosine similarities at once using a _single_ numpy operation!

### TASK: Use your `CountDocumentVectors` subclass in `main`

Finally, there is one more `TODO` in this file, which is found in `main`. After parsing the command line arguments, `main` is supposed to initialize a vector model (i.e., a subclass of `WikiDocumentVectors`) as `doc_vector_model`, and pass the model to `WikiSearchEngine`. As indicated by the `TODO`, you simply need to fill in the code that initializes the model. Right now, we only have one subclass of `WikiDocumentVectors`, which is the `CountDocumentVectors` that you implemented above. So, that's what you should initialize `doc_vector_model` as.

Once you have finished all the coding tasks in this part, you should be able to successfully run queries! Answer the following analysis questions:

### Analysis Question #1

Try the following queries:
- "first manned mission to the moon"
- "liberal arts college in southern california"
- "marvel movie about an insect themed superhero"

For each query, paste in your journal the results you get, and then discuss: how satisfied are you with these results? Do at least some of the top matches appear relevant to the query? Do you think that at least one of the returned articles matches the _intent_ of the query? For results that seem clearly incorrect, briefly speculate on what is causing them to score highly for the query.

### Analysis Question #2

Come up with 4 queries of your own, in the following categories:
- 1 query where you think a human would have an easy time finding the answer, and where you expect your IR system to get it right
- 1 query where you think a human would have an easy time finding the answer, but where you expect your IR system to get it wrong
- 1 query where you think a human would have a hard time finding the answer, but where you expect your IR system to get it right
- 1 query where you think a human would have a hard time finding the answer, and where you expect your IR system to also get it wrong

Briefly justify your reasoning for each query. Then, report the results of running your IR system for each query. For each query, did the IR system behave the way you expected, or were you surprised? Why?

## Part 3: Information Retrieval with SVD

As discussed in class, one problem with using the term-document matrix for IR is that cosine similarity on those vectors depends on exact word overlap. One solution we discussed was to learn higher-level "concepts" using SVD&mdash;a rudimentary version of the larger concept of _vector semantics_. In this part of the lab, you'll apply SVD to the term-document matrix to transform your original document vectors into "concept space" where each column represents some higher-level concept that is correlated with multiple words. Then, you'll observe how this transformation impacts the search results.

### TASK: Create a `WikiDocumentVectors` subclass that represents documents using SVD

Our main information retrieval system in `WikiSearchEngine` was designed to be generic, so thankfully as long as we simply implement an SVD vector model, we can just plug that model in to `WikiSearchEngine` without having to change any of the IR code. To this end, you will **create a new `WikiDocumentVectors` subclass**, which you should call `SVDDocumentVectors`. 

Remember that SVD works by _transforming_ a term-document matrix, so `SVDDocumentVectors` will share quite a bit of code with your previous `CountDocumentVectors`. Therefore, you should **start by copying the code from your `CountDocumentVectors`** (yes, I know copy-pasting code is considered bad practice, but we'll let it slide for this lab since otherwise we'd need to overcomplicate the inheritance structure of the classes...) . Then, you can proceed by making a few simple modifications:

1. In `train`, instead of directly returning the term-document matrix, you should instead run SVD on it. The matrix you get from `TfidfVectorizer` is a _sparse matrix_ (which you learned about in Lab 3), so to do SVD on it, you can use the [`svds` function from the `scipy.sparse` library](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.linalg.svds.html). **For the number of latent dimensions (i.e., the number of "concepts" we want to learn), you should set the `k` parameter to 100.** `svds` will return three matrices: `U`, `s`, and `Vh`. These correspond to $$U$$, $$\Sigma$$, and $$V^T$$ from the lecture. As a reminder, $$U$$ contains the document vectors in "concept space" (so, this is what you want to return), $$\Sigma$$ contains the singular values (which, for our purposes, we'll ignore), and $$V^T$$ contains the word vectors in "concept space" (but transposed so the words are on the columns). **You will want to hang on to $$V$$ somehow, as we'll need it later!**
2. In `get_document_vector`, after computing the query vector using `TfidfVectorizer`, you need to similarly transform that vector to "concept space". As a reminder, this can be done by a matrix multiplication with $$V$$ (which, as noted above, you should have somehow held on to after training!). As usual, be careful about the matrix shapes when doing matrix multiplication! Intuitively, the result we want is a _single_ vector representing the query in "concept space". So, this should be a single-row 1x`k` matrix, where `k` is the number of latent dimensions (which we previously set to 100). I recommend printing the matrix shapes to make sure you're getting them right; don't hesitate to reshape and/or transpose as needed!

### TASK: Add a new command-line argument to enable the use of SVD vectors

Now that we've defined our SVD vector model, we need to **modify the `main` function** to let us specify that we want to use the SVD vectors in `WikiSearchEngine`. Make the following modifications to `main`:

1. Add a new argument `--model` / `-m` to the argument parser. This argument should be a string that can take two possible values: "tfidf" or "svd", and the default value should be "tfidf".
2. Modify the initialization of the `doc_vector_model` variable, which in Part 2 you initialized as a `CountDocumentVectors` object. You should add an `if` statement so that if `args.model` is "svd", then `doc_vector_model` instead gets initialized as a `SVDDocumentVectors` object. If `args.model` is "tfidf" (the default), then `doc_vector_model` should remain as a `CountDocumentVectors` object.

Once you have made these changes, then running:
```
python wiki_search.py /courses/cs159/data/wikipedia/enwiki_sampled/ -q "liberal arts college in southern california" -m svd
```
should run the same "liberal arts college in southern california" search from before, but using SVD vectors.

Use this setting to answer the following analysis questions:

### Analysis Question #3

Re-run the same queries from Analysis Question #1 using your new SVD vector model, and paste the new results in your journal. What changed? For each query, do you think the new results are better or worse than before? Why?

### Analysis Question #4

Re-run your queries from Analysis Question #2 using your new SVD vector model, and paste the new results in your journal. What changed? Do the new results better align with your expectations? For the two queries you originally expected the IR system to get wrong, does the system now get them right?

## Part 4: Word similarity with SVD

We learned in class that one neat thing about SVD is that it doesn't _just_ transform the document vectors into "concept space"; it _also_ creates vectors for each word in the vocabulary, in the same "concept space"! This allows us to compare how related two words are (for example, in the lecture we said that "cold" and "frozen" would be similar since they would both point roughly in the direction of the "coldness" dimension).

We have provided another script, `wiki_word_lookup.py`, to help you play with these word vectors. If you examine the code, you should find that it looks nearly identical to the `wiki_search.py` script. This is by design; fundamentally the two scripts are doing the same thing (computing cosine similarities of vectors), it's just that the vectors involved mean different things. Like `wiki_search.py`, `wiki_word_lookup.py` is nearly complete, you simply need to fill in the "holes" (which, roughly, are the same as the "holes" in `wiki_search.py`):

### TASK: Create a `WikiWordVectors` subclass that represents words using SVD

First, you need to implement the word vector model that `WikiWordLookup` will use. To this end, you will **create a subclass of `WikiWordVectors`** that you should call `SVDWordVectors`. Remember: we are running the _same_ SVD as we did in the original document search! Therefore, you can start by **copying the relevant SVD code from your `SVDDocumentVectors`**. Note however that the `WikiWordVectors` interface is slightly different; in addition to `train`, you will have to implement:
- `get_word_index`: given a word (as a string), this function should return the index of that word in the vocabulary that was learned by `TfidfVectorizer`. This is necessary so we know which row of the $$V$$ matrix to look up to find the vector for a given word.
- `get_vocabulary`: this function should return the vocabulary that was learned by `TfidfVectorizer`, as a list. We can later use this to do the inverse of `get_word_index`: given an index, we can find what the word at that index is.
- `get_word_vector`: this is the equivalent of `get_document_vector`, but because we only know about words that are in our vocabulary, we don't need to generate a new vector; given a word, we simply need to look up its row in the $$V$$ matrix. You can use `get_word_index` as a helper function here!

### TASK: Finish the implementation of `WikiWordLookup`

Just like in Part 2's `WikiSearchEngine`, `WikiWordLookup` is just missing the bit of code that computes the cosine similarities. Your job is to fill in that missing bit. A major hint here is to remember that `wiki_word_lookup.py` is fundamentally doing the same thing as `wiki_search.py`; cosine similarity is just math that doesn't care about what the vectors "mean". Therefore, you should not need to write any new code here; the code should be **identical to its counterpart from `WikiSearchEngine`!** (yay, more copy pasting...)

### TASK: Use your `SVDWordVectors` subclass in `main`

OK, now _I'm_ literally just copy-pasting instructions from the previous sections...

> Finally, there is one more `TODO` in this file, which is found in `main`. After parsing the command line arguments, `main` is supposed to initialize a vector model (i.e., a subclass of `WikiWordVectors`) as `word_vector_model`, and pass the model to `WikiWordLookup`. As indicated by the `TODO`, you simply need to fill in the code that initializes the model. Right now, we only have one subclass of `WikiWordVectors`, which is the `SVDWordVectors` that you implemented above. So, that's what you should initialize `word_vector_model` as.

Once you have finished all the coding tasks in this part, you should be able to successfully search for words that are similar to a given word! Answer the following analysis questions:

### Analysis 

### Analysis Question #5

Run `wiki_word_lookup.py` on at least 2 of the following words:
- "college"
- "frozen"
- "science"
- "programming"
- "anime"

In addition, run it on 3 more words of your choice. For all lookups, paste the results in your journal. What do you think of the results? Are any of them surprising?

### Analysis Question #6

Try running `wiki_word_lookup.py` on the word "male". Then, run it on the word "female". Compare the results: what stands out to you? Is there anything you find troubling, or otherwise unsatisfying, about how the "female" results look compared to the "male" results?

Do the same comparison and analysis for the following pairs of words: "black" vs "white", and "africa" vs "europe"

## Part 5: Choose Your Own Adventure

![Anyone remember these books?](https://alchetron.com/cdn/choose-your-own-adventure-62af4720-802a-4570-b998-c9f991ccffa-resize-750.jpeg)

For this last part of the lab, we want to give you some experience finding new Python libaries and learning to use them with minimal guidance from us. This will likely be something you have to do quite a bit of in your final project!

There are two "paths" in Part 5; since this lab has already gotten quite long, **you are only requrired to do ONE of the paths, Part 5a OR Part 5b**. That being said, your experience with the lab may be most "satisfying" if you do both! To sweeten the deal, anyone who completes both paths is eligible to receive **5 points of extra credit on the midterm!** The midterm is out of 100 points, so 5 points is exactly equal to 5 percent&mdash;that's not small! However, **you must explicitly indicate on your journal that you are intending to claim the extra credit points**. Failure to do so means you are forfeiting the extra credit points, even if you complete both paths!

Both paths are related to same idea: at the end of lecture, we explained that SVD has limitations as a system of vector semantics, and that in practice, neural network based approaches can capture more intuitive "concepts" and therefore lead to better search results. Part 5 will have you explore such neural network vector models; Part 5a focuses on models for _document vectors_ and Part 5b focuses on models for _word vectors_.

**Hint**: This part is asking you to find Python libraries that implement these neural network approaches. Whatever library you find probably won't be already installed in your environment. But the benefit of using virtual environments is that you can always install whatever libraries you want in the environment using pip, no admin permissions needed!

## Part 5a: Neural network document vectors

Your task here is to do some research to find Python packages that implement models for representing documents as vectors. As a hint for your search, these are more commonly referred to as _embedding models_. You can use whatever resources you want for your search&mdash;search for "python embedding models" on google, do a paper search, ask ChatGPT, talk to your peers who have done summer research, or ask a prof! Whatever approach you use, make sure to **document your search process in your journal.** As another hint, one popular (relatively) modern embedding model is BERT; maybe that can help guide your search (but you can also find other models!)

Once you have found a library that you can use to compute document embeddings, you should integrate it into `wiki_search.py` by implementing a new subclass of `WikiDocumentVectors` that uses the library you found to create vector representations of documents (and queries). You will also want to modify `main` to allow the script to use this new subclass. Then, answer the following analysis questions:

### Analysis Question #7a

Re-run the same queries from Analysis Question #1/#3 using your new neural network vector model, and paste the new results in your journal. What changed? For each query, do you think the new results are better or worse than before? Why?

### Analysis Question #8a

Re-run your queries from Analysis Question #2/#4 using your new neural network vector model, and paste the new results in your journal. What changed? Do the new results better align with your expectations? For the two queries you originally expected the IR system to get wrong, does the system now get them right?

## Part 5b: Neural network word vectors

Your task here is to do some research to find Python packages that implement models for representing words as vectors. As a hint for your search, these are more commonly referred to as _word embedding models_. You can use whatever resources you want for your search&mdash;search for "python word embedding models" on google, do a paper search, ask ChatGPT, talk to your peers who have done summer research, or ask a prof! Whatever approach you use, make sure to **document your search process in your journal.** As another hint, one famous word embedding model is word2vec; maybe that can help guide your search (but you can also find other models!)

Once you have found a library that you can use to compute word embeddings, you should integrate it into `wiki_word_lookup.py` by implementing a new subclass of `WikiWordVectors` that uses the library you found to create vector representations of documents (and queries). You will also want to modify `main` to allow the script to use this new subclass. Then, answer the following analysis questions:

### Analysis Question #7b

Repeat Analysis Question #5 using your new neural network vector model, and paste the results in your journal. How do these results compare to the ones from Analysis Question #5? Do you think that this model capture more "humanlike" concepts than SVD? Are there any word similarities you still find confusing or incorrect?

### Analysis Question #8b

Repeat Analysis Question #6 using your new neural network vector model. Have the results significantly improved, in terms of things you were originally troubled or unsatisfied by? Or perhaps have the results gotten worse? In either case, why do you think this is?