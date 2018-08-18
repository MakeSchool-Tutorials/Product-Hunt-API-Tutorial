---

title: "Creating The Networking Layer"
slug: create-networking-layer

---

With our model prepared, the networking layer will be pretty simple to create.

# Create Manager Class

> [action]
Start by creating a new file named `NetworkManager.swift`

We'll keep everything in a class called `NetworkManager`

> [action]
> Create `NetworkManager` class
>
> ```swift
> class NetworkManager {
>
> }
> ```
>
> And add the following variables
>
> ```swift
> let urlSession = URLSession.shared
> var baseURL = "https://api.producthunt.com/v1/"
> var token = "replace-me-with-your-token-from-product-hunt-api-dashboard-🙏"
> ```

This is what we'll use to create the network request.

# Get Posts Method

Next we'll create the method that handles the request.

> [action]
> Create `getPosts()` method
>
> ```swift
> func getPosts() {
>
> }
> ```

Because network request require continues data flow which can result in different speeds depending on the network, we'll use an **escaping closure** as a completion handler in order to return data.

Using the normal function `return` would result in inconsistent results, as the data retrieved from a request might not always be prepared for before the method returns

> [action]
> Add an **escaping closure** to `getPosts()` as a parameter.
>
> ```swift
> func getPosts(completion: @escaping ([Post]) -> Void)
> ```

The escaping closure allows the compiler to continue on to other code—**escaping** the method—and return later on when.

## Constructing The Request

> [action]
> Add the following lines in `getPosts(completion:)`
>
> ```swift
> let query = "posts/all?sort_by=votes_count&order=desc&search[featured]=true&per_page=20"
> let fullURL = URL(string: baseURL + query)!
> var request = URLRequest(url: fullURL)
> ```

This using the baseURL of the API and the parameters we established earlier to construct a request instance.

We'll configure the request before we send it off.

> [action]
> Add these lines to `getPosts(completion:)`
>
> ```swift
> ...
>
> request.httpMethod = "GET"
> request.allHTTPHeaderFields = [
>     "Accept": "application/json",
>     "Content-Type": "application/json",
>     "Authorization": "Bearer \(token)",
>     "Host": "api.producthunt.com"
> ]
> ```
>

To send the request we'll use the `dataTask` method on our `urlSession`

> [action]
> Add the following to the bottom of `getPosts`
>
> ```swift
>    ...
>
>    let task = urlSession.dataTask(with: request) { data, response, error in
>   
>    }
> }
> ```

The `dataTask` method executes the request provided and the completion handler returns the result as `data`, a `response` and an `error` if there is any reasons for an incomplete request. This method returns us a `URLSessionDataTask` object which we can invoke `cancel()`, `suspend()` or `resume()`. We can also check its progress.

We'll check to see if there is an error first and then if there is any data to **decode**.

> [action]
> Add the following inside the `dataTask(with:)` completion handler
>
> ```swift
> let task = urlSession.dataTask(with: request) { data, response, error in
>     if let error = error {
>         print(error.localizedDescription)
>         return
>     }
>
>     guard let data = data else {
>         return
>     }
> }
> ```

These lines make sure that we have data to work with.

Once we get past those checkpoints, we can decode the data.

> [action]
> Use `JSONDecoder` to decode the data retrieved into a `PostList`
>
> ```swift
> let task = urlSession.dataTask(with: request) { data, response, error in
>     ...
>
>     guard let result = try? JSONDecoder().decode(PostList.self, from: data) else {
>         return
>     }
> }
> ```

> [info]
> `JSONDecoder().decode` automatically decodes any `Decodable`.
>

If the `Post` is modeled correctly, `JSONDecoder().decode(...)` will continue on to the next step; returning the result as an array of posts.

> [action]
> Add the following to the bottom of the `dataTask` completion Handler
>
> ```swift
> let task = urlSession.dataTask(with: request) { data, response, error in
>     ...
>
>     let posts = result.posts
>
>     DispatchQueue.main.async {
>         completion(posts)
>     }
> }
> ```

Since our `getPosts(...)` method performs a network call on a separate queue, a background queue, we need to execute our completion handler on the main queue since all UI code must execute on the main queue.

One last thing we need to do is resume the data task. By default, task tasks are paused. We'll have to resume it to start the task.

> [action]
> ```swift
> let task = urlSession.dataTask(with: request) { data, response, error in
>      ...
>
> }
>
> task.resume()
> ```

That's the networking layer completed 👌

> [solution]
>
> ```swift
> class NetworkManager {
>   let urlSession = URLSession.shared
>   var baseURL = "https://api.producthunt.com/v1/"
>   var token = "replace-me-with-your-token-from-product-hunt-api-dashboard-🙏"
>
>   func getPosts(completion: @escaping ([Post]) -> Void) {
>     // Construct the URL to get posts from API.
>     let query = "posts/all?sort_by=votes_count&order=desc&search[featured]=true&per_page=20"
>     let fullURL = URL(string: baseURL + query)!
>     var request = URLRequest(url: fullURL)
>
>     request.httpMethod = "GET"
>     // Set up header with API Token.
>     request.allHTTPHeaderFields = [
>       "Accept": "application/json",
>       "Content-Type": "application/json",
>       "Authorization": "Bearer \(token)",
>       "Host": "api.producthunt.com"
>     ]
>
>     let task = urlSession.dataTask(with: request) { data, response, error in
>       // Check for errors.
>       if let error = error {
>         print(error)
>         return
>       }
>
>       // Check to see if there is any data that was retrieved.
>       guard let data = data else {
>         return
>       }
>
>       // Attempt to decode the data.
>       guard let result = try? JSONDecoder().decode(PostList.self, from: data) else {
>         return
>       }
>
>       let posts = result.posts
>
>       // Return the result with the completion handler.
>       DispatchQueue.main.async {
>           completion(posts)
>       }
>     }
>
>     task.resume()
>   }
> }
> ```

We can now use our `getPosts(...)` method in the `FeedViewController`.
