---
title: "Creating the Custom Cell"
slug: creating-the-custom-cell
---

The default cell only has room for a string of text. We need it to display more than that.

We will need 4 labels, for the name, tagline, number of votes, and comments, which will be grouped together in a stack view. We'll also add an image view to display the screenshot of each product.

1. >[action]Open storyboard and open the table view's Attribute Inspector to give it a Dynamic Prototype Cell.
    > ![Prototype Cell](assets/dynamic-prototype-cell.png)
2. We'll use the view that comes with the cell, the `Content View`, as cell's **background**; and a new UIView as the **container** for all the views that will display information.
    >[action]
    >Drag a UIView into the prototype cell and Pin it on all 4 sides of the `Content View`.
3. >[action]
    >Add a UILabel pinned to the top and left of the `Container View`.
4. Add imageView. You can get one from https://placeholder.com/
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

# Use Mock Data to Test Feed View

1. Create list of Posts with mock data.
2. Set tableview to use mock data count.
3. Cast dequed cell as PostTableViewCell.
4. Use didSet to set to easily update labels.
