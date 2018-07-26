---

title: "Creating the Custom Cell"
slug: creating-the-custom-cell

---

With our custom cell, we now have all the UI elements we need to see the `Feed View` in action. However, we won't be making any network requests just yet; instead we'll use **mock data** to emulate the data that we will receive from an API request.

> [action]
> Open `FeedViewController.swift` and go to the `UITableViewDataSource` extension to add the follow lines in
1. Set number of cells to 3 for testing purposes.
2. Guard let to dequeue post cells.
3. Run to see how it looks!

# Create Post Tableview Cell

1. Set custom class to PostTableViewCell.swift for custom cell in storyboard.
2. Connect IBOutlets.
3. Run!

# Use Mock Data to Test Feed View

Using Mock Data allows us to easily test our app without having to create an entire networking layer. We'll simply create an array with `Post` objects and provide the data for each post ourselves.

>[action]
>Create an array of Posts with mock data below your `feedTableView` IBOutlet.
>
>```swift
>...
>
>var mockData: [Post] = {
>    var meTube = Post(id: 0, name: "Me Tube", tagline: "Stream videos for free!", votesCount: 25, commentsCount: 4)
>    var boogle = Post(id: 1, name: "Boogle", tagline: "Search anything!", votesCount: 1000, commentsCount: 50)
>    var mTunes = Post(id: 2, name: "mTunes", tagline: "Listen to any song!", votesCount: 25000, commentsCount: 590)
>    return [meTube, boogle, mTunes]
>}()
>
>...
>```
>

We can now use this array as a datasource for the table view, allowing us to provide data for the labels in the custom cell we created.

>[action]
>Update the following methods in the `UITableViewDataSource` extension:
>
>```swift
>func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
>   return posts.count
>}
>
>func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
>   guard let cell = tableView.dequeueReusableCell(withIdentifier: "postCell") as? PostTableViewCell else {
>     return UITableViewCell()
>   }
>   let post = posts[indexPath.row]
>   cell.post = post
>   return cell
>}
>```
>

4. Use didSet to set to easily update labels.
