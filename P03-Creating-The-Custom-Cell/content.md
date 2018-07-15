---
title: "Creating the Custom Cell"
slug: creating-the-custom-cell
---

The default cell only has room for a string of text. We need it to display more than that.

We will need 4 labels, for the name, tagline, number of votes, and comments, which will be grouped together in a stack view. We'll also add an image view to display the screenshot of each product.

1. >[action]
    >Open storyboard and open the table view's `Attribute Inspector` to give it a Dynamic Prototype Cell.
    >![Prototype Cell](assets/dynamic-prototype-cell.png)
2. >[action]
    >Increase the size of the cell to 250 in the **Size Inspector**
    >![Increase Cell Size](assets/cell-size.png)
3. We'll use the view that comes with the cell, the `Content View`, as cell's **background**; and a new UIView as the **container** for all the views that will display information.
    >[action]
    >Drag a UIView into the prototype cell and Pin it on all 4 sides of the `Content View`.
4. >[action]
    >Add a UILabel pinned to the top and left of the `Container View`. This will be the **name** label.
5. >[action]
    >Add an UIImageView to the center of the cell and pin it to all 4 sides of the `Container View` with these constraints:
    >![Pin Image View](assets/pin-image.png)

    You can set the image to whatever you want. Free placeholder images [here](https://placeholder.com/)
6. >[action]
    >Add a label pinned to the left, bottom, and right of the `Container View` for the **tagline**.

    >[action]
    >Set the font size of this label to 13.
7. >[action]
    >Pin a horizontal UIStackView to the top right with 2 labels inside of font size 13. These will be for the number of **comments** and **votes**.
8. To be able to use this custom cell in code an **identifier** is needed.
    >[action]
    >Set `Identifier` of the cel to  `postCell` in the `Identity Inspector`.

>[solution]
>![Completed custom cell](assets/custom-cell.png)

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
