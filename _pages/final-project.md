---
permalink: /final-project/
title: "Final Project"
---

For your final project, you will be writing an ACL-style "short paper" and giving a short presentation on a work of your choice. Your grade will be split across a brainstorming session (5%), a short proposal "pitch" (5%), a literature review (10%), participation in peer review (10%), a presentation (20%), and a final paper (50%). Projects must be done in groups of 2-4 students (this could be the same as your midterm group, but does not have to be).

## Part 1: Project Brainstorming (Due Nov 7)

**This is the ONLY part of the project that may be done individually!**

**This is the ONLY deliverable that is not on Gradescope!**

During the week that Prof Chang is out, you will have the chance to work collaboratively with your peers to brainstorm project ideas. At this stage, you do not have to have fully formed ideas; that's why it's called "brainstorming"! We simply want you to start thinking about what some cool, fun projects might be.

You will add your ideas to the following spreadsheet: [link](https://docs.google.com/spreadsheets/d/16VZn25HTGeTUmTULKomuaUJKEJbdFjzh04-IGzLI348/edit?usp=sharing)

To receive full credit, you MUST **contribute at least one idea** and **"upvote" at least one other person/team's idea** ("upvote" just means indicating you like the idea and would be open to working on it). Of course, each person can contribute more than one idea (the more the better!) and upvote more than one idea, and each idea can be upvoted by multiple people (in fact, upvoting is one possible way to find a team!)

At this stage, we do not necessarily expect that you have formed teams yet, so you are free to contribute ideas individually. Of course, if you already have a team in mind, you can submit your idea together as well; just make sure all your names are listed.

If you are unsure how to come up with ideas, here are some suggestions:
- Read some papers (the EMNLP paper list on Gradescope is a great starting point!)
- Read some general articles about NLP/AI (Prof Bachman has a great substack about AI!)
- Look in the textbook
- Look at the [SemEval 2026 Tasks](https://semeval.github.io/SemEval2026/tasks.html) (these are "challenges" that NLP researchers around the world are working on as we speak!)
- Talk to friends/relatives/colleagues about needs they might have for which a tool doesn't currently exist
- Talk to your classmates; though Prof Chang is out, you are welcome and encouraged to still go to our classroom and chat with each other about project ideas!

When Prof Chang is back, he will provide feedback on the ideas (which may help you narrow down which idea to formally propose).

## Part 2: Project Pitches (Due Nov 18)

Your assignment is to write a one-paragraph project pitch. Each pitch should clearly state what research question you're attempting to answer, what data you'll use (**including a specific example of raw data that you have collected**, in order to demonstrate that your proposed dataset actually exists), what experiments you want to run, and what metrics you'll use to evaluate your experiments' output.

You'll write this up using LaTeX; please submit both the PDF and LaTeX file on Gradescope so I can conveniently pull out the text of your proposals for the shared projects list. Since you'll use it later for your projects, I recommend taking [this template](https://www.overleaf.com/latex/templates/association-for-computational-linguistics-acl-conference/jvxskxpnznfj) to start from. You are welcome to get rid of all of the formatting except the title and authors for now.

It's a tricky thing to come up with a project from scratch, particularly when you don't have a lot of time. Here are some tips for the task:

1. **Make your research question clear and concrete.** Getting a good research question is a subtle thing: borrowing terms from this helpful advice post, you want it to be (a) clear to an audience of your classmates, (b) focused enough that you will be able to address it with one or two narrow experiments, (c), concise enough to state within the first few sentences of a paragraph, (d) complex enough that the answer isn't immediately evident, and (e) arguable, in the sense it should be possible to provide evidence that supports or rejects an answer to your research question.

2. **Pick a clean, ready-to-use dataset.** Dataset processing takes a long time. You'll probably need to do some no matter what (and I encourage you to borrow code from the labs to process XML or use spaCy to do so), but you should probably plan to use a dataset that is already prepared for processing. Good sources of these datasets include leveraging existing shared tasks (for instance, SemEval and CoNLL tasks and the GLUE benchmark) or datasets that have been used for lots of NLP processing in the past. Websites like [Kaggle](https://www.kaggle.com/datasets) or [the UCI Machine Learning Repository](https://archive.ics.uci.edu/) are great places to find pre-prepared datasets. Alternatively, several Python software packages including [HuggingFace Datasets](https://huggingface.co/docs/hub/en/datasets) and [ConvoKit](https://convokit.cornell.edu/) (which Prof Chang is a main contributor to!) offer easy ways to download cleaned, formatted datasets directly in Python code. Finally, if you choose to scrape a dataset from scratch, note that some platforms are easier to get data from than others; like anything related to [Wikimedia](https://dumps.wikimedia.org/) (e.g. Wikipedia) or [StackExchange](https://archive.org/details/stackexchange) (e.g. StackOverflow). **If the process to acquire the dataset for your project takes more than 24 hours or costs money, it's not an option for this class.**

3. **Keep things narrow.** It's okay if your project doesn't create a new dataset, model, and evaluation all in one swoop! The smaller the scope is for what you're doing, the easier it will be to provide evidence that you did it well. For instance, you could (a) perform a replication study (e.g., take existing code for an experiment and check that it's doing what it says, plus break down their results a bit more); (b) take a large unstructured dataset and curate it into one that helps answer a more particular question + show a simple model works on it; (c) try to get a good result on an existing shared task/analyze the contents of the text of a shared task to see what parts are "easy" or "hard"; or (d) create a new metric/evaluation and show it does something interesting.

## Part 3: Literature Review (Due Nov 25)

Part of your final paper for your project will include a discussion of related works to your paper. For this deadline, I would like you to work on compiling discussion of papers related to the project you are undertaking in order to describe what those projects do. You will **modify your writeup from the project pitch** to add two NEW sections: a **related work** section and a **methods** section (note that headers for both sections are already present in the LaTeX template, but you may have deleted them when turning the template into your pitch).

**Related Work**: Oftentimes, you've probably seen or written a section in papers called "Related Work" which, when executed best, describes scholarly works that are closely related to your project and how your project differs. This includes projects solving the same problem in a different way, projects solving slightly different problems with the same or a similar strategy to yours, projects on which your project builds, etc.

The amount of detail you want to put into this part may depend on how comparative your work is; for instance, while the [Centroid-Based Text Summarization](https://aclanthology.org/W17-1003.pdf) paper has a fairly succinct Section 2 that quickly addresses the context of their model, the [NarrativeQA](https://direct.mit.edu/tacl/article/doi/10.1162/tacl_a_00023/43442/The-NarrativeQA-Reading-Comprehension-Challenge) model spends a lot longer talking about similar works because illustrating that distinction is part of the core argument of their paper. I would guess most projects will fall somewhere in between these two, but make sure this isn't just a grocery list of papers that have similar keywords. You don't have to thoroughly read every paper you cite, but you should know enough about them to be able to succinctly say how what you're doing is similar and different to what they did.

To compile these papers, the resources above and Semantic Scholar or Google Scholar are helpful starting points. If you find important papers, textbook chapters, or pages referencing this topic, I'd encourage you to look at the bibliographies of those papers to find out what the core papers are that people cite in this subfield. Google Scholar usually has a "Cited by ##" link that you can use to find more recent papers that cited some fundamental paper; for example, here's what [Google Scholar gives me for papers that cite the NarrativeQA paper](https://scholar.google.com/scholar?cites=15318882709554150182&as_sdt=2005&sciodt=0,5&hl=en). If you're not sure where to start, the textbook or Wikipedia page may give you some starting paper links, but don't let the textbook be your main resource; go find primary sources!

**Methods**: In a standard NLP experimental research paper, citations are not only found in the Related Work section; they're scattered throughout the paper, as they help motivate the introduction, describe the evaluations, and contextualize the results of an experiment. For instance, the previously mentioned centroid-based summarization paper uses citations for what word embedding learning algorithms and weighting they used, the basis for their centroid selection algorithm, what metrics they used for evaluation, and where they got their datasets.

I'd like you to write out a citation-enriched plan of what you're going to use to assemble your project, including models, evaluations, libraries, datasets, and published strategies. Note that your job here isn't to justify why these choices are the right ones, it's to document where those choices came from in the existing literature (which may actually turn out to be all the justification you need). This isn't a contract with me for what your project will look like, but it's likely to help you get a good checklist for what you'll need to get running. Again, try to find primary sources if you can; while Jurafsky & Martin describes how to use tf-idf, for instance, it's appropriate to cite the original 1986 Salton & McGill paper as your source if you use tf-idf (as the centroid paper does). Similarly, for many datasets, there's an associated paper that introduces the dataset, and it's appropriate to cite that paper when you first refer to the dataset in your paper. Only in cases where _absolutely sure_ that there is no associated paper are you allowed to cite a dataset by URL.

There is no maximum or minimum length for this, because depending on the project, there may be more or less to focus on. I'd guess most of these will be 1.5-2 pages, which is about how much space the literature review and methods sections take up in an average ACL short paper. I also expect roughly 8-15 citations, maybe 3 of which you engage with more deeply to compare your work. While I expect many of the citations will come from NLP papers, I also expect some may come from other domains, like gender studies, linguistics, or political science.

IMPORTANT: please formally cite all your sources with proper bibliography styling. In LaTeX, this can be done using `bibtex` (if you are new to this, [see here for a tutorial](https://www.overleaf.com/learn/latex/Bibliography_management_with_bibtex)). Google Scholar can generate `bibtex` entries for you; but you should always double-check them since it sometimes gets things wrong (most notably, for some reason Google Scholar often defaults to the arXiv version of a paper if one is available, whereas for your citations you should always prefer citing the official journal/conference proceedings).

## Part 4: Presentation (Dec 2 and 4)

To present the core problem you're working on, your approach, and a little of what you've done so far, you'll give a 10 minute in-class presentation describing your project. 10 minutes is a misleadingly short amount of time, so you'll want to quickly get to the core research question you're addressing and a quick idea of what results you have so far. Of course, this presentation is happening _before_ your final writeup is due, so it is understandable and expected if your project is not in a complete state yet. The goal of the presentation is more to be a "progress report" rather than a "final" presentation.

Presentations will take place during the last week of classes, on Tuesday Dec 2 and Thursday Dec 4. For fairness, presentation slot assignments will be randomly decided, and the assignments will be sent out one week prior.

Presentations will be graded on the following:

- **Problem statement** (20 pts): Does your presentation clearly establish what the problem or research question is that your project addresses? This should be concrete and something for which you can provide evidence: a guiding question like "how does gender affect translation" isn't a concrete research question, but "how does signaling speaker gender to a machine translation system affect the quality of translations" is.
- **Background** (10 pts): Is it clear what existing work you're building off of/comparing to? You don't have to mention all related work, but it'd be good to mention a couple of close neighbors to your project/historical context for your project so someone can understand your specific contribution.
- **Progress** (10 pts): Is it clear what you've done so far, and what's left to do? This can be a combination of initial results (with tables and/or plots) and a description of steps left to take.
- **Slides** (10 pts): Do the slides help communicate your ideas in a clear and effective way? Good slides give enough information to visualize or support the argument you're making, but won't necessarily have text for everything you say (in fact, many of my slides for technical presentations have no text at all). A good rule for slide decks is that most people average about a minute of speech per slide. You will need some sort of plot, figure, screenshot, or table in your slides showing at least a partial result to get full points.

## Part 5: Final Writeup (Dec 12 / Last day of finals)

_Prof Chang is still working on this part; check back soon!_
