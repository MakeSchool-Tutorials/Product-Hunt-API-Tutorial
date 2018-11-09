---

title: "Creating The Post Model"
slug: post-model

---

Let's model the information we will receive for each posting. The properties we need are the product's ID, name, tagline, number of votes, and number of comments.

> [action]
> Create a new Swift file and call it `Post.swift`.

We'll use a `struct` to define the variables we want to get from the Product Hunt API.

> [action]
> Create a Struct called `Post`.

> ```swift
> /// A product retrieved from the Product Hunt API.
> struct Post {
>
> }
> ```

> [action]
> Give `Post` variables to hold a product's id, name, tagline, number of votes, number of comments, and screenshot url.
>
> ```swift
> struct Post {
>     let id: Int
>     let name: String
>     let tagline: String
>     let votesCount: Int
>     let commentsCount: Int
> }
>```

There's more we will need to add to this file to allow it to work effortlessly with network calls. We'll leave it like this for now and move on to create the UI that will show this data.
