---

title: "Reconstructing Networking Layer"
slug: networking-layer

---

Now that we have a working `CommentsViewController` the next step will be to build the methods necessary to pull data from the API.

Going back to the API documentation you'll find how to send a pull request to retrieve a specific post's comments.

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
> ```swift
> struct Comment: Decodable {
>   let id: Int
>   let body: String
> }
> ```

And the `struct` to represent the API response from the pull request.

> [action]
> Create `CommentApiResponse` that has a list of `Comment` objects to model the JSON we will retrieve from the API.
>
> ```swift
> struct CommentApiResponse: Decodable {
>     let comments: [Comment]
> }
> ```

Because our variables match the properties of the JSON retrieved from the API we do not need to set up coding keys or an initializer. The Comment model is ready to go 👍

# EndPoints

We'll do some major refactoring to the networking layer to improve the overall quality of the code and reduce repetitive code.

Using `enums` is a great way to do this. An enumeration defines a common type for a group of related values and gives us access to those values in a way that is type-safe.

We'll have a case for each endpoint from the API that we want to access.

> [action]
> Create enum `EndPoints` with case for `posts` and `comments` inside the `NetworkManager` class
>
> ```swift
> enum EndPoints {
>   case posts
>   case comments(postId: Int)
> }
> ```

We'll have a method for each part of the `URLRequest` necessary to building the pull request to get posts and comments.


> [action]
>  Create method to get the **getPath**
>
> ```swift
>  enum EndPoints {
>    ...
>    func getPath() -> String {
>        switch self {
>        case .posts:
>            return "posts/all"
>        case .comments(let postId):
>            return ""
>        }
>    }
> }
> ```

> [action]
> Create method to get the **http method** in a type-safe way.
>
> ```swift
> enum EndPoints {
>   ...
>   func getHTTPMethod() -> String {
>     return "GET"
>   }
> }
> ```

> [action]
> Create method to get **headers**.
>
> ```swift
> enum EndPoints {
>   ...
>   func getHeaders(token: String) -> [String: String] {
>     return [
>        "Accept": "application/json",
>       "Content-Type": "application/json",
>       "Authorization": "Bearer \(token)",
>       "Host": "api.producthunt.com"
>     ]
>   }
> }
> ```

> [action]
> Create method to get **parameters**
>
> ```swift
> enum EndPoints {
>   ...
>   func getParams() -> [String: String] {
>     switch self {
>     case .posts:
>       return [
>         "sort_by": "votes_count",
>         "order": "desc",
>         "per_page": "20",
>
>         "search[featured]": "true"
>       ]
>
>     case let .comments(postId):
>       return [
>         "sort_by": "votes",
>         "order": "asc",
>         "per_page": "20",
>
>          "search[post_id]": "\(postId)"
>        ]
>     }
>   }
> }
> ```

> [action]
> Create method to convert params array into a connected string.
>
> ```swift
> enum EndPoints {
>   ...
>   func paramsToString() -> String {
>     let parameterArray = getParams().map { key, value in
>       return "\(key)=\(value)"
>     }
>
>     return parameterArray.joined(separator: "&")
>   }
> }
> ```

Now you can use this to quickly and efficiently create a network request to the Product Hunt API.

# Use EndPoints To Construct Request

Now we can clean up the code in `NetworkManager`

> [action]
> Add private method `makeRequest` to `NetworkManager` class.
>
> ```swift
> private func makeRequest(for endPoint: EndPoints) -> URLRequest {
>    let stringParams = endPoint.paramsToString()
>    let path = endPoint.getPath()
>    let fullURL = URL(string: baseURL.appending("\(path)?\(stringParams)"))!
>
>    var request = URLRequest(url: fullURL)
>    request.httpMethod = endPoint.getHTTPMethod()
>    request.allHTTPHeaderFields = endPoint.getHeaders(token: token)
>
>    return request
> }
> ```

We'll also use an `enum` to create `Result` type that allows use to easily handle different responses from the API.

> [action]
> Create enum `Result` inside the `NetworkManager` class with `success` case for returning decoded data, and `failure` case for returning error messages from the response.
>
> ```swift
> enum Result<T> {
>   case success(T)
>   case failure(Error)
> }
> ```

And an enum to define all the errors we wish to handle in code.

> [action]
> Create enum `EndPointError`. Also, add this inside the `NetworkManager` class
>
> ```swift
> enum EndPointError: Error {
>    case couldNotParse
>    case noData
> }
> ```

# Update Get Posts Method

Now we can update the `getPosts` method to use the enums we created to build requests and handle from the API.

> [action]
> Update `getPosts` method parameters to use the `Result` type.
>
> ```swift
> func getPosts(_ completion: @escaping (Result<[Post]>) -> Void) {
> ```

> [action]
> Replace everything above `urlSession.dataTask` and with new `postsRequest` variable and use it in the `dataTask` method call.
>
> ```swift
> func getPosts(_ completion: @escaping (Result<[Post]>) -> Void) {
>     let postsRequest = makeRequest(for: .posts)
>     let task = urlSession.dataTask(with: postsRequest) { data, response, error in
>         // Check for errors.
>         if let error = error {
>             return completion(Result.failure(error))
>         }
>         
>         // Check to see if there is any data that was retrieved.
>         guard let data = data else {
>             return completion(Result.failure(EndPointError.noData))
>         }
>         
>         // Attempt to decode the data.
>         guard let result = try? JSONDecoder().decode(PostList.self, from: data) else {
>             return completion(Result.failure(EndPointError.couldNotParse))
>         }
>         
>         let posts = result.posts
>         
>         // Return the result with the completion handler.
>         DispatchQueue.main.async {
>             completion(Result.success(posts))
>         }
>     }
>     
>     task.resume()
> }
> ```

# Create Get Comments Method

Now it should be relatively easier to send a request to get a posts comments> [action]

> [action]
> Create method to get comments for a post.
>
> ```swift
> func getComments(_ postId: Int, completion: @escaping (Result<[Comment]>) -> Void) {
>    let commentsRequest = makeRequest(for: .comments(postId: postId))
>    let task = urlSession.dataTask(with: commentsRequest) { data, response, error in
>      if let error = error {
>        return completion(Result.failure(error))
>      }
>
>      guard let data = data else {
>        return completion(Result.failure(EndPointError.noData))
>      }
>
>      guard let result = try? JSONDecoder().decode(CommentApiResponse.self, from: data) else {
>        return completion(Result.failure(EndPointError.couldNotParse))
>      }
>
>      DispatchQueue.main.async {
>        completion(Result.success(result.comments))
>      }
>    }
>     
>    task.resume()
> }
> ```
