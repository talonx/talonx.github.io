---           
layout: post
title: Discovering fast.ai
date: 2023-06-05 18:00:46 IST
comments: true
categories: machine-learning
---

I recently discovered the fast.ai deep learning library and its related course and the [book](https://course.fast.ai/Resources/book.html).

The course at [fast.ai](https://course.fast.ai/) has the stated aim of getting programmers up to speed using deep learning to solve practical problems. Most guides to deep learning (and machine learning in general) advise you to start bottom-up. They start with the mathematical foundations - which can be daunting - and slowly build on those. This approach can take years, and with good reason. But that's not the only way to use ML anymore.

There's no doubt that to do original research in machine learning or to write a new ML library you need to know the foundations well. However, we are at a stage where there are many more roles available to people interested in "doing" machine learning. You can choose to be a PhD and work on cutting edge research - but not all of us can or want to go down that route. Some of us are more interested in applying ML techniques to solve problems with the option of going deeper if needed. This is what libraries like fast.ai facilitate. What I love about this course is the focus on getting results first with its top-down approach.

The course has 2 parts. Part 1 is introductory and provides an overview of various ML techniques and abstractions with working examples. Lesson 1 itself teaches you how to build an image classifier using fast.ai's library. Part 2 goes deeper into the theory. 

If you're working through Part 1, here are some guidelines I found helpful

- You might have to walk through each lecture 2 or more times before you get it, and that's fine.
- Work through the code in the lectures, and the notebooks, until you can replicate the results.
- As you reach Lesson 3 (corresponding to Chapter 4 in the book) you might suddenly face concepts which are alien if you are new to Pytorch and ML in general, and also a bit of math. What worked for me was to keep going at it until I understood them.
- It's important not to digress into taking another course or starting to learn another framework in the middle because you think it might help you grok some concepts. For example, when tensor operations are first mentioned, you might want to go and checkout a PyTorch tutorial. Don't - just read the docs for those operations and understand what they are doing at a high level. At some point, understanding numpy and PyTorch are necessary but you can get by in this course without that. The same is true for neural networks and concepts like ReLU.
- It's ok to first finish the video lessons and read the book later. Attempting both concurrently is difficult because they follow slightly different paths, but they complement each other in the end.
- When you need help, ask on the forums or in the Discord server - people are very helpful. In general, it's a super helpful community.

Others [have](https://medium.com/@init_27/how-not-to-do-fast-ai-or-any-ml-mooc-3d34a7e0ab8c) [mentioned](https://kurianbenoy.com/posts/2021/2021-06-16-fastgroup-1.html) [some](https://www.alexstrick.com/blog/fastai-lesson-zero) of these tips - and I am thankful to them.
