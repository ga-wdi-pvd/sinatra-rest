# REST + Sinatra

## Obectives:
- Gain a basic understanding of HTTP.
- Understand what is a RESTful "method".
- Learn what the 5 most common RESTful methods are.
- Know what is a RESTful "route".
- Recognize the interaction of RESTful routes between the client and server.
- We're going to see examples from the industry using RESTful routes.
- Learn how to recieve query strings.
- Learn how to recieve data from a form.
- Know what RESTful action methods forms are limited to and how to get around them.

### Overview

We've been seeing that `get` word a lot. It has to do with HTTP requests.
Remember: a server's job is to respond to HTTP requests. In order to talk about
how Sinatra functions, we need to talk about how HTTP requests work.

### HyperText Transfer Protocol (HTTP)

HTTP functions as a request–response protocol in the client–server computing model.

Every HTTP request consists of a request **method** and **path**. The **path**
is the URL to which the request is being sent. That's pretty familiar. However,
your browser always sends that request in a particular *way* that gives the
server a hint as to the "point" of the request. This "particular way" is the
**method**.

### REST

Browsers have different "ways of talking" to servers. These are called
**methods**. REST, or REpresentational State Transfer, is a convention for what
these methods should be to standardize all the communication between browsers
and servers.

Knowing REST is important because the vast majority of web developers have
agreed to follow this same convention.

"GET" is one of these methods. It means the browser just wants to read (or
"get") some information. When you write `get '/say_hi' do`, you're telling your
Sinatra server how to respond when a browser says, "Hey, I'd like to get some
information from the `say_hi` path."

We make requests all the time -- especially GET requests. Everytime you go to
your browser, enter a URL, and hit enter, you're actually making a GET request.

### REST Methods

To better understand REST, the community has created another acronym called CRUD to better understand what each REST method does. 

