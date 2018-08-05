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
