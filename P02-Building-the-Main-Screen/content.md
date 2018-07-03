---
title: "Building the Main Screen"
slug: buidling-the-main-screen
---

We'll start by just building the UI of the app. Let's begin with the product feed screen.

# Create Feed View

1. Embed navigation controller.
2. Title “Feed.”
3. Add table view.
    - Easy constraints, just pin everything to all sides
4. Rename to “FeedViewController”
5. Connect feedTableView to FeedViewController
    - Set delegate and data source to FeedViewController
6. Add required stubs for data source

# Create Post Model

1. Create a new swift file called `Post.swift`.
2. Create a struct called `Post`.
3. Give it an `id: Int`, `name: String`, `tagline: String`, `votesCount: Int`, and `commentsCount: Int`.

# Create Custom Cell

1. Add one prototype cell
2. Get placeholder image
3. Add a container view and pin to sides
4. Add imageView with placeholder image
5. Add name label
6. Add tagline label with reduced font size
7. Add horizontal stack view with comments and votes label with reduce font size and pin to top and right
8. Set identifier to postCell.

# Configure Tableview to Use Custom Cell

1. Set number of cells to 3 for testing purposes.
2. Guard let to dequeue post cells.
3. Run to see how it looks!

# Create Post Tableview Cell

1. Set custom class to PostTableViewCell.swift for custom cell in storyboard.
2. Connect IBOutlets.
3. Run!

# Use Fake Data to Test Feed View

1. Create list of Posts with fake data.
2. Set tableview to use fake data count.
3. Cast dequed cell as PostTableViewCell.
4. Use didSet to set to easily update labels.
