---
layout: post
title: Updating My Rails Project with Javascipt
author: Lucas Moore
---

I had a lot of fun building my [first rails app](http://dev.thelucasmoore.com/2016/05/01/Rails-Project-Time.html) a few weeks ago.
Now it's time to go back over the project and add in some functionality with javascript. I used jQuery to build some front end elements here.

You can see a quick run through of this code in action here, or in the nifty gifs below.
This is a longer post because I learned a lot here. 

<iframe width="420" height="315" src="https://www.youtube.com/embed/Owt-AIWWckc" frameborder="0" allowfullscreen></iframe>

## Rendering Rails Model Data in JSON

There's three simple steps to turn rails data models into JSON.

First, I added the *'active_model_serializers'* gem to the gemfile and ran <code>bundle install</code> and <code>rails g serializer (modelname)</code> for each model I wanted to serialize. Then in *App > Serializers* I edited the serializer format to include the data I needed.

``` rails
class RecipeSerializer < ActiveModel::Serializer
  attributes :id, :name, :description, :user_id, :rating
  has_many :ingredients
end
```

To render this data in the view, we can add the following into the controller instead of simply 'render :show'.

``` rails
def show
  ...
  respond_to do |format|
    format.html {render :show}
    format.json {render json:@recipe}
  end
end
```
So now when I go to <code>/recipes/:id.json</code>, the JSON object is there, waiting to be parsed! 
To accomplish my goals on this lab, I serialized recipes, ingredients and comments.

## Grabbing Data with $.getJSON

![js-recipe](/assets/js_ingredient_show.gif)

I create page specific javascript using <code><%= content_for :ingredient do %></code> in my views.

As you can see in the gif above, I added a click event to the ingredient show page, which grabs the JSON. 
It then gets parsed and displayed. So the recipe show page is rendered within another page, with the options to read more or hide the information. 

``` javascript
var linkOverride = function() {
  $("a#recipe").click(function(event) {
    event.preventDefault(); // prevent link from clicking through
    loadRecipes(event["target"]["href"]); // pass link to loadRecipes method
  });
};

function loadRecipes(target_url) {
  $.getJSON (target_url + ".json", function(result) { // add .json to the url
    handleResponse(result); // pass off to handle the response
  })
};
```

## Creating New Data with $.Ajax Requests

![new comment](/assets/js_add_comment.gif)

Getting a little more complex now, I built instant comment functionality. A user adds a new comment, which makes an Ajax request to create the comment. 
At the same time, the comment is instantly rendered on the page and the comment box is hidden, limiting recipes to having one comment per user per visit. 
I utilized the magic of the <code>serialize()</code> method, which passes the form submission as serialized data, which rails is ready to process.

The code to accomplish this: 

``` javascript
var createMethods = function() {
  $('div#new_comment form').on("submit", function(event) { // identifying the form with jQuery selectors
    event.preventDefault(); // prevent form from submitting
    var recipe_id = event['currentTarget']['4']['value']; // grab recipe ID from the event

    var values = $(this).serialize(); // I understand this magic.

  $.ajax({ // post serialized data to the restful route
    url: "http://localhost:3000/recipes/" + recipe_id + "/comments/",
    method: 'post',
    data: values,
    dataType: 'json'
  }).done(function(data) { // when done, render that comment instantly on the page.
      $('div#new_comment form').hide();
      $('.newUser').text("<%= current_user.name %>" + " says:");
      $('#newComment').text(data['content']);
    });
  })
}
```

## Editing and Updating Data with $.Ajax

I used similar code to edit, update and delete comments. The structure is basically the same as above, so check out the functionality and comments for each one. 

# Editing Comments

![edit comment](/assets/js-edit-comment.gif)

The edit comment form is hidden with CSS and needs to be shown conditionally when a user clicks on the edit link.
The most difficult part about editing comments in this way was selecting the exact comment to show only that form. 

I accomplished this by passing the comment ID into the class, so each comment and form was within a unique class, like <code>.commentos-64</code>.

``` erb
# views/comments/index.html.erb
<div class="commentos-<%= comment.id %>">
  <%= comment.content %>
    <form id="edit_comment_form">
      <%= text_field_tag :content, comment.content %><br>
      <%= hidden_field_tag :user_id, current_user.id %>
      <%= hidden_field_tag :recipe_id, @recipe.id %>
      <%= hidden_field_tag :id, comment.id %>
      <%= submit_tag "Edit", id: :submit_edit %>
    </form>
</div>
```
After much tinkering, the key was to stop using generic class-based selectors (<code>#edit_comment_form</code>) and to dig out the next sibling, parent node of the click event, which was the that form specifically: <code>.commentos-(id) form</code>. Then the CSS of the form is switched to show, allowing a user to edit the comment. 

# Deleting Comments

![delete comment](/assets/js_comment_delete.gif)

I wanted to have a confirmation prompt, to ensure a user meant to delete their comment. If they say yes, the logic to delete the comment is executed. 

``` javascript
var deleteMethods = function() {
  $('a#delete').on("click", function(event) {
    event.preventDefault();
    var url = event['target']['href'];
    var result = confirm("Are you sure?"); // tied confirmation to a variable
    if(result) { //only if the variable returns true, execute deletion
      $.ajax({
        url: url,
        method: 'delete'
      }).done(deleteResponse(event));
    }
  });
}
var deleteResponse = function(event) { //overwrite comment with deleted message
  var comment = event['target']['parentElement'];
  $(comment).text("Comment Deleted");
} // the comment will disappear upon page refresh
```

# Updating RecipeIngredients

![recipe ingredients](/assets/js_update_ingredients.gif)

I also wrote the functionality to update the quantity of ingredients in a recipe, which is stored in a RecipeIngredients join table. A simple alert pops up to show that the update was successful. 

## So What Now?

I am just starting to get my hands dirty with javascript. I have a lot more to learn. I understand that a user could edit anyones comments by manipulating the CSS of the comment edit form, showing it and updating the comment. I have some ideas of how to fix that in my next update.

But mostly, I am excited to finally understand all of these interconnected parts of a web application. Rails for the backend, HTML and CSS in the view, Javascript for front end logic. Seeing this security flaw is a step forward. 

By the way, I made these rad gifs with [the giphy capture tool](http://giphy.com/posts/giphy-launches-giphy-capture-for-mac).

