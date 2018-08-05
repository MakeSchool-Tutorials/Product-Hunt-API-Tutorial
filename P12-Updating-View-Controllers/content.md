---

title: "Updating View Controllers"
slug: update-view-controllers

---

Now we have everything we need to have the full app working and ready to show to the client.

First let's update the `FeedViewController` to use the refactored networking methods.

# Update FeedViewController

> [action]
> Change `updateFeed` method to use new `getPosts` method.
>
> ```swift
> func updateFeed() {
>     networkManager.getPosts() { result in
>         switch result {
>         case let .success(posts):
>           self.posts = posts
>         case let .failure(error):
>           dump(error)
>         }
>     }
> }
> ```

Instead of setting the `comments` list to mock data when pushing a `CommentsViewController` onto the `navigationController`, we'll send in the **id** of the post that was tapped.

> [action]
> Update tapping posts
>
> ```swift
>     ...
>     guard let commentsView = storyboard.instantiateViewController(withIdentifier: "commentsView") as? CommentsViewController else {
>       return
>     }
>     commentsView.postID = post.id
>     navigationController?.pushViewController(commentsView, animated: true)
> }
> ```

# Update CommentsViewController

> [action]
> Add `Int` var below `comments` and add a `NetworkManager`
>
> ```swift
>   ...
> }
>
> var postID: Int!
>
> var networkManager: NetworkManager!
> ```

> [action]
> Initialize `networkManager` in `viewDidLoad`
>
> ```swift
> ...
>
> networkManager = NetworkManager()
> ```

> [action]
> Add method to pull comments from `networkManager` called `updateComments`
>
> ```swift
> func updateComments() {
>     networkManager.getComments(postID) { result in
>         switch result {
>         case let .success(comments):
>           self.comments = comments
>         case let .failure(error):
>           dump(error)
>         }
>     }
> }
> ```

> [action]
> Update tableView `cellForRowAt` method.
>
> ```swift
>     ...
>
>     let comment = comments[indexPath.row]
>     cell.commentTextView.text = comment.body
>     return cell
> }
> ```

Done! Now you have a completed product ready to show the client
