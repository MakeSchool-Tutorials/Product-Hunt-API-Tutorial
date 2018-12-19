---
title: "Concluding"
slug: conclusion
---

Congrats on building an iOS Product Hunt Reader! If you followed this tutorial then a user of your product should be able to accomplish the following:

**As a user...**

- I can browse products featured today on Product Hunt by scrolling through the app’s main screen.
- I see each product’s name, tagline, number of votes.
- I can tap on each post to see its comments in descending order.

If a user can accomplish all three of those tasks, then your product is a success!

# What You Learned

Satisfying your users is great, but more importantly, you learned **a lot** of new tools you can add to your iOS engineering tool-belt! Through building this app, you should have learned:

## How to work with APIs.

We learned how to dig into the Product Hunt API via Postman, which helped inform us how to build out our models

## How to build a network layer in Swift.

We built an entire network layer to interact with the Product Hunt API and fetch the appropriate data from it.

## How to decode JSON into Swift models.

We learned what being `Decodable` means, and how we can use `Decodables` to integrate `JSON` data into our app.


## How to take advantage of mock data.

Setting an app up with real data right away is difficult, and also can lead to dealing with more problems if not everything in your views is set up correctly.

It's much easier to set up mock data (in the format we expect to receive the data) so that we can make sure everything is wired up correctly. It also lets us focus on building out our views before worrying about the data plumbing.


## How to use different data types to make your URL composable
???



## How to display data in tableviews with custom UI.

We used **a lot** of `UITableViews` and got a lot of practice tweaking the UI to suit our product's needs.

# Challenges

If you're still hungry for more, here are some extra challenges to improve upon your product:

- We still don't have images being pulled into the app! Replace the `placeholder` image with the actual images of the products from the Product Hunt API
- Allow users to filter by `topics` (check out the `post` data from the Product Hunt API)
- For each product, show the `maker` of that product by displaying their name and picture underneath the product `tagline` (Product Hunt API `post` data can help you here too!)
