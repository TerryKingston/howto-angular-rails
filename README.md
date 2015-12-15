# howto-angular-rails
tutorial that describes getting started with angular and rails

# Introduction

## HTML

We'll start with the front end only in a single HTML page to see how simple it is to get some powerful results using Angular. 
We build a basic list of todo items

make a file called index.html and add this:
```html
<!DOCTYPE html>
<html>
<head>
    <title>My Simple Todo App</title>
</head>
<body>
<h2>Todo</h2>
<ul>
    <li>Do this!</li>
    <li>And this</li>
    <li>And also this</li>
</ul>
</body>
</html>
```

open the file in the browser and Tada! you have a web page. (I know, not quite what you were expecting, but we'll make it cooler very soon).

## Bootstrap

Now lets add some style to this app. We'll take the easy road and just use the CSS/Javascript library [Bootstrap](http://getbootstrap.com/). With an actual site you will want to consider using your own CSS theme, but the approach used by Bootstrap is a good way to develop your own theme so I suggest trying Bootstrap at first to get a feel for how you might want to do things yourself. 

For now, we'll use a source available on the web found [here](http://getbootstrap.com/getting-started/), and later we'll serve our own copy of Bootstrap source from our server.

Add the following between the <head> and </head> of index.html
```
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
```

Now we can add the prepackaged classes provided by bootstrap to our html components to infuse them with style! I'll also add a nav bar to make things look a little nicer.

The result looks like this:
```html
<!DOCTYPE html>
<html>
<head>
    <title>My Simple Todo App</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
</head>
<body>
<div class="navbar navbar-default">
  <div class="container-fluid">
    <div class="navbar-header">
        <a class="navbar-brand" href="#">A slightly more Awesome Todo App</a>
    </div>
  </div>
</div>
<div class="container-fluid">
    <div class="row">
        <div class="col-md-4 col-md-offset-4">
            <div class="panel panel-default">
                <div class="list-group">
                    <label class="list-group-item">Do this!</label>
                    <label class="list-group-item">And this</label>
                    <label class="list-group-item">And also this</label>
                </div>
            </div>
        </div>
    </div>
</div>
</body>
</html>
```

Refresh the page in your browser and behold the sort-of-awesome style!

## Angular

Great! we have an HTML page with a list of items and no way to modify it. Now lets make this static page more responsive by adding angular. Instead of rendering a bunch of labels, we'll let Angular make our list based on an array of todo objects. We'll also add a text box to add more todo's to the list. We'll start by moving the list of tasks from the static HTML to a list in the javascript portion of the page.

We'll bring in the angular code via CDN:
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.0-rc.0/angular.min.js">
    </script>
```

Then add our app and controller directives
```
<script type="text/javascript">
    angular.module("sampleTodoApp", [])
    .controller("todoController", ['$scope', function($scope){
        $scope.todos = [{title: "Do This!"},
                        {title: "And this"},
                        {title: "And also this"}]

    }])
</script>
```

This code says: "Angular, make a module and call it 'sampleTodoApp', then attach a controller to that module called 'todoController', then, when the appropriate html appears on the page run this function (the one with `function($scope){ ... }`" I'll talk more about the details as we go along.

Angular needs to know when it should care about a web page and what portion it should worry about. We tell Angular this by adding the directive `ng-app` to some html element. This tells Angular to 'compile' the stuff inside the element. The value of `ng-app` should match the name of our module declared above:

```html
<body ng-app="sampleTodoApp">
```

Because we could have multiple controllers for various portions of our html page, we have to tell angular what controller should govern what portion of the html. we do this with the `ng-controller` directive. 

```html
<div class="container-fluid" ng-controller="todoController">
```

Note: Later, you might agree with many [other developers](http://teropa.info/blog/2014/10/24/how-ive-improved-my-angular-apps-by-banning-ng-controller.html) and decide to ban the use of ng-controller for custom directives instead, but thats a lesson for another day.

now we can change the html list to make the list based on the data in $scope.todos declared above:
```html
<div class="list-group">
    <div class="list-group-item" ng-repeat="todo in todos">
        <label>{{todo.title}}</label>
    </div>
</div>
```

the `ng-repeat` directive above says, "Loop through the objects in the list called 'todos' and, one at a time assign them to the variable 'todo', then repeat this element for each one". This will repeat the `<div>` element as many times as there are todos in the list.

now lets add a text input to add more todo's to the list. Add the following below the </div> of the list-group:
```html
<div class="panel-body">
    <form class="form-inline">
        <div class="form-group">
            <input type="text" class="form-control" placeholder="new Todo">
            <button type="button" class="btn btn-primary">Add</button>
        </div>
    </form>
</div>

```

now to activate the form. We'll bind the text input to a variable in our $scope, and the button to a function defined in the scope:

in the `<script>` tag, todoController
```html 
$scope.newTodo = ''

$scope.addNewTodo = function(){
    $scope.todos.push({title: $scope.newTodo})
    $scope.newTodo = ''
}
```

The $scope variable represents the data and functions available to the html within the controller's scope (html inside the element with the ng-controller="todoController"). We can add data and functions in the JS and it automatically becomes available to the HTML within the controller's scope.

change the `<input>` and `<button>` to this:
```html 
<input type="text" class="form-control" placeholder="new Todo" ng-model="newTodo">
<button type="button" class="btn btn-primary" ng-click="addNewTodo()" >Add</button>
```

The `ng-model` above says "whatever is in the variable $scope.newTodo make the text in this input match, and if the user changes this text you should also change the value of the variable"
The `ng-click` above is similar to the regular `click` attribute except it doesn't fire until Angular has finished compiling the html and javascript. `ng-click` will look in the $scope for the function it's given.

Heres the full page so far:
```html
<!DOCTYPE html>
<html>
<head>
    <title>My Simple Todo App</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.0-rc.0/angular.min.js">
    </script>
    <script type="text/javascript">
    angular.module("sampleTodoApp", [])
    .controller("todoController", ['$scope', function($scope){
        $scope.todos = [{title: "Do This!"},
                        {title: "And this"},
                        {title: "And also this"}]

        $scope.newTodo = ''

        $scope.addNewTodo = function(){
            $scope.todos.push({title: $scope.newTodo})
            $scope.newTodo = ''
        }
    }])
    </script>
</head>
<body ng-app="sampleTodoApp">
<div class="navbar navbar-default">
  <div class="container-fluid">
    <div class="navbar-header">
        <a class="navbar-brand" href="#">A slightly more Awesome Todo App</a>
    </div>
  </div>
</div>
<div class="container-fluid" ng-controller="todoController">
    <div class="row">
        <div class="col-md-4 col-md-offset-4">
            <div class="panel panel-default">
                <div class="list-group">
                    <div class="list-group-item" ng-repeat="todo in todos">
                        <label>{{todo.title}}</label>
                    </div>
                </div>
                <div class="panel-body">
                    <form class="form-inline">
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="new Todo" ng-model="newTodo">
                            <button type="button" class="btn btn-primary" ng-click="addNewTodo()" >Add</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
</body>
</html>
```

## Rails

now, lets get the backend going. After installing [RVM](https://rvm.io/rvm/install) and [rails](http://railsapps.github.io/installing-rails.html), we'll make our project.

```bash
rails new sample-todo
```

This creates a bunch of files for us in the root directory sample-todo

Before we continue, we'll tell Rails not to use CoffeeScript for its JS files. We do this by commenting out the CoffeeScript Gem in the gem file. Find the file `sample-todo/Gemfile` and comment out the line:
```ruby
gem 'coffee-rails', '~> 4.1.0'
```

now lets make our TODO models, views and controllers. We'll use Rails' built in scaffolding system to generate a bunch of default files to get us going quickly.

```bash
cd sample-todo
rails generate scaffold todo title:text completed_at:datetime
```

This makes the models, views, and controllers for the todo object. this also made the migration which contain SQL commands to create the `todos` table in the database. We'll need to tell rails to run this migration to create the table before running the app:

```bash
rake db:migrate
```

(rake is the task runner inside rails. many tasks will be ran using rake commands)

now you can run the development server:
```bash
rails server
```

then point a browser window to `localhost:3000/todos` to see the canned views that `rails generate scaffold ...` made for you. They are even functional, you can add new todos, and see a list of todos. We want to replace these views with our Angular view. We'll replace index.html file at `sample-todo/app/vies/todos/index.html.erb` with our angular index.html file, but rails actually builds the page out of two seperate files, the layout is the file `sample-todo\app\views\layouts\application.html.erb`. But, we dont need to change this file because we are going to serve our JS assets (angular and bootstrap) from our own server instead.

To serve our own assets, we'll add a couple gems to our gem file. one to bring in the angular code, and one for bootstrap. In `sample-todo/Gemfile` add the following:

```ruby
# angular JS
gem 'angularjs-rails'

gem "therubyracer" # Javascript runtime needed for Less gem
gem "less-rails" #Sprockets (what Rails 3.1 uses for its asset pipeline) supports LESS
gem "twitter-bootstrap-rails"

```

now, Rails has its own way of including assets into the page. so instead of adding `<link rel="stylesheet" href=...` to the page header, we'll add a require statement to our base JavaScript file. Find `sample-todo/app/assets/javascripts/application.js` and add:

```javascript
//= require angular
```
(add this before the `//= require_tree .`  line)

the bootstrap gem comes with its own installer, so we'll use that (more detailed instructions found [here](https://github.com/seyhunak/twitter-bootstrap-rails)). run this command in the root folder of the app (sample-todo)

```bash
rails generate bootstrap:install less --no-coffeescript
```

And while were at it, we'll move our home made javascript to its proper home in `app/assets/javascripts/todos.js`:

```javascript
angular.module("sampleTodoApp", [])
.controller("todoController", ['$scope', function($scope){
    $scope.todos = [{title: "Do This!"},
                    {title: "And this"},
                    {title: "And also this"}]

    $scope.newTodo = ''

    $scope.addNewTodo = function(){
        $scope.todos.push({title: $scope.newTodo})
        $scope.newTodo = ''
    }
}])

Finally, find `sample-todo/app/views/todos/index.html.erb` and change it to this:

```html
<div ng-app="sampleTodoApp">
    <div class="navbar navbar-default">
      <div class="container-fluid">
        <div class="navbar-header">
            <a class="navbar-brand" href="#">A slightly more Awesome Todo App</a>
        </div>
      </div>
    </div>
    <div class="container-fluid" ng-controller="todoController">
        <div class="row">
            <div class="col-md-4 col-md-offset-4">
                <div class="panel panel-default">
                    <div class="list-group">
                        <div class="list-group-item" ng-repeat="todo in todos">
                        <label>{{todo.title}}</label>
                        </div>
                    </div>
                    <div class="panel-body">
                        <form class="form-inline">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="new Todo" ng-model="newTodo">
                                <button type="button" class="btn btn-primary" ng-click="addNewTodo()" >Add</button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

now you can start the server again to see the result
```bash
rails server
```

## REST

This is cool and all, but when you refresh the page, everything starts over. We'd really like to save the todos in a DB and have the changes show up when the page loads. The scaffolding already created a controller for us to use; its at `sample-todo/app/controllers/todos_controller.rb` and its already setup to respond to JSON Create, Update, and Delete requests. We just have to make our front end app (Angular) use the REST interface instead of the hard coded list of todos.

We'll use a very handy library called Angular Resource for this. It doesn't come bundled with angular so we need to require it seperately. in `sample-todo/app/assets/javascripts/application.js` add this line before `//= require_tree`

```javascript
//= require angular-resource
```

Also, we need to tell our Rails app that its ok to accept requests which do not have the [authenticity token](http://stackoverflow.com/questions/941594/understanding-the-rails-authenticity-token). Add the third line below to `sample-todo/app/controllers/todos_controller.rb`
```ruby
class TodosController < ApplicationController
  before_action :set_todo, only: [:show, :edit, :update, :destroy]
  skip_before_action :verify_authenticity_token, if: json_request?
```

and the following method to the bottom of the class, just before the last `end`
```ruby
def json_request?
    request.format.json?
end
```

Now, we'll do the rest in `sample-todo/app/assets/javascripts/todos.js`

Tell the angular module to inject this dependency into our app:
```javascript
angular.module("sampleTodoApp", ['ngResource'])
```

Now create a factory that will act as the JSON service provider for todos:
```javascript
angular.module("sampleTodoApp", ['ngResource'])
.factory('todoModel', ['$resource', function($resource){
    return $resource('/todos/:id.json', {id: "@id"}, {update: {method: "PUT"}})
}])
```

finally, tell the controller to use the service for the list of todos
```javascript
// $scope.todos = [{title: "Do This!"},
//                 {title: "And this"},
//                 {title: "And also this"}]

$scope.todos = todoModel.query()
```

now, after refreshing the page, the list is empty because the database is empty and when we add new todos it still doesn't save. We'll mod our addNewTodo method to save them.

```javascript



