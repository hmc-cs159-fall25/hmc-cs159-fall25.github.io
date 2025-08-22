---
permalink: /labs/grading/
title: "Lab Journaling Instructions and Grading Rubric"
---

_Note: the journaling system is an experiment I'm trying out this semester, inspired by other recent "alternative grading" experiments that have been conducted in the CS Education space (including in our very own CS5). I'm rather excited about it, but as with all new things, it may or may not work as expected. Please do not hesitate to give me your feedback and suggestions at any point during the semester!_

## Motivation

Oftentimes, what distinguishes highly successful NLP projects from less successful ones is not the quality of the code or the sophistication of the mathematics. Rather, it is the ability of the project developers to successfully _interpret_ their model and their findings, often by drawing insights from linguistics, social sciences, and biology (for example: modern-day LLMs are fundamentally powered by a mechanism known as "attention"—to be briefly discussed later in this class—which was _very_ loosely inspired by similar mechanisms in the human brain!). Yes, it's important to know how to write code for a part-of-speech tagger, but it's far _more_ important to be able to analyze why, based on your understanding of the model and the data it was trained on, the tagger is likely to make certain kinds of mistakes.

All of this is to say: I care less about your actual code and more about your underlying thought process. And the best way for me to see your thought process is, well, to have you _literally_ write down your thought process!

## Journaling instructions

To this end, for each lab assignment you will be asked to record and submit a real-time _journal_ that details all the things you tried as you worked on the assignment. "**All** the things you tried" is the key point here: when working on an assignment (or any other coding project), it is often the case that the first few things you try don't quite work. Therefore, in a _traditional_ class environment, the code/writeup you end up submitting only reflects the thing that ended up working. But here it's the opposite: I want to know all the details of "how you cooked" your final submission, how you _got_ from a not-working to a working state. And importantly, I want to see your debugging process: when you see that something has gone wrong, I don't just want to hear you say "it didn't work", I also want to hear your speculation about _why_ it didn't work.

(Of course, it is also the case that some things you try _will_ work. When that happens, I also want to hear it; after all, success is worth celebrating!)

To be more specific, you journal should include all of the following:

- High-level ideas you have as you figure out how to solve a problem in the assignment. (example: "My first thought was to use Python's `Counter` class to handle the counting of tokens)
- Any difficulties you encountered. Note that "difficulties" doesn't _only_ include bugs, but also covers things like being unsure how to even start, having code that works but gives results that don't make sense, or even being confused about what the problem is asking. (examples: "The function worked for most test cases but gave counts that were off by one on the file `example.txt`"; "I know the first thing we need to do is tokenize but I'm not sure how to further filter the tokens")
- Steps you took to resolve the difficulties. This can include things like (concisely) outlining your debugging steps, or citing resources that helped you solve the problem. (examples: "I opened `example.txt` and compared it to another input file that the function worked correctly on"; "I went to grutoring hours and the grutor told me to pay attention to how I handle line endings")
- Explicitly marked answers to **analysis questions** (see below).

There are no requirements about how you choose to format your journal; you can go with paragraphs or bullet points based on personal preference. Likewise, there are no requirements about how the journal is organized, other than it is _somehow_ organized so that it's readable (ideas include making separate sections for each day you worked on the assignment, or sections corresponding to sections in the assignment instructions).

### Analysis questions

In each lab assignment, there will be specifically marked analysis questions we want you to answer in your journal writeup. These questions will be clearly marked in the assignment as **"Analysis question #N"** (where N is a number). You can answer these _wherever_ you want in your journal (for example, some students may prefer to place them naturally in the middle of a narrative, while others may prefer to have a section at the end dedicated to the analysis questions); just make sure that you **clearly mark** the answers in your journal with the same header text **"Analysis question #N"** (the course staff will search for this pattern to locate your answers to the analysis questions).

### Journaling dos and don'ts

DO:

- Be complete. Include all ideas you had even if they didn't pan out, and include sufficient details about your problem-solving process that course staff can get a good sense of what you did.
- Be concise. This may seem to be in tension with the previous point, but the idea here is that you should be complete without including extraneous details. We don't need to hear something like "and then in the middle of working on this, I went to dinner".
- State when you got help from an outside source, and cite that source properly.
- Draw direct connections to material from the lectures or readings.
- Include example outputs where appropriate.
- Maintain your journal in real-time, as you work through the assignment.

DON'T:

- Include large blocks of code in your journal. You will submit your code separately so the course staff will have access to it. You should only include small snippets or plain-english summaries of your code as needed to explain your ideas.
- Blindly copy-paste entire output printouts. Oftentimes, the code will print a bunch of stuff but only part of that output is relevant to the problem at hand. Selecting and including only the relevant output both demonstrates that you truly understand what's being printed, and helps keep your journal more readable for us.
- Focus only on the code. In NLP, understanding the cause of a problem usually requires an understanding not only of the code itself, but also of the input data that the code is running on.
- Wait until the assignment is done to write your journal all at once. This defeats the purpose of journaling, and I guarantee you there will be details you forget if you wait until the end to write everything up.

## Journal submission and grading

For each lab, you will be asked to submit two things: a link to the GitHub repo for your code, and a PDF copy of your journal (you can use any method you want to write the journal, be it Google Docs or LaTeX or Markdown or anything else, you just need to export it as a PDF at the end). If you were working with a partner, you only need to submit one journal for both of you, but note that on the Honor Code, both partners must contribute roughly equally to the submitted journal.

You may notice that we are having you simply provide a link to your GitHub repo rather than directly submitting your code to Gradescope. This is because **we will not be running an autograder on your code, and in fact will not be directly grading your code at all**. Instead, **your lab grade will be determined purely based on your journal**. I recognize that this may be a somewhat alien experience to many of you, but my hope is that this adds some flexibility and nuance to the grading process. In fact, with a good journal, you may be able to get a lot of credit on an assignment even if your code doesn't work perfectly!

Journal grading is based on four criteria: **Insight**, **Correctness**, **Completeness**, and **Organization**. Each criterion is judged on a 4-point scale. Just to help calibrate expectations: the scales are designed such that it should be practically impossible to get a score of 1 (barring cases of academic dishonesty or almost entirely incomplete submissions), and I expect that a solid understanding of course content paired with a good-faith effort is highly likely to get at least a 3.

[Click here to view the rubric we will use to judge all four criteria.](https://docs.google.com/document/d/1jcSjJ7xsHyQm6iiWvdkAOoh4QcitZCwpPuOQbrJ3OMs/edit?usp=sharing)

_Note: As stated at the top of the page, this whole journaling system is an experiment. It is quite likely we will need to iterate on the rubric a few times throughout the semester. I will make an announcement any time there is a major revision to the rubric. Once again, please don't hesitate to contact me with feedback or ideas throughout the semester!_