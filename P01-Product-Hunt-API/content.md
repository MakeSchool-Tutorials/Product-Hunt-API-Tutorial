---
title: "Product Hunt API"
slug: product-hunt-api
---

Product Hunt is an amazing platform to discover new apps and projects.

# Check It Out

You will need to sign up for Product Hunt, register an application, and generate a token in order to use the API.

Explore the [website](https://www.producthunt.com/) and after creating an account, navigate to the [API dashboard](https://api.producthunt.com/v1/docs/). This is where we will create our application and generate a token to use the API.

# Create An Application

1. Click API dashboard at top right.
2. Click Add application.
3. Give your application a name and this redirect URI: `https://localhost:3000/users/auth/producthunt/callback`
4. Click create token

# Make a GET Request with Postman

1. Get Postman.
2. Past URL and input headers to make get request to posts.
3. Copy result and save for later.
4. Analyze structure to figure out model.
