---
title: "Building the Main Screen"
slug: buidling-the-main-screen
---

We'll start by just building the UI of the app. Let's begin with the product feed screen.

# The Feed

1.	Embed navigation controller.
2.	Title “Feed.”
3.	Add table view.
a.	Easy constraints, just pin everything to all sides
4.	Rename to “FeedViewController”
5.	Connect feedTableView to FeedViewController
a.	Set delegate and data source to FeedViewController
6.	Add required stubs for data source

# The Model

1. Create a new swift file called `Post.swift`.
2. Create a struct called `Post`.
3. Give it an `id: Int`, `name: String`, `tagline: String`, `votesCount: Int`, and `commentsCount: Int`.

This is based on the data we saw in the GET request using Postman.

# Create An Application

1. Click API dashboard at top right.
2. Click Add application.
3. Give your application a name and this redirect URI: `https://localhost:3000/users/auth/producthunt/callback`
4. Click create token

# Create a Custom Cell

1. Add one prototype cell
2. Get placeholder image
3. Add a container view and pin to sides
4. Add imageView with placeholder image
5. Add name label
6. Add tagline label with reduced font size
7. Add horizontal stack view with comments and votes label with reduce font size and pin to top and right
8. Set identifier to postCell.

# Configure Tableview to Use Custom Cell

1. Set number of cells to 3
2. Guard let to dequeue post cells.
3. Run to see how it looks!

# Create a Class for Custom Cell

# Use Fake Data to Test Feed View
