---
title: "Creating the Post Cell Class"
slug: create-post-cell-class
---

In order to access the custom cell's views in code, a custom class is needed.

We'll call this custom class `PostTableViewCell` and place all the methods related to keeping the cell up-to-date in here.

Start by creating the file.

> [action]
> Create a new file using the `Cocoa Touch Class` template.
>
> Name the Class `PostTableViewCell` and make it a Subclass of `UITableViewCell` from the dropdown menu.
>
> ![Create post cell](assets/01_creating-the-post_view-cell.png)

In the new file we just created, we won't be needing the `awakeFromNib()` and `setSelected(:, animated:)` methods. So, you can delete those (or keep them, there's no harm done).

Next we'll set the Prototype Cell we created in Storyboard to use this class.

> [action]
> Go to Storyboard and select the cell we created in the previous chapter (be sure to select the `UITableViewCell` and not the `contentView` or the `Container View` we created).
>
> In the `Identity Inspector`, change the `Class` field to `PostTableViewCell`.

Now we can connect the `IBOutlets` to the class we created.

> [action]
> Create weak `IBOutlets` for the labels and image view inside `PostTableViewCell.swift`
> Name them `nameLabel`, `taglineLabel`, `commentsCountLabel`, `votesCountLabel`, and `previewImageView`

To make updating these views quick and easy, we'll use a **property observer** on a `Post` object.

> [info]
> Property observers watch and respond to changes in a property's value and are called every time a new value is set.

<!-- -->

> [action]
> Below your `IBOutlets` create a new optional `Post` var called `post`
>
> Now add the property observer `didSet`
>
```swift
...
>
var post: Post? {
   didSet {
>
   }
}
```

Any code we place inside `didSet {}` will execute every time the `post` variable is set. This is a good place to update the labels and image view.

> [action]
> Add the following lines to update the views inside the property observer:
>
```swift
// Runs every time the post variable is set
didSet {
   // make sure we return if post doesn't exist
   guard let post = post else { return }
   // Assign our UI elements to their post counterparts
   nameLabel.text = post.name
   taglineLabel.text = post.tagline
   commentsCountLabel.text = "Comments: \(post.commentsCount)"
   votesCountLabel.text = "Votes: \(post.votesCount)"
   // We'll write this next!
   updatePreviewImage()
}
```

# Placeholder Images

Next create the `updatePreviewImage()` method. For now it will be very simple and give every post a placeholder image.

> [action]
>
```swift
...
>
func updatePreviewImage() {
   // make sure we return if post doesn't exist
   guard let post = post else { return }
   // assign the placeholder image to the UI element
   previewImageView.image = UIImage(named: "placeholder")
}
```

Now the custom cell is ready to be used by a UITableView. Make sure your variable names match up with your Storyboard!

> [solution]
>
```swift
class PostTableViewCell: UITableViewCell {
  // All of our UI elements we'll use for our view that relate to properties of a post
  @IBOutlet weak var nameLabel: UILabel!
  @IBOutlet weak var taglineLabel: UILabel!
  @IBOutlet weak var commentsCountLabel: UILabel!
  @IBOutlet weak var votesCountLabel: UILabel!
  @IBOutlet weak var previewImageView: UIImageView!
>    
  var post: Post? {
    // Runs every time the post variable is set
    didSet {
       // make sure we return if post doesn't exist
       guard let post = post else { return }
       // Assign our UI elements to their post counterparts
       nameLabel.text = post.name
       taglineLabel.text = post.tagline
       commentsCountLabel.text = "Comments: \(post.commentsCount)"
       votesCountLabel.text = "Votes: \(post.votesCount)"
       // We'll write this next!
       updatePreviewImage()
    }
  }
>
  func updatePreviewImage() {
     // make sure we return if post doesn't exist
     guard let post = post else { return }
     // assign the placeholder image to the UI element
     previewImageView.image = UIImage(named: "placeholder")
  }
}
```

# Now Commit

```bash
$ git add .
$ git commit -m 'Created custom class for Posts'
$ git push
```