You can dive deeper on REST and CRUD here at [HTTP Request Methods](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#frbanner3).

REST defines five main methods(technically there are more but there are 5 most commony used), each of which corresponds to one of the CRUD
functionalities.

| Method | Crud functionality |
|---|---|
|GET| read |
|POST| create |
|PUT| update |
|PATCH| update |
|DELETE| delete |

So, wait -- there are 5 REST methods, but only 4 CRUD methods?

PUT and PATCH are both used for updating. Whenever you update your Facebook
profile you're probably making a PUT or PATCH request. The difference is PUT
would be intended to completely replace your profile, whereas PATCH would be
intended to just change a few fields of your profile.

PUT rewrites data; PATCH just changes parts of data. To clarify further, PATCH is replacing part of the data and PUT is replacing the whole thing.

### What's the difference at a technical level between a GET and a POST request?

There really isn't a whole lot of difference. The browser sends the request
more-or-less the same way. The difference is in how the data comprising the
request is "packaged".

We'll see this in greater detail later. For now, just think that, say, a GET
request is sent in a plain old white envelope, while a POST request is sent in a
red envelope with "ACTION REQUIRED" written on it.

### RESTful Routes

A **route** is a **method** plus a **path**:

**Route = Method + Path**

Each route results in an **action**.

REST provides a template for the way different paths should look:

| Method | Path | Action |
| --- | --- | --- |
| GET | `/students` | Read information about all students |
| POST | `/students` | Create a new student |
| GET | `/students/1` | Read information about the student whose ID is 1 |
| PUT | `/students/1` | Update the existing student whose ID is 1 with all new content |
| PATCH | `/students/1` | Update the existing student whose ID is 1 with partially new content |
| DELETE | `/students/1` | Delete the existing student whose ID is 1 |

Note that the path doesn't contain any of the words describing the CRUD
functionality that will be executed. That's the method's job.

Let's check out the [ESPN website](http://espn.go.com/). If we go to a specific
player's webpage, we can see this same sort of structure in the URL.

### You do:

Create routes for the following requests. The first one is done for you.

1. Create a new animal
  - `POST /animals`
2. Delete an animal
3. Update an existing homework assignment
4. Create a new class at GA.
5. View all students in WDI.
6. Update the info for an animal with 3 as its id.
7. Update homework submission #32 for assignment #3

# BREAK

## Query Parameters

So far we've done all of our passing-of-dat through the route's path, as path
parameters. This hasn't been a problem since the data has been tiny -- a user
ID, or a name.

But when data starts getting bigger, this'll start getting a little crazy. We'd
need to be going to some enormous URLs.

Rest gives us a couple different ways of sending data. Path params are the most
upfront and basic. Slightly more advanced are GET paramters, or query
parameters.

### We do: Demo with Twitter

To demonstrate, let's check out Twitter's search functionality.

https://twitter.com/search

I'm going to search for "toast". This takes me to a new URL:

https://twitter.com/search?q=toast&src=typd

Check out the stuff at the end of the URL. I'm not sure what `src` means, but I
do recognize "toast", which is in `q=toast`.

If I add `+strudel` to the end of `toast` and go to the new URL...

https://twitter.com/search?q=toast+strudel&src=typd

I can see that the search box now has the words "toast" and "strudel", and all
the displayed Tweets contain those words.

### Querystrings

This is a **querystring**. It's a string of key/value pairs at the end of a URL
that pass additional data to the server.

Querystrings always follow the same format:

```
[some URL]?[key1]=[value1]&[key2]=[value2]&[key3]=[value3]...
```

First you have the URL, like `http://google.com` or `http://twitter.com/search` or `localhost:4567/hello`.

Then, you have a single question mark `?` that indicates the beginning of the
querystring.

Then, you have any number of pairs of `key=value`. Each pair is separated by an
ampersand `&`.

> Note: There is only **one** question mark in your querystring!

#### Looking back at the Twitter URL, with `q=toast+strudel`, what does `q` stands for?

### URL Encoding

URLs aren't supposed to have spaces in them, and some characters are reserved.
You can't just put a forward-slash `/` in a querystring because your browser's
going to think that you mean a directory. Any `&` in a querystring is going to
be interpreted as something separating key-value pairs.

So instead we encode "special characters" in a special way (*not* to be confused
with HTML's way of encoding special characters).

For example, spaces are replaced either with a plus `+`, or they are
**percent-encoded** with a special number. Space is `%20`.

You'll notice that these three links go to the same place:

https://twitter.com/search?q=toast%20strudel&src=typd

https://twitter.com/search?q=toast+strudel&src=typd

https://twitter.com/search?q=toast strudel&src=typd

In fact, in the last one, my browser replaced the space with `%20` itself!

[I like to use this site to URL-encode and URL-decode stuff](http://meyerweb.com/eric/tools/dencoder/)

### Querystrings in Sinatra

Querystring parameters are accessible in Sinatra paths just like path
parameters. Swap out your `app.rb` with this:

```rb
require 'sinatra'
require 'sinatra/reloader'

get '/' do
  "Hi there, #{params[:first_name]}!"
end
```

Now, go to `localhost:4567/?first_name=James`.

Let's add another parameter:

```rb
require 'sinatra'
require 'sinatra/reloader'

get '/' do
  "Hi there, #{params[:last_name]}, #{params[:first_name]} #{params[:last_name]}!"
end
```

...and go to `localhost:4567/?first_name=James&last_name=Bond`.

### You Do: Make the above statement show up in an ERB view! (5 minutes)

# Forms

So how do we get all this data into the URL's querystring to begin with?
Javascript? Nah, too much work. HTML actually has a built-in way of sending user
input. It's with the `<form>` element.

Copy and paste this code into your ERB:

```html
<form method="get" action=".">
  <input type="text" name="first_name" placeholder="First name" >
  <input type="text" name="last_name" placeholder="Last name" >
  <input type="submit" value="Submit" >
</form>
```

#### Type something in and "submit". What happens?

We can access this data in the `app.rb` file in the same way we got the path
parameters: with `params`.

```html
<h1>Hi there, <%= params[:first_name] %> <%= params[:last_name] %></h1>
<form method="get" action=".">
  <input type="text" name="first_name" placeholder="First name" />
  <input type="text" name="last_name" placeholder="Last name" />
  <input type="submit" value="Submit" />
</form>
```
> What's important to keep in mind with forms is that we need to make sure what method we're using--forms can only post and get-- and where we're submitting the request--the action.


### You do: Make the first and last names show up in the form using `@variables` instead.

```rb
require 'sinatra'
require 'sinatra/reloader'

get '/' do
  @first_name = params[:first_name]
  @last_name = params[:last_name]
  erb :index
end
```

```html
<h1>Hi there, <%= @first_name %> <%= @last_name %></h1>
<form method="get" action=".">
  <input type="text" name="first_name" placeholder="First name" />
  <input type="text" name="last_name" placeholder="Last name" />
  <input type="submit" value="Submit" />
</form>
```

### POST

Forms with a POST action are used for creating new things.

First let's make a new route in our `app.rb`:

```rb
get '/names' do
  @names = names
  erb :names
end
```
Let's add a `names` array too:

```rb
names = ["winston", "nelly", "rasheeda"]
```

Create a `names.erb` and add this:

```html
<% @names.each do |name| %>
  <h3><%= name %></h3>
<% end %>

<form action="/names" method = 'post'>
  <input type="text" name='student_name'>
  <input type="submit">
</form>
```
Let's try submitting this form. OH NOES. Sinatra doesn't know this diddy.

> Note that when we change whats in the action in the form. The diddy sinatra
doesn't know changes to whatever is in that action form.

Add this to `app.rb`:

```ruby
post '/names' do
  "this is a post request!"
end
```

Well this isn't very useful, instead i'd like to actually add to our names.

```ruby
post '/names' do
  names.push(params[:student_name])
end
```

Let's try submitting again. What happened? Hmm, seems like whatever we type in
gets rendered. Instead of that, I think it'd be really helpful if we just
redirected to our names route after we push the name to the array. Redirect is making a new get request and puts us in a new response request cycle.

Update `app.rb`:

```ruby
post '/names' do
  names.push(params[:student_name])
  redirect "names"
end
```

> In this situation we are actually redirecting to the get request to names.
This is traditionally how post requests work. Where some creation functionality
happens and then it redirects to a page.

> Another thing you may have figured out by now, is that we could have `names <<
params[:student_name]` in a get request, why might this be a bad idea? (ST- WG)
It goes against REST! `GET` requests should only be to access information.

> The difference between GET and POST is that GET sends data as URL parameters,
while POST sends data in the "body" of the request. This is the difference
between sending a postcard -- all the data is visible to anyone who happens to
be passing -- and a letter in an envelope -- the data is *not* visible to anyone
who happens to be passing.

#### Why have both?

- For what sorts of things is doing stuff by GET especially nice?
- For what other sorts of things is doing stuff by POST especially nice?

We've mentioned that POST is usually used to create stuff. Obviously, we're not
actually creating anything yet, but soon we'll be hooking up PSQL and
ActiveRecord to actually do so.

This all seems like a lot of trouble for a few routes. Why not just use GET
requests, which are nice and simple, and just have paths like `/read`,
`/update`, `/delete`, and so on?

### You do: [Play with HTML forms](https://github.com/ga-dc/html-forms-practice)

## PUT, PATCH, and DELETE

So what about the other REST methods?

HTML forms **can't actually put, patch, or delete**. They can only GET and POST.

We can do those in Ruby and in Javascript, which we'll cover later.
