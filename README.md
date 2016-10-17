![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)
# Views and Helpers

### Why is this important?
<!-- framing the "why" in big-picture/real world examples -->
*This workshop is important because:*

We want pages users can see and interact with! And we want to take advantage of how easy Rails makes views if we follow the "Rails way!"

### Objectives
*After this lesson, developers will be able to:*

- Describe how layouts, templates & views work together.
- Recognize rails url helpers and path helpers.
- Explain benefits of using Rails form helpers and link helpers.
- Find and determine correct syntax for Rails form helpers and link helpers.

### Where should we be now?
*Before this workshop, developers should already be able to:*

- Spin up a Rails app with a route and controller for a home page view.
- Incorporate routes, controller, and model for a single resource
- Read and write routes.

### Resources

The following can resources are great additions to this Readme:

* [Glossary](glossary.md)
* Rails Guides:
  * [Action View Overview](http://guides.rubyonrails.org/action_view_overview.html)
  * [Layouts and Rendering](http://guides.rubyonrails.org/layouts_and_rendering.html)
  * [Form Helpers](http://guides.rubyonrails.org/form_helpers.html)
* API documentation:
  * ActionView's [FormHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html)
  * ActionController's [`render`](http://api.rubyonrails.org/classes/ActionController/Base.html#class-ActionController::Base-label-Renders)
  * ActionController's [`redirect_to`](http://api.rubyonrails.org/classes/ActionController/Base.html#class-ActionController::Base-label-Redirects)


## Putting Together the Page

### Layouts

In Rails, the pages a user sees are rendered as a combination of a layout template and a view template. The view template, in turn, might be built up with one or more smaller "partial" templates.

We can use layouts for the lines of HTML that link site-wide CSS and JavaScript files and other material like a footer that stays the same across many pages from multiple controllers.

Views are rendered into the `<%= yield %>` tag in a layout.

When the app is created, Rails will automatically add a layout `application.html.erb` in `app/views/layouts/application.html.erb`. This layout already contains a yield statement and all the links to CSS and JavaScript files in the head part of the HTML document.

### Views

View templates contain all the content that's specific to one page.  

Each controller will have its own directory inside `app/views` to contain the view templates used by that controller.


### Partials

Partials allow us to take a portion of a template and move it into a separate file.  

**Check for Understanding:** Why might we want to separate out portions of our HTML view templates?

<details><summary>click for a few ideas</summary>
* A best practice is to always keep every template small. A rule of thumb would be to keep templates to about 50 lines for readability. If we wrote the bulk of our HTML directly in view templates, we'd quickly exceed this limit.
* We might have content that is repeated on more than one page associated with a controller - partials help us reuse HTML code for this content.
* Separating code into partials makes our views more modular and easier to change.
</details>

Using partials:

* Partials are always named starting with an underscore: `app/views/products/_form.html.erb` or `app/views/shared/_menu.rb`.  
* Partials can be called directly from inside a view file.  `<%= render "form" %>`.  Note that you don't  specify the `_` when rendering the partial.
* You can pass data to a partial: `<%= render partial: "customer", object: @new_customer %>`

You can also create partial layouts, but those are less common in apps of the size we're dealing with.


### Adding Partials to a View

We add the ERB from partials files into a view with `render`. Here are examples of common use cases:

1. Render a partial from a file called `app/views/products/_product.html.erb`.

  ```erb
  <%- # inside app/views/products/show.html.erb %>
  <%= render "product" %>
  ```

1. Render a partial from a file called `app/views/shared/_footer.html.erb`.
  ```erb
  <%- # inside app/views/products/show.html.erb %>
  <%= render "shared/footer" %>
  ```

1. Pass a variable `product` to a partial to be used within the partial:

  ```erb
  <%- # inside app/views/products/index.html.erb %>
  <% @products.each do |product| %>
    <%= render partial: "product", locals: {product: product} %>
  <% end %>
  ```

  Rails win! There's also a shorter syntax for the common pattern of repeating a partial for all items in a collection:

  ```erb
  <%- # inside app/views/products/index.html.erb %>
  <%= render partial: "product", collection: @products %>
  ```

###Check for Understanding: Using Partials and Layouts

1)  How can we split up pages into layout and views if the website is structured like this:

  ```
    -----    html head section     -----
    -----    header with menu      -----
    -----    current page content  -----
    -----    footer                -----
  ```

  <details><summary>click for an answer</summary>

  In Rails, the page head will already be in the application layout. We should probably move the top menu and the footer into the layout, too.  

  This give us a layout...

  ```
    -----    html head section     -----
    -----    header with menu      -----
    -----    <%= yield %>          -----
    -----    footer                -----
  ```

  ... and templates for the content of each page.

  ```
  -----    current page content  -----
  ```


  Remember: `yield` is where the current page content will be rendered.
</details>

Another alternative would be to move the header with menu and/or the footer into a partial instead.  This option would be best when for example we only use the menu on some pages, but not all. For instance, if the home page uses a hero image in place of the header, we should move that header with menu section to a partial.

2)  What would our layout, view, and template look like if the header with menu section is a partial?

  <details><summary>click for an answer</summary>

  **Layout:**

  ```
    -----    html head section     -----
    -----    <%= yield %>          -----
    -----    footer                -----
  ```

  **View Templates:**

  ```
  -----  <%= render "shared/header" %>   -----
  -----       current page content       -----
  ```
  and
  ```
  -----  <%= render "hero_image" %>  -----
  -----     current page content     -----
  ```

  **Partials:**

  ```
  ----- header with menu -----
  ```
  and
  ```
  ----- hero image -----
  ```

  </details>


A very common example of using partials in Rails is moving code for a new/edit form into a partial.

```
# new.html.erb
-----  new instructions text  -----
-----  form                   -----
-----  form-inputs            -----
-----  form-submit            -----
-----  next steps info        -----

# edit.html.erb
-----  edit instructions text -----
-----  form                   -----
-----  form-inputs            -----
-----  form-submit            -----
```

Take the form out of both pages and put it inside a partial.  Result:

```
# new.html.erb
-----  new instructions text  -----
-----  render :form           -----
-----  next steps info        -----

# edit.html.erb
-----  edit instructions text -----
-----  render :form           -----

# _form.html.erb
-----  form                   -----
-----  form-inputs            -----
-----  form-submit            -----
```


**Check for Understanding**

An app might has `app/views/products` with the files listed below.  Explain what each file is for.

```
_form.html.erb
edit.html.erb
index.html.erb
index.json.jbuilder
new.html.erb
show.html.erb
show.json.jbuilder
```


## Using Views with Rails

Now that we've gone over how layout templates, view templates, and partial templates come together to create the pages users see, let's dive into how our apps' controllers will trigger these views.


Every route in Rails will execute a method inside a controller. In Rails, the basic logic for rendering an HTML view is straightforward.  When the method is executed:

1. Rails looks for a folder inside `views` corresponding to the controller's name (folder `posts` for `PostsController`).
2. Inside that folder, Rails looks for an `.html.erb` file with a name matching the method's name.

For example, if we visit `http://localhost:3000/posts` in the browser, that GET request should be routed to the PostsController#index method (because of RESTful conventions).  
By default controller methods will render views files that match the controller method name. So when Rails executes the code in `index`, it will look for and render a template located in `app/views/posts/index.html.erb` unless we tell it to do something else.  

**Check for Understanding:** Which RESTful controller actions in Rails usually render an HTML view? What are their purposes?

<details><summary>click for a list</summary>
  * `index` - display a list of all the records for one type of resource
  * `show` - display details of one singe record
  * `new` - display a form for users to create a new record
  * `edit` - display a form for users to update a record
</details>


In the rest of the controller actions, you *won't* use the default rendering behavior.

**Check for Understanding**: Which RESTful controller actions in Rails DO NOT render an HTML view? What are their purposes?

<details><summary>click for a list</summary>
  * `create` - work with the database to create a record (based on form input)
  * `update` - work with the database to update one record (based on form input)
  * `destroy` - work with the database to destroy one record
</details>


Lets take a look at a `create` action:

```ruby
# inside app/controllers/PostsController.rb
def create
  if @post.save
    redirect_to @post, notice: 'Post was successfully created.'
  else
    render :new
  end
end
```

**Check for Understanding:** What is happening in the code above?

<details><summary>click for explanation</summary>

Based on the result of `@post.save`, the method will execute either the code in the `if` or in the `else`.

If the new post `@post` correctly saves, the response will be a redirect to that post's show page. If the new post doesn't save correctly, the new post form view is rendered again. Note that neither one of these renders a `create.html.erb` file.

</details>

We've seen two ways to do something other than rendering the HTML view that matches our controller action name:
 - We can redirect to another action.  
 - We can use a `render` statement to specify a view to render.

> HOORAY!  Rails 5 comes bundled with a `jbuilder` gem that helps us create JSON views instead of HTML. Why is this awesome?


## Helpers in Rails

Rails provides a ton of helper methods to make it easier to write code. Today, we'll look at a few important helpers that every Rails app should take advantage of.



### Path Helpers

Rails adds helper methods that return the paths for routes in your app.  These are configured through the `config/routes.rb` file.

Here's some `rake routes` output:

```
$ rake routes
     Prefix Verb   URI Pattern                 Controller#Action
    turkeys GET    /turkeys(.:format)          turkeys#index
            POST   /turkeys(.:format)          turkeys#create
 new_turkey GET    /turkeys/new(.:format)      turkeys#new
edit_turkey GET    /turkeys/:id/edit(.:format) turkeys#edit
     turkey GET    /turkeys/:id(.:format)      turkeys#show
            PATCH  /turkeys/:id(.:format)      turkeys#update
            PUT    /turkeys/:id(.:format)      turkeys#update
            DELETE /turkeys/:id(.:format)      turkeys#destroy
```

The **Prefix** on the left clues us in to the available URL and path helpers.  Just taking the first line, we can tell that a `turkeys_path` helper exists. This method will return the path that corresponds to the index route for all turkeys (`'/turkeys`).  Looking further down the line, we can see following helpers exist: `new_turkey_path`, `edit_turkey_path`, `turkey_path`.

Some of those routes require an **id**.  For those helpers, we'll pass an object (or an integer) to tell the helper how to fill in the id portion of the path.  

```
# examples
turkey_path(12)        =>  "turkeys/12/edit"
turkey_path(@turkey)   =>  "turkeys/#{@turkey.id}"
```


You can set individual route prefixes by using `as:` in your routes.

  ```rb
  # inside config/routes.rb
  get 'posts/new', to: 'posts#new', as: 'new_post'
  ```

The turkeys output from above used `resources` in the route configuration, so the prefixes we saw above were auto-generated by Rails. It's good practice to stick to these prefixes when possible even if you're writing out routes by hand instead of using `resources`.

  ```rb
  # inside config/routes.rb
  resources :turkeys
  ```

There are also URL helpers that use the same prefixes to generate a full URL instead of just the path part. For instance, `turkeys_url`. See the Rails Routing Guide [Path and URL helpers section](http://guides.rubyonrails.org/routing.html#path-and-url-helpers).

## View Helpers

Rails provides a huge swath of helpers designed to make it more convenient to generate HTML for your views, especially HTML related to your resources.  These view helpers also enforce the Rails way by automatically setting some attributes inside the HTML.  

Let's start by looking at some simple and very commonly used view helpers.

### `link_to` and `button_to`

Pair up. One person should silently skim the documentation for [`link_to`](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to).   The other should silently skim the documentation for
[`button_to`](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-button_to).

After 2 minutes, turn to your partner and explain what kind of element your method generates in the HTML and what kind of request we can expect that element to send.

As a team, list differences between `link_to` and `button_to`.  Come up with an example of where we could use each in a blog app.


<details><summary>click to see `link_to` examples</summary>
  Simple link:
  ```rb
  link_to "Profile", @profile
  # => <a href="/profiles/1">Profile</a>
  ```

  Setting class and id in HTML:

  ```ruby
  link_to "Articles", articles_path, id: "news", class: "article"
  # => <a href="/articles" class="article" id="news">Articles</a>
  ```
</details>

<details><summary>click for example of `button_to`</summary>

Image delete button
```rb
<%= button_to "Delete Image", { action: "delete", id: @image.id },
                                method: :delete, data: { confirm: "Are you sure?" } %>
# => "<form method="post" action="/images/delete/1" class="button_to">
#      <input type="hidden" name="_method" value="delete" />
#      <input data-confirm='Are you sure?' value="Delete Image" type="submit" />
#      <input name="authenticity_token" type="hidden" value="10f2163b45388899ad4d5ae948988266befcb6c3d1b2451cf657a0c293d605a6"/>
#    </form>"
```

</details>

#### PUT, PATCH, DELETE

Most browsers don't support PUT, PATCH & DELETE as form submission methods.  Rails however has a _work-around_ for this.  

Rails adds a hidden form field and processes this on a POST request, internally changing the request type.  

```erb
form_tag(search_path, method: "patch")
```

```html
<form accept-charset="UTF-8" action="/search" method="post">
  <input name="_method" type="hidden" value="patch" />
  <input name="utf8" type="hidden" value="&#x2713;" />
  <input name="authenticity_token" type="hidden" value="f755bb0ed134b76c432144748a6d4b7a7ddf2b71" />
  ...
</form>
```

> Tip: run `rake routes` and look at the Prefix column to see what `_path` helpers are available.

| Path helper          | using this path helper  with link_to |
|---------------------| -------------------------------------------|
| `turkeys_path`        | `link_to "view all turkeys", turkeys_path` |
| `new_turkey_path`     | `link_to "create a new turkey", new_turkey_path` |
| `edit_turkey_path`    | `link_to "edit this turkey", edit_turkey @turkey` |
| `turkey_path`         | `link_to "view this turkey", turkey_path @turkey ` |
| `turkey_path`         | `link_to "view this turkey", turkey_path 12` |

## Form Helpers

As we saw with `button_to`, Rails can generate forms for us.  There are two main kinds of helpers we can use to generate forms:  [FormTagHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html) and [FormBuilderHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html).  You can read more about both in the [Form Helpers Rails Guide](http://guides.rubyonrails.org/form_helpers.html)

* FormTagHelper's `form_tag` is very general, and it's often used for method/action combinations that don't map directly to a RESTful route for one of our resources.

  <details><summary>click for example</summary>

  >this ERB:

  >```erb
  <%= form_tag("/search", method: "get") do %>
    <%= label_tag(:q, "Search for:") %>
    <%= text_field_tag(:q) %>
    <%= submit_tag("Search") %>
  <% end %>
  ```

  >generates HTML:

  >```html
  <form accept-charset="UTF-8" action="/search" method="get">
    <input name="utf8" type="hidden" value="&#x2713;" />
    <label for="q">Search for:</label>
    <input id="q" name="q" type="text" />
    <input name="commit" type="submit" value="Search" />
  </form>
  ```

  </details>

* FormBuilderHelper's `form_for` is intended to work with our resources and RESTful routes. It does a lot to help you pre-fill values.


  <details><summary>click for example</summary>

  >this ERB:

  >```erb
  <%= form_for @article, url: {action: "create"}, html: {class: "nifty_form"} do |f| %>
    <%= f.text_field :title %>
    <%= f.text_area :body, size: "60x12" %>
    <%= f.submit "Create" %>
  <% end %>
  ```
  > generates HTML:

  >```html
  <form accept-charset="UTF-8" action="/articles" method="post" class="nifty_form">
    <input id="article_title" name="article[title]" type="text" />
    <textarea id="article_body" name="article[body]" cols="60" rows="12"></textarea>
    <input name="commit" type="submit" value="Create" />
  </form>
  ```

  </details>

### Form Builder and `form_for`

We'll mostly be working with resources, so it's important to get some exposure to `form_for`.

1) You build up a form in ERB.

```erb
<%= form_for @article, url: {action: "create"}, html: {class: "nifty_form"} do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :body, size: "60x12" %>
  <%= f.submit "Create" %>
<% end %>
```

Inside a form builder, you'll build up a form using an object.  That's the parameter in the  `form_for` `do`... `end` block. It's usually called `f`.

Each line inside the form will generate a label or an input.  Most of these `f.____` methods take in a symbol. That symbol tells the form builder which attribute of the model is being input in that field.  Looking at the form builder above, we can tell that the `@article` variable has a `title` attribute and a `body` attribute.

2) Rails uses this code to generate a form for you!

Note that when the HTML is generated, the **name** HTML attribute for each form `input` will be a combination of the model name and the attribute symbol.

```html
<form accept-charset="UTF-8" action="/articles" method="post" class="nifty_form">
  <input id="article_title" name="article[title]" type="text" />
  <textarea id="article_body" name="article[body]" cols="60" rows="12"></textarea>
  <input name="commit" type="submit" value="Create" />
</form>
```

The name HTML attribute is important to us in forms because these names become the keys in the data the client sends to the server.   

3) You get the data in your controller!

