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
>           print(error)
>         }
>     }
> }
> ```

Instead of setting the `comments` list to mock data when pushing a `CommentsViewController` onto the `navigationController`, we'll send in the **id** of the post that was tapped.

> [action]
> Update the `tableView(tableView: didSelectRowAt:)` in our `FeedViewController.swift`
>
> ```swift
> func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
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
> Add `Int` var below `comments` and add a `NetworkManager`. Be sure to update the comment var to an array of Comments and not an array of Strings
>
> ```swift
> class CommentsViewController: UIViewController {
>   ...
>
>   var comments: [Comment] = []
>
>   var postID: Int!
>
>   var networkManager = NetworkManager()
>
> }
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
>           print(error)
>         }
>     }
> }
> ```

> [action]
> Add the `updateComments()` method to the `CommentsViewController`'s `viewDidLoad()`
>
> ```swift
> override func viewDidLoad() {
>     
>     updateComments()
> }
> ```

> [action]
> Update tableView `cellForRowAt` method.
>
> ```swift
>  func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
>     ...
>
>     let comment = comments[indexPath.row]
>     cell.commentTextView.text = comment.body
>     return cell
>  }
> ```

Done! Now you have a completed product ready to show the client
