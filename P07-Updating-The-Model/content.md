---

title: "Updating The Model"
slug: update-model

---

In order for this model to work well with network requests, we will make it **decodable**.

# Getting Started

First, let's add a variable for the preview image url:

> [action]
> Add previewImageUrl variable at the bottom of Post model.
>
> ```swift
>   ...
>   let previewImageUrl: URL
> }
> ```

This will hold the link to the screenshot of the product and will allow us to download the image later on.

Next, let's make `Post` **decodable** by creating an extension:

> [action]
> Create an extension for `Post` and make it inherit from the `Decodable` protocol.
>
> ```swift
> // MARK: Decodable
> extension Post: Decodable {
>
> }
> ```

## Add Coding Keys

We'll need to define **coding keys** to tell Swift exactly where to find the information to fill the model's variables. However, we actually only need this because the Product Hunt API uses a different naming convention for it's properties.

> [action]
> Create an `enum` called `PostKeys` which inherits from `String` and `CodingKey` and place it inside the `Decodable` extension.
>
> ```swift
> enum PostKeys: String, CodingKey {
>     case id
>     case name
>     case tagline
>     case votesCount = "votes_count"
>     case commentsCount = "comments_count"
>     case previewImageUrl = "screenshot_url"
> }
> ```
>
> ![Post Keys](assets/post-coding-keys.png)

Note how only `votesCount`, `commentsCount`, and `previewImageUrl` are the only variables that are set to a string. This is because in the JSON we receive from the request, these variables are named differently (using **snake_case**, rather than **camelCase** which is the recommended practice for Swift).

In fact, if we did not plan to collect these variables from the JSON, we would not need to create any **coding keys**.

Also, there are cases where you simply what to rename the property differently, such as for the `previewImageUrl`. We'll create a coding key for that as well:

> [action]
> Add a CodingKey for the preview image.
>
> ```swift
> enum PreviewImageURLKeys: String, CodingKey {
>     case imageURL = "850px"
> }
> ```
>
> ![Preview Keys](assets/preview-coding-keys.png)

# Initializing A Decodable
