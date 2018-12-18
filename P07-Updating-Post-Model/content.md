---
title: "Updating Post Model"
slug: update-post-model
---

In order for this model to work well with network requests, we will make it **decodable**.

# Getting Started

First, let's add a variable for the preview image url:

> [action]
> Add previewImageUrl variable at the bottom of `Post` model.
>
```swift
 ...
 let previewImageURL: URL
}
```

This will hold the link to the screenshot of the product and will allow us to download the image later on.

Next, let's make `Post` **decodable** by conforming to the `Decodable` protocol.

> [action]
>
```swift
// MARK: Decodable
struct Post: Decodable {
 ...
}
```

## Add Coding Keys

We'll need to define **coding keys** to tell Swift exactly where to find the information to fill the model's variables. However, we actually only need this because the Product Hunt API uses a different naming convention for it's properties.

> [action]
> Create an `enum` called `PostKeys` with the raw type as `String` and conforms to `CodingKey` and place it inside the `Post` struct.
>
```swift
enum PostKeys: String, CodingKey {
   case id
   case name
   case tagline
   case votesCount = "votes_count"
   case commentsCount = "comments_count"
   case previewImageURL = "screenshot_url"
}
```
>
> ![Post Keys](assets/post-coding-keys.png)

Note how only `votesCount`, `commentsCount`, and `previewImageUrl` are the only variables that are set to a string. This is because in the JSON we receive from the request, these variables are named differently (using **snake_case**, rather than **camelCase** which is the recommended practice for Swift).

In fact, if we did not plan to collect these variables from the JSON, we would not need to create any **coding keys**.

Also, there are cases where you simply what to rename the property differently, such as for the `previewImageUrl`. We'll create a coding key for that as well:

> [action]
> Add a CodingKey for the preview image.
>
```swift
enum PreviewImageURLKeys: String, CodingKey {
   case imageURL = "850px"
}
```
>
> ![Preview Keys](assets/preview-coding-keys.png)

# Initializing A Decodable

Now that we have all our necessary coding keys, the next step will be to setup the initializer for our model.

> [action]
> Add this initializer inside the `Post` struct:
>
```swift
init(from decoder: Decoder) throws {
>
}
```

We'll first need to _enter_ the post object in order to access its properties. This is done using **containers** which uses `CodingKeys`:

> [action]
> Add this to the initializer:
>
```swift
let postsContainer = try decoder.container(keyedBy: PostKeys.self)
```

Now we can grab all the information we need.

> [action]
> Set the variables of the `Post` using the container.
>
```swift
  ...
  id = try postsContainer.decode(Int.self, forKey: .id)
  name = try postsContainer.decode(String.self, forKey: .name)
  tagline = try postsContainer.decode(String.self, forKey: .tagline)
  votesCount = try postsContainer.decode(Int.self, forKey: .votesCount)
  commentsCount = try postsContainer.decode(Int.self, forKey: .commentsCount)
}
```
>
> ![Posts container](assets/post-container.png)

The screenshot URL is inside an object, so we'll need to access it through a **nested container** using the `PreviewImageURLKeys`:

> [action]
> Add this add the bottom of the initializer:
>
```swift
   ...
>
   let screenshotURLContainer = try postsContainer.nestedContainer(keyedBy: PreviewImageURLKeys.self, forKey: .previewImageURL)
   previewImageURL = try screenshotURLContainer.decode(URL.self, forKey: .imageURL)
}
```
>
> ![Screenshot container](assets/screenshot-container.png)

Now the model is ready to go! But there's one more thing we need to add to make things easier.

The products we retrieve from the API are inside the array called "posts". We can model this using a Struct:

> [action]
> Add this below your `Post` Struct:
>
```swift
struct PostList: Decodable {
   var posts: [Post]
}
```

Next up will be creating the networking layer.
