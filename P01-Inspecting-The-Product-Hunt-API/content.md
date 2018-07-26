---

title: "Inspecting The Product Hunt API"
slug: product-hunt-api

---

Product Hunt is an amazing platform to discover new apps and projects. We'll be making it much easier to discover the best ones that have been released recently.

# Checkout Product Hunt

You will need to sign up for Product Hunt, register an application, and generate the token needed to use the API in our app.

1. Explore the Product Hunt [website](https://www.producthunt.com/) for cool projects and ideas.
2. Create a Product Hunt account, which will give you access to API features.
3. Read the [API docs](https://api.producthunt.com/v1/docs/); this gives you all the information you need to use the API.

# Create An Application

In order to use the API, we'll have to register our application in the API Dashboard

1. From the API Docs page, click the link at top right labeled "API Dashboard" to navigate to the API Dashboard.
2. Click on *ADD AN APPLICATION*.
3. Give your application a name and the following redirect URI:
  `https://localhost:3000/users/auth/producthunt/callback`
4. Click on *CREATE TOKEN* to generate the access token you'll use in your application.

Your dashboard should now look similar to this:
![API dashboard](assets/api-dashboard.png)

# Make a GET Request with Postman

Now that we have access to the API, let's check out the structure of the data we'll be receiving. To do this, we'll use Postman.

Postman is an API Development Environment (ADE) and an awesome tool for any developer working with API's. We'll be using it in this tutorial to test making requests to the Product Hunt API and analyze the data we get to model the data in Swift.

1. Download the [Postman desktop app](https://www.getpostman.com/) from their website if you don't have it already. You don't need an account to use it.
2. Upon opening Postman, you should see this welcome screen:
  ![Postman welcome screen](assets/postman-welcome-screen.png)
3. Choose **Request** under building blocks to create a new request.
    - You can name the request whatever you want and save it whichever collection you choose; For this tutorial I'll be naming the request **Get Posts** and saving it in the **Product Hunt** collection.
2. Paste this URL into the URL field at the top of the window: `https://api.producthunt.com/v1/posts/all?sort_by=votes_count&order=desc&search[featured]=true&per_page=20`
3. Let's break down the URL:
    - `api.producthunt.com/v1` is the **base URL** for this API.
    - `posts/all` is the **endpoint** or **route** which gives you access to every product posted on Product Hunt (as long as you have a valid token!).
    - Everything after the **_?_** are the **paramaters** of our request and each parameter is seperated by **_&'s_**.
      - `sort_by=votes_count` and `order=desc` denotes that we want the resulting data to be organized by the number of votes in descending order.
      - `search[featured]=true` makes it so that we only get featured posts.
      - `per_page=20` limits the amount of posts we get to only 20. There are thousands of products listed on product hunt, we'll only need to get 20 at a time for our app.
4. Hit send. You should receive JSON data as a response to your request. This is what you should see:

![JSON response](assets/postman-get-request.png)

# Analyze Response

What you received from your request is known as JSON (Javascript Object Notation), a text format that is completely language independent and easy to read.

How the data in the JSON response is organized is extremely important, especially for our app. We'll need to understand the hierarchy of the data so that we can accurately model the data in Swift.

**See if you can find a product's name, tagline, number of votes, and screenshot URL.** Take note of where these are located:

![Structure of response](assets/response-structure.png)

1. The beginning of the JSON response objects is indicated by the opening bracket `{` and ends with the closing bracket `}` on the same level. This object only contains one object which is an arry of posts, where we will get all the information we need to display today's featured posts on our app.
2. The `posts` array contains the products that match the parameters given in our request. We asked for 20 of today's featured products in descending order of vote count, so we received an array with the first post being today's featured product with the highest votes.
3. The properties of the post object also has a hierarchy of objects. We are only concerned with `comments_count`, `id`, `name`, `tagline`, and `screenshot_url`. You can search up these terms in Postman by using the shortcut `CMD + F` and typing the name of the property in the search field that appears.
