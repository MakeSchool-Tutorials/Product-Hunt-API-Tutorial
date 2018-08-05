---

title: "Reconstructing Networking Layer"
slug: networking-layer

---

> [action]
> Open Postman and send a **GET** request with this URL
> `https://api.producthunt.com/v1/comments?sory_by=votes&order=asc&per_page=20&search[post_id]=1`

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

> [action]
> Create `commentApiResponse` that has a list of `Comment` objects to model the JSON we will retrieve from the API.
>
> ```swift
> struct CommentApiResponse: Decodable {
>     let comments: [Comment]
> }
> ```

# EndPoints

> [action]
> Create enum `EndPoints` with case for `posts` and `comments`
>
> ```swift
> enum EndPoints {
>   case posts
>   case comments(postId: Int)
> }
> ```

> [action]
> Create method to get **http method**.
>
> ```swift
> func getHTTPMethod() -> String {
>   return "GET"
> }
> ```

> [action]
> Create method to get **headers**.
>
> ```swift
> func getHeaders(token: String) -> [String: String] {
>   return [
>      "Accept": "application/json",
>      "Content-Type": "application/json",
>      "Authorization": "Bearer \(token)",
>      "Host": "api.producthunt.com"
>   ]
> }
> ```

> [action]
> Create method to get **parameters**
>
> ```swift
> func getParams(id: Int? = nil) -> String {
>   switch self {
>   case .posts:
>      return [
>        "sort_by": "votes_count",
>        "order": "desc",
>        "per_page": "20",
>
>        "search[featured]": "true"
>      ]
>
>   case let .comments(postId):
>      return [
>        "sort_by": "votes",
>        "order": "asc",
>        "per_page": "20",
>
>        "search[post_id]": "\(postId)"
>      ]
>   }
> }
> ```

> [action]
> Create method to convert params into a string
>
> ```swift
> func paramsToString() -> String {
>   let parameterArray = getParams().map { key, value in
>     return "\(key)=\(value)"
>   }
>
>   return parameterArray.joined(separator: "&")
> }
> ```

Now you can use this to quickly and efficiently create a network request to the Product Hunt API.

# Use EndPoints To Construct Request

> [action]
> Open `ProductHuntNetworkRequest` and add private method `makeRequest`
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

> [action]
> Create enum `Result`
>
> ```swift
> enum Result<T> {
>   case success(T)
>   case failure(Error)
> }
> ```

> [action]
> Create enum `EndPointError`
>
> ```swift
> enum EndPointError: Error {
>    case couldNotParse
>    case noData
>    case noPosts
>    case noComments
> }
> ```

# Update Get Posts Method

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
> let postsRequest = makeRequest(for: .posts)
> urlSession.dataTask(with: postsRequest) { data, response, error in
> ```

# Create Get Comments Method

> [action]
>
> ```swift
> func getComments(_ postId: Int, completion: @escaping (Result<[Comment]>) -> Void) {
>    let commentsRequest = makeRequest(for: .comments(postId: postId))
>    urlSession.dataTask(with: commentsRequest) { data, response, error in
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
>      completion(Result.success(result.comments))
>    }.resume()
> }
> ```
