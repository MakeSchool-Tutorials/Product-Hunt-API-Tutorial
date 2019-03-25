---
title: "Creating The Post Model"
slug: post-model
---

1. ~~Look at the Product Hunt API~~
1. ~~Build the Feed View~~
1. **Create the Post Model**
    1. **Implement the struct**
    1. **Implement the properties needed**
1. Build the Post Cell
1. Create the Post Cell Class
1. Test the Feed Table View
1. Allow the Post Model to work with network requests
1. Create the network layer
1. Retrieve data from the PH API
1. Build the Comments View
1. Pull Comments data from the API
1. Update the view controllers to hook everything up

Let's model the information we will receive for each posting. The properties we need are the product's ID, name, tagline, number of votes, and number of comments.

> [action]
> Create a new Swift file and call it `Post.swift`.

We'll use a `struct` to define the variables we want to get from the Product Hunt API.

> [action]
> Create a Struct called `Post`.
>
```swift
/// A product retrieved from the Product Hunt API.
struct Post {
>
}
```
>
> Give `Post` variables that will hold a product's id, name, tagline, number of votes, number of comments, and screenshot url.
>
```swift
/// A product retrieved from the Product Hunt API.
struct Post {
   // Various properties of a post that we either need or want to display
   let id: Int
   let name: String
   let tagline: String
   let votesCount: Int
   let commentsCount: Int
}
```

There's more we will need to add to this file to allow it to work effortlessly with network calls. We'll leave it like this for now and move on to create the UI that will show this data.

# Now Commit

```bash
$ git add .
$ git commit -m 'Created Post model'
$ git push
```