In Rails, we access this data in a controller through the `params` hash.  In the example above, the controller will receive a `params` hash that looks like this:

```rb
{
  'article' => {
                 'title' => '#whatever value the title text input had when submitted',
                 'body' => '#whatever value the body textarea had when submitted'
               }
}
```

It's a best practice to use "strong parameters," which is a pattern of using built-in methods to say what format of parameters your app will accept. With the example above, we'd want to write the following in our controller code:

```rb
private

# Using a private method to encapsulate the permissible parameters
# is just a good pattern since you'll be able to reuse the same
# permit list between create and update. Also, you can specialize
# this method with per-user checking of permissible attributes.
def article_params
  params.require(:article).permit(:title, :body)
end
```
<!--
## Independent Practice: View Helper Research

Research one of the methods below, and prepare to give a 2 sentence explanation about where it is used and what it does to everyone!


* `csrf_meta_tags`

* `stylesheet_link_tag`

* `javascript_include_tag`

* `audio_tag`

* `image_tag`

* `image_url`

* `video_tag`

* `time_ago_in_words`

* `hidden_field` or `f.hidden_field`

* `password_field`

* `color_field`

* `f.label`

* `check_box`

* `collection_check_boxes`

* `select` (form builder)

* `date_field`

* `date_select`

* `truncate`

* `pluralize`

* `simple_format`

* `number_to_human` -->


### Closing Thoughts

- Describe how layouts, templates & views work together.
- How can we figure out what path helpers are available?
- Why would we use `form_for` and `link_to`?
- Where would you look for syntax for Rails form helpers?
