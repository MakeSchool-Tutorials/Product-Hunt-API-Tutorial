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
>     "Authorization": "Bearer replace-me-with-a-token-🙏",
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
>    urlSession.dataTask(with: request) { data, response, error in
>   
>    }
> }
> ```

The `dataTask` method executes the request provided and the completion handler returns the result as `data`, a `response` and an `error` if there is any reasons for an incomplete request.

We'll check to see if there is an error first and then if there is any data to **decode**.

> [action]
> Add the following inside the `dataTask(with:)` completion handler
>
> ```swift
> if let error = error {
>     dump(error)
> }
>
> guard let data = data else {
>     return
> }
> ```

These lines make sure that we have data to work with.

Once we get past those checkpoints, we can decode the data.

> [action]
> Use `JSONDecoder` to decode the data retrieved into a `PostList`
>
> ```swift
> ...
>
> guard let result = try? JSONDecoder().decode(PostList.self, from: data) else {
>     return
> }
> ```

> [info]
> `JSONDecoder().decode` automatically decodes any `Decodable`.
>

If the `Post` is modeled correctly, the code should continue on to the next step; returning the result as an array of posts.

> [action]
> Add the following to the bottom of the `dataTask` completion Handler
>
> ```swift
>     ...
>
>     guard let posts = result.posts else {
>         return
>     }
>
>     completion(posts)
> }
> ```

One last thing we need to add is a method call that allows the dataTask to run continuously.

> [action]
> Append this to the last bracket of the `dataTask` method call.
>
> ```swift
>      ...
>
> }.resume()
> ```

That's the networking layer completed 👌

> [solution]
>
> ```swift
> class NetworkManager {
>   let urlSession = URLSession.shared
>   var baseURL = "https://api.producthunt.com/v1/"
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
>       "Authorization": "Bearer eb715e9a4c4dd4f7randomt0ken61e49aaa0randomt0kenc0cac1c6",
>       "Host": "api.producthunt.com"
>     ]
>
>     urlSession.dataTask(with: request) { data, response, error in
>       // Check for errors.
>       if let error = error {
>         dump(error)
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
>       // Check to see if there is are any posts that were decoded successfully.
>       guard let posts = result.posts else {
>         return
>       }
>
>       // Return the result with the completion handler.
>       completion(posts)
>     }.resume()
>   }
> }
> ```

We can now use it in the `FeedViewController`.