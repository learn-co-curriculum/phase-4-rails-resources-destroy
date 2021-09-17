# Rails Resource Routing: Destroy

## Learning Goals

- Delete a resource using Rails

## Introduction

In this lesson, we'll continue working on our Bird API by adding a `destroy`
action, so that clients can use our API to delete birds. To get set up, run:

```console
$ bundle install
$ rails db:migrate db:seed
```

This will download all the dependencies for our app and set up the database.

| HTTP Verb        | Path           | Controller#Action     | Description                |
| ---------------- | -------------- | --------------------- | -------------------------- |
| GET              | /birds         | birds#index           | Show all birds             |
| POST             | /birds         | birds#create          | Create a new bird          |
| GET              | /birds/:id     | birds#show            | Show a specific bird       |
| PATCH or PUT     | /birds/:id     | birds#update          | Update a specific bird     |
| **DELETE**       | **/birds/:id** | **birds#destroy**     | **Delete a specific bird** |

<!-- ## Video Walkthrough -->
<!-- <iframe width="560" height="315" src="https://www.youtube.com/embed/XmTWuLphloM?rel=0&showinfo=0" frameborder="0" allowfullscreen></iframe> -->

## Deleting a Bird

We're at the last step on our journey to becoming CRUD experts! Our goal is to
give users the ability to delete birds via the API. To start, we'll need to set
up a route to handle a `DELETE /birds/:id` request. We can do so by adding
`:destroy` to our resources:

```rb
resources :birds, only: [:index, :show, :create, :update, :destroy]
```

And since we're now using all five RESTful routes, we can omit the `only` option:

```rb
resources :birds
```

Running `rails routes` will show us all the RESTful routes in our application,
plus our custom route:

```console
$ rails routes
Prefix Verb   URI Pattern               Controller#Action
 birds GET    /birds(.:format)          birds#index
       POST   /birds(.:format)          birds#create
  bird GET    /birds/:id(.:format)      birds#show
       PATCH  /birds/:id(.:format)      birds#update
       PUT    /birds/:id(.:format)      birds#update
       DELETE /birds/:id(.:format)      birds#destroy
       PATCH  /birds/:id/like(.:format) birds#increment_likes
```

We'll also need to add a `destroy` action to our controller where we'll be
deleting the bird from the database:

```rb
def destroy
  bird = Bird.find_by(id: params[:id])
  if bird
    bird.destroy
    head :no_content
  else
    render json: { error: "Bird not found" }, status: :not_found
  end
end
```

In this controller action, our goal is to:

- Find a bird using the ID from the route params
- Remove it from the database with `bird.destroy`

You'll also notice that instead of rendering a JSON response, we're returning
`head :no_content` if our bird was successfully deleted. `:no_content` will give
a 204 status code, indicating that the server has successfully fulfilled the
request and that there is no content to send in the response. We're also not
sending any payload of data in the body of the request.

One thing to watch out for: if the API doesn't return JSON data, and you try to
read the response data from a `fetch` request, you will get an error:

```js
fetch("http://localhost:3000/birds/3", {
  method: "DELETE",
})
  .then((r) => r.json()) // this line will error out, because there is no JSON to parse!
  .then((data) => console.log(data));
```

Depending on your needs, you could send back a JSON response to verify that the
request was completed successfully. For example, `json-server` handles a
successful delete request by sending an empty object:

```rb
bird.destroy
render json: {}
```

Ultimately, you can decide which option you prefer based on how you'll use this
data in your client application.

Make sure to test these out in Postman to see the difference between using
`head` and `render`.

## Conclusion

Now that we've covered the Delete action, you can perform all four CRUD actions
with Rails and do so following RESTful conventions!

## Check For Understanding

Before you move on, make sure you can answer the following question:

1. What options did you learn about in this lesson for returning information to
   your users about the status of a delete request?

## Resources

- [The `head` method](https://api.rubyonrails.org/v6.1.3.1/classes/ActionController/Head.html#method-i-head)
- [DELETE request status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/DELETE#responses)
