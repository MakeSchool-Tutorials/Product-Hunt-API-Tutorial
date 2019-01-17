---
title: "Reconstructing Networking Layer"
slug: networking-layer
---

Now that we have a working `CommentsViewController` the next step will be to build the methods necessary to pull data from the API.

Going back to the [API documentation](https://api.producthunt.com/v1/docs), you'll find how to send a pull request to retrieve a specific post's comments.

> [action]
> Open Postman and send a **GET** request with this URL
> `https://api.producthunt.com/v1/comments?sory_by=votes&order=asc&per_page=20&search[post_id]=1`
>
> **Note:** be sure to use the same request sent with the correct headers such as authorization or this request will not work

# Create Comment Model

Let's start by creating the model that will hold the **id** and **body** of a comment retrieved from the API.

> [action]
> Create a `Decodable` model called `Comment` with an `Int id` and `String body`.
>
```swift
struct Comment: Decodable {
 let id: Int
 let body: String
}
```

And the `struct` to represent the API response from the pull request.

> [action]
> Create the `CommentApiResponse` struct within your `Comment` model that has a list of `Comment` objects to model the JSON we will retrieve from the API. This will need to be `Decodable` as well.
>
```swift
struct CommentApiResponse: Decodable {
   let comments: [Comment]
}
```

Because our variables match the properties of the JSON retrieved from the API we do not need to set up coding keys or an initializer. The Comment model is ready to go 👍

# EndPoints

We'll do some major refactoring to the networking layer to improve the overall quality of the code and reduce repetitive code.

Using `enums` is a great way to do this. An **enumeration** defines a common type for a group of related values and gives us access to those values in a way that is type-safe.

>[info]
> Something is considered **type-safe** if an operation checks that it can be performed on a specific data type before it attempts to do so. For example, preventing a function from calling the `append` method on an `Int` before it actually executes the call.

We'll have a case for each endpoint from the API that we want to access.

> [action]
> Create enum `EndPoints` with cases for `posts` and `comments` inside the `NetworkManager` class
>
```swift
class NetworkManager {
...
  enum EndPoints {
   case posts
   case comments(postId: Int)
  }
}
```

We'll have a method for each part of the `URLRequest` necessary to build the pull request to get posts and comments.

> [action]
>  Create a method **get the path** of the posts and comments.
>
```swift
enum EndPoints {
  ...
  func getPath() -> String {
      switch self {
      case .posts:
          return "posts/all"
      case .comments:
          return "comments"
      }
  }
}
```
>
> Create a method to get the **http method** in a type-safe way.
>
```swift
enum EndPoints {
 ...
 func getHTTPMethod() -> String {
   return "GET"
 }
}
```
>
> Create method to get **headers**.
>
```swift
enum EndPoints {
 ...
 func getHeaders(token: String) -> [String: String] {
   return [
      "Accept": "application/json",
     "Content-Type": "application/json",
     "Authorization": "Bearer \(token)",
     "Host": "api.producthunt.com"
   ]
 }
}
```
>
> Create method to get **parameters**
>
```swift
enum EndPoints {
 ...
 func getParams() -> [String: String] {
   switch self {
   case .posts:
     return [
       "sort_by": "votes_count",
       "order": "desc",
       "per_page": "20",
>
       "search[featured]": "true"
     ]
>
   case let .comments(postId):
     return [
       "sort_by": "votes",
       "order": "asc",
       "per_page": "20",
>
        "search[post_id]": "\(postId)"
      ]
   }
 }
}
```
>
> Create method to convert the **params** array into a connected **string**.
>
```swift
enum EndPoints {
 ...
 func paramsToString() -> String {
   let parameterArray = getParams().map { key, value in
     return "\(key)=\(value)"
   }
>
   return parameterArray.joined(separator: "&")
 }
}
```

Now you can use this to quickly and efficiently create a network request to the Product Hunt API.

# Use EndPoints To Construct Request

Now we can clean up the code in `NetworkManager`

> [action]
> Add a private method `makeRequest` to the `NetworkManager` class.
>
```swift
private func makeRequest(for endPoint: EndPoints) -> URLRequest {
  let stringParams = endPoint.paramsToString()
  let path = endPoint.getPath()
  let fullURL = URL(string: baseURL.appending("\(path)?\(stringParams)"))!
  var request = URLRequest(url: fullURL)
  request.httpMethod = endPoint.getHTTPMethod()
  request.allHTTPHeaderFields = endPoint.getHeaders(token: token)
>
  return request
}
```

We'll also use an `enum` to create a `Result` type that allows us to easily handle different responses from the API.

> [action]
> Create an enum `Result` inside the `NetworkManager` class with `success` case for returning decoded data, and `failure` case for returning error messages from the response.
>
```swift
enum Result<T> {
 case success(T)
 case failure(Error)
}
```

And an `enum` to define all the errors we wish to handle in code.

> [action]
> Create an enum `EndPointError` and add this inside the `NetworkManager` class
>
```swift
enum EndPointError: Error {
  case couldNotParse
  case noData
}
```

# Update Get Posts Method

Now we can update the `getPosts` method to use the enums we created to build requests and handle responses from the API.

> [action]
> Update the `getPosts` method parameters to use the `Result` type.
>
```swift
func getPosts(_ completion: @escaping (Result<[Post]>) -> Void) {
```
>
> Replace the body of the `getPosts` method to use the enums we created and reduce the redundancy that it currently has:
>
```swift
func getPosts(_ completion: @escaping (Result<[Post]>) -> Void) {
   let postsRequest = makeRequest(for: .posts)
   let task = urlSession.dataTask(with: postsRequest) { data, response, error in
       // Check for errors.
       if let error = error {
           return completion(Result.failure(error))
       }
>         
       // Check to see if there is any data that was retrieved.
       guard let data = data else {
           return completion(Result.failure(EndPointError.noData))
       }
>         
       // Attempt to decode the data.
       guard let result = try? JSONDecoder().decode(PostList.self, from: data) else {
           return completion(Result.failure(EndPointError.couldNotParse))
       }
>         
       let posts = result.posts
>         
       // Return the result with the completion handler.
       DispatchQueue.main.async {
           completion(Result.success(posts))
       }
   }
>     
   task.resume()
}
```

# Create Get Comments Method

Now it should be relatively easy to send a request to get a post's comments> [action]

> [action]
> Create method to get comments for a post:
>
```swift
func getComments(_ postId: Int, completion: @escaping (Result<[Comment]>) -> Void) {
  let commentsRequest = makeRequest(for: .comments(postId: postId))
  let task = urlSession.dataTask(with: commentsRequest) { data, response, error in
    if let error = error {
      return completion(Result.failure(error))
    }
>
    guard let data = data else {
      return completion(Result.failure(EndPointError.noData))
    }
>
    guard let result = try? JSONDecoder().decode(CommentApiResponse.self, from: data) else {
      return completion(Result.failure(EndPointError.couldNotParse))
    }
>
    DispatchQueue.main.async {
      completion(Result.success(result.comments))
    }
  }
>     
  task.resume()
}
```

Well done! We got more practice here in **decoding JSON into Swift models, building out our network layer, and working with an API!**

Unfortunately we have a problem: If we try to build right now, we'll get errors since we still need to update our view controllers to utilize what we've written in `NetworkManager`. Once we do that though, we'll have our very own Product Hunt Reader!

# Now Commit

```bash
$ git add .
$ git commit -m 'Integrate comments API'
$ git push
```
