# The Scoop

## Project Overview

In this project, my responsibility was to build all of the routing and database logic for an article submission web app called The Scoop.

The Scoop allows users to:
- Create and log in to custom username handles
- Submit, edit, and delete articles containing a link and description
- Upvote and downvote articles
- Create, edit, and delete comments on articles
- Upvote and downvote comments
- View all of a user's articles and comments

The starting code of The Scoop contained all of the server logic for **users** and **articles**, but did not have the necessary database and route logic for **comments**.

- `database` is a JavaScript object containing all of the stored model instances. The `users` and `articles` properties contain JavaScript objects which will contain all of your created users and articles. Each of these objects will have keys representing the ID of the saved resource and values of the whole resource. For example if we created an article with ID `1`, the `articles` object would look like this `{1: {id: 1, url: 'article url', ...}}`. Additionally the `database` object contains a property for keeping track of the ID of the next article to create (to ensure no two articles have the same ID)
- `routes` is a JavaScript object containing all of the route's needed for The Scoop. The keys of this object are the path of the request, and the values are objects containing all of the possible HTTP verbs for that path. Each of those verbs map to a function to call if that route is hit and will be passed the path of the request and the request object (containing the request body)

## How To Begin

To start, download the code for this project. It requires a bash terminal program.

To start your server, run `npm install` and then `node server.js` from the root directory of this project. Every time you change **server.js**, you will have to restart your server before the changes will take effect. To do this press "control + c" in the bash terminal where your server is running (or close the terminal) to shut it down and then re-run `node server.js` to start it again. While your server is running, you will not be able to run commands in the bash terminal, so open a new terminal if you want to run other commands.

To view your local version of the site, open **index.html** in Google Chrome.

## Implementation Details

To complete this project, I needed to add the database information and server routes for Comments. Below is listed the high-level information about the Comment data model and the expected functionality of each Comment route. Additional implementation information, such as edge cases, can be discovered by running the test suite. (This can be achieved by running `npm test` from the command line once you have the local server running in another CL.)

### Database Properties

* **comments** - an object with keys of IDs and values of the corresponding comments
  - id - Number, unique to each comment
  - body - String
  - username - String, the username of the author
  - articleId - Number, the ID of the article the comment belongs to
  - upvotedBy - Array of usernames, corresponding to users who upvoted the comment
  - downvotedBy - Array of usernames, corresponding to users who downvoted the comment

* **nextCommentId** - a number representing the ID of the next comment to create (to ensure all comments have unique IDs), initializes to `1`


### Route Paths and Functionality

**/comments**
- POST
  - Receives comment information from `comment` property of request body
  - Creates new comment and adds it to database, returns a 201 response with comment on `comment` property of response body
  - If body isn't supplied, user with supplied username doesn't exist, or article with supplied article ID doesn't exist, returns a 400 response

**/comments/:id**
- PUT
  - Receives comment ID from URL parameter and updated comment from `comment` property of request body
  - Updates body of corresponding comment in database, returns a 200 response with the updated comment on `comment` property of the response body
  - If comment with given ID does not exist, returns 404 response
  - If no ID or updated comment is supplied, returns 200 response
- DELETE
  - Receives comment ID from URL parameter
  - Deletes comment from database and removes all references to its ID from corresponding user and article models, returns 204 response
  - If no ID is supplied or comment with supplied ID doesn't exist, returns 400 response

**/comments/:id/upvote**
- PUT
  - Receives comment ID from URL parameter and username from `username` property of request body
  - Adds supplied username to `upvotedBy` of corresponding comment if user hasn't already upvoted the comment, removes username from `downvotedBy` if that user had previously downvoted the comment, returns 200 response with comment on `comment` property of response body
  - If no ID is supplied, comment with supplied ID doesn't exist, or user with supplied username doesn't exist, returns 400 response

**/comments/:id/downvote**
- PUT
  - Receives comment ID from URL parameter and username from `username` property of request body
  - Adds supplied username to `downvotedBy` of corresponding comment if user hasn't already downvoted the comment, remove username from `upvotedBy` if that user had previously upvoted the comment, returns 200 response with comment on `comment` property of response body
  - If no ID is supplied, comment with supplied ID doesn't exist, or user with supplied username doesn't exist, returns 400 response

### YAML Saving and Loading

I was tasked with implementing a way to save the database information so that it was not lost when closing the server because the provided code didn't include this functionality originally.

I had to write two functions, one that saves the database object to YAML after each server call, and another that loads the database object when the server starts. The implemented logic for calling these functions was present, it was my task to find the appropriate JavaScript modules for this functionality and writing the following functions:

**loadDatabase**

- Reads a YAML file containing the database and returns a JavaScript object representing the database

**saveDatabase**

- Writes the current value of `database` to a YAML file

## Testing

A testing suite was provided in the starter code, checking for all essential functionality and
edge cases.

To run these tests, first, open the root project directory in your terminal. Then run `npm install` to install
all necessary testing dependencies (if you haven't already).
Finally, run `npm test`. You will see a list of tests that ran and all should pass currently.
