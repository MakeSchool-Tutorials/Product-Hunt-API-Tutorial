---
title: "Get Started."
slug: getting-started
---

Imagine that you're a developer who is hired to create a Product Hunt reader that shows the currently featured apps on Product Hunt.

The first feature the client wants you to implement is the feed, which displays all of today's featured products on the Product Hunt. Build a quick prototype to shows the client all the core features in action.

# Learning Outcomes

By the end of this tutorial, you should know...

1. How to work with APIs.
1. How to build a network layer in Swift.
1. How to decode JSON into Swift models.
1. How to take advantage of mock data.
1. How to display data in tableviews with custom UI.

# What We're Building

![Preview final product](assets/final-product.png)

## User Stories

**As a user...**

- I can browse products featured today on Product Hunt by scrolling through the app’s main screen.
- I see each product’s name, tagline, number of votes.
- I can tap on each post to see its comments in descending order.

# Implementation Plan

We're going to building this app outside in; starting with the UI of the app and ending with the networking layer that communicated with the Product Hunt API.

This makes it quick and easy to build out the app and test some of its features before making requests to the API.

# Using Git/GitHub

As you go through this tutorial, you will also be making commits after completing milestones. **This is a requirement, you must make a commit whenever the tutorial prompts you**. This not only further enforces best practices for software engineering, but also will help you more easily figure out where a bug originated from if you break your progress up into discrete, trackable chunks.

When prompted to commit, you'll see a sample commit message. Feel free to use your own message, so long as it clearly and concisely covers the work done.

Lastly, the commit prompts in this tutorial should be the **minimum** amount of times you commit. If you want to do more commits, breaking your chunks into even smaller chunks, that is totally fine!
