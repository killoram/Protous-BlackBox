<img src="https://scontent-lga3-1.xx.fbcdn.net/v/t1.0-9/13322149_496026890588396_132787504032628895_n.jpg?oh=e73f599317982b03b9c0add391d0d10c&oe=57E1688B" alt="Protous BlackBox Banner">

A  Modulue Based, Client Side, Backend API and Application Framework for creating secure web and hybrid mobile applications.

##What does it do?

Protous-BlackBox brings secure backend functionality to the client side. This means that you can set up a fully functional web application without the need for a web server or database.

So where/when would you use that?
* It's the perfect solution for application prototyping as it makes for fast development, secure functionality, and requires nothing besides a browser. 
* It is also a good choice for quickly testing your UIs and website designs, if/when you want to test the scalability and flexibility of your designs.
* It's great for creating offline web applications and really any web application that needs to store data client side.
* Possibly best of all, it is a brilliant solution for HTML5 hybrid mobile apps.

It was designed to make user account systems a breeze to construct, and storing any sets of data just as easy.
It also has out of the box functions for handling and displaying RESTful API (JSON) data.

In one line of code you can construct a full backend of your application.
Say you want to have a user account system, and you want to store/handle posts, comments, likes, and friendships:

```js
var myApp = new PROTOUS_BACKBOX.app('friendships,posts,comments,likes','appUsers');
```
The above code creates a super object (myApp) that contains all the functions, mothods, and security measures for the whole application.
As soon as you place that line of code in your main module...
```js
var myApp = (function(){
  var backend = new PROTOUS_BLACKBOX.app('friendships,posts,comments,likes','appUsers');
  //Any aplication operations (mainly event responses and triggers) go here
})();
```
You're secure and ready to role!

###Security

Protous-BlackBox is entirely module based, so nothing but event triggering functions are available to the console.
This keeps potential "hackers" from expoiting your application's functionality. This is accomplished through the use of modules and a PubSub.

It also encrypts all application data (wtih SJCL) before storing it in the localStorage, so that your data is safe.
Every time the data is accessed it is re-encrypted with a new, randomly generated, encryption key. This adds yet another level of security to your application and its data.

## Contents
* [Getting Started](#getting-started)
* [Documentation](#documentation)
* [Application Syntax](#protous-application-syntax)
* [DataSection Functions](#datasection-functions)
* [User Account System Functions](#user-account-system-functions)
* [Form Handling Functions](#form-handling-functions)

# Getting Started

Note that Protous IS IN FACT MODULE BASED so IF YOU DO NOT UNDERSTAND MODULAR JS THEN LOOK IT UP! (Otherwise this will make no sense to you what so ever)

Lets say you want to test it out by making a simple online store application:
```js
var app = (function(PROTOUS_BLACKBOX){
	var backend = new PROTOUS_BLACKBOX.app("categories,products,cartProducts","customers"); //Construct a new app
})(PROTOUS_BLACKBOX);
```
The above code sets up the backend of your application.
Although it may not look like much...That middle line of code returns a super object full of functions for handling all data in your entire application and initializes your application storage.
Don't belive me? Try running ( var backend = new PROTOUS_BLACKBOX.app("categories,products,cartProducts","customers"); ) in your javascript console and navigate the returned object.
Now this short snippet of code creates the whole backend for your app but it is no use if you don't do anything with it...
```js
var app = (function(PROTOUS_BLACKBOX){
	var backend = new PROTOUS_BLACKBOX.app("categories,products,cartProducts","customers"); //Construct a new app
	var events = PROTOUS_BLACKBOX.import('eventSystem'); //import the eventSystem
	newResponse(events, 'submitProduct', function(data){ //Set a new response (in 'events') for the 'submitProduct' event
		backend['products'].add(data);
	});
})(PROTOUS_BLACKBOX);
```
The above code shows the use of the Protous event BUS (Which is technically a pubsub).
It allows you "respond", "trigger", or "neglect" an event. In this case, we are storing the event data as the product object in the backend of the application.
To trigger that event, you would say something like this:
```js
events.trigger('submitProduct', getWholeForm("form"));
```
Passing the data of the form, the submitProduct event is triggered and the assigned functions (from above) are run with the provided data.
You can add as many event responses as wanted. These allow you to define how you want Protous to react.
Now lets say you want to display all the products in a div from your DOM. The following HTML would do the trick:
```html
<loop ds-name="products" logic-all="true">
	<div class="product">
		<h1>(-title-) - <b>(-price-)</b></h1>
		<img src="(-image-)">
		<p>(-description-)</p>
	</div>
</loop>
<script>app.dsLogic();</script>
```
Assuming that your product objects have the following properties (title, price, description, and image) this would loop through all of your products (thus logic-all="true") and display the markup within the loop element (replacing the (-propertyName-)s with the property values) for each product that is stored. But wait, that won't work! You have not yet given app the dsLogic method. To import Protous functions simply return them as properties to your app object:
```js
var app = (function(PROTOUS_BLACKBOX){
	var backend = new PROTOUS_BLACKBOX.app("categories,products,cartProducts","customers");
	var events = PROTOUS_BLACKBOX.import('eventSystem');
	newResponse('userRegistered', function(data){
		backend['customers'].SignUp(data.username,data.properties,data.used||null);
	});
	return {
		dsLogic: PROTOUS_BLACKBOX.import('dsLogic')
	}; 
})(PROTOUS_BLACKBOX);
```
You could include all of the data looping functions if you wanted:
```js
var app = (function(PROTOUS_BLACKBOX){
	var backend = new PROTOUS_BLACKBOX.app("categories,products,cartProducts","customers");
	var events = PROTOUS_BLACKBOX.import('eventSystem');
	newResponse('userRegistered', function(data){
		backend['customers'].SignUp(data.username,data.properties,data.used||null);
	});
	newResponse('submitProduct', function(data){
		backend['product'].add(data);
	});
	return {
		protousLogic: PROTOUS_BLACKBOX.import('ProtousLogic'),
		generateAllLoop: PROTOUS_BLACKBOX.import('generateAllLoop'),
		generateWhereLoop: PROTOUS_BLACKBOX.import('generateWhereLoop'),
		addAllLoop: PROTOUS_BLACKBOX.import('addWhereLoop'),
		addWhereLoop: PROTOUS_BLACKBOX.import('addWhereLoop'),
		dsLogic: PROTOUS_BLACKBOX.import('dsLogic'),
		logicLoop: PROTOUS_BLACKBOX.import('logicLoop')
	}; 
})(PROTOUS_BLACKBOX);
```
But that isn't needed.

I hope this gave you a feel for what Protous 4 can do. This documentation will be improved upon soon, but let me know if you have any questions in the comments below.


# Documentation

PROTOUS_BLACKBOX.app is a constructor for creating the backend of an application.
SYNTAX: app(DATA_SECTIONS, USER_ACCOUNT_SYSTEMS);
DataSections are the types of data that you want to store and handle: posts, comments, products, messages, etc...
User Account Systems are exactly that
Both parameters are strings with each section or system devided by a comma.
Only one of the parameters need to be filled for the constructor to work. In order to bypass DataSections, simply set it to null.
Neither Parameters require more than one element but if there are they MUST BE SEPARATED WITH COMMAS.

##Protous Application Syntax

```js
var myApp = new PROTOUS_BLACKBOX.app("posts,comments,likes","users");

//Application Syntax: APP[DATASECTION][FUNCTION](PARAMETERS);
myApp['posts']['add']({
	title: "First Post",
	body: "This is my first post.",
	by: "Seth",
	date: new Date();
});
//app section  function  parameters
// |     |        |         |
myApp['users']['SignUp']("SethV"/*Username */, {//User Object:
	name: "Seth Vandebrooke",
	password: "*******",
	email: "example@example.com",
	joined: new Date();
}, function(){ //Function to run if the user already exists
	alert("This username: "+username+" has already been used");
});
//Application Syntax^
```
Protous application syntax is quite simple: AppName, DataSectionORUserAccountSystem, FunctionToRun, and Parameters.
As seen used above.
```js
APP[DATASECTION][FUNCTION](PARAMETERS);
```
Lists of functions and capabilities are below:

##DataSection functions:

Function                                    | Returns           | Description 
------------------------------------------- | ----------------- | --------------------------------------------------------------------
add(OBJECT)                                 | undefined         | Stores an object in the datasection
edit(whereThis,equalsThis,setThis,toThis)   | undefined         | Changes a property's value of any object who's given property equals the given value.
remove(whereThis,equalsThis)                | undefined         | Removes any object who's given property equals the given value.
get(whereThis,equalsThis)                   | object            | Returns the object who's given property equals the given value.
listAll()                                   | array of objects  | Returns all stored objects in the dataSection
search(whereThis,equalsThis)                | array of objects  | Returns all objects who's given property equals the given value.
filter(whereThese,equalsThese)              | array of objects  | Does the same as search but takes two arrays as parameters. One array of properties and another of values. All objects who's given properties equal the given values will be returned.
filterE(evaluations)                        | array of objects  | Runs an evaluation against every object in the dataSection. Every object where the evaluation(s) are true are returned. NOTE in the evaluations, if you want to reference the object that the evaluation is being run against simply use "object": Example... object.name!==null


##User Account System functions:

Function                                     | Returns           | Description
-------------------------------------------  | ----------------- | -------------------------------------------------------------------
SignUp(username,userObject,used)             | undefined         | Registers a user account with the given userObject and runs a function (used) if the username has already been used. NOTE: the used parameter is optional.
Login(username,password,wrongPass,noSuchUser)| undefined         | Logs the given user in if the username and password match and the user exists. If the user does not exist then it will run the function given for noSuchuser. Likewise if the password is incorrect then it will run the function given for wrongPass. Both wrongPass and noSuchUser are optional parameters.
LoginWEmail(email,password,wrongPass,noSuchUser)| undefined      | Logs the given user in who's email is equal to the given email if the email and password match. wrongPass and noSuchUser act the same as the Login function.
logout()                                     | undefined         | Logs the currently logged in user out.
deleteUserAccount(username)                  | undefined         | Deletes the user with the given username.
getUsername()                                | string            | Returns the username of the user that's logged in.
getUser(username)                            | object            | Return the userObject for the given username.
updateUser(username,OBJECT)                  | undefined         | Overrides the userObject for the given username with the given object.
updateUserProperty(username,propertyToChange,value) | undefined  | Updates (or edits) a property of the userObject for the given username by changing it to the given value.

##Form handling functions:

Protous 4.0 has 3 form handling functions:
```js
getFormValues(ids);
getWholeForm(formId);
validateForm(formObject,arrayOfExpressions);
```

getFormValues gets the values of the form elements via the given element IDs and returns an object. In the form object, the properties are equal to the ID of the element and the value is the obviously the value of the element.
Example:

Say you had an html form:
```html
<form>
<input type="text" id="username">
<input type="email" id="email">
<input type="password" id="password">
</form>
```
And you ran the following code in the JavaScript console:
```js
var form = getFormValues('username,email,password');
```
You would see that form becomes an object with properties that hold the values of the form data.
If you wanted to get the value of the username textfield you would merely need to say:
```js
form.username
```
This functionality allows you to specifically choose what elements to grab from, but if you make a form and you want all values from all of the form elements in the form then you should use the getWholeForm function...

getWholeForm grabs the all of the values from a form and returns an object. What will the property names be equal to? That depends. If you give your elements IDs then it will use the IDs. If you use the Name attribute instead then that's what it will use. If you have no form of identification then it will simply assign a property name that is equal to the tagname of the element and a random number.
Consider the following situation:

You want to let a user signup with the following form:
```html
<form id="myform">
<input type="text" id="username" placeholder="Username">
<input type="email" id="email" placeholder="Email">
<input type="password" id="password" placeholder="Password">
<input type="password" id="password2" placeholder="Confirm password">
</form>
```

You could easily grab all of the data like so:
```js
var form = getWholeForm("myform");
```
This automatically gathers and sorts the data into an object that is ready for use:
```js
app['users']['SignUp'](form.username,form);
```
This also works if you use names instead, or mix them both:
```html
<form id="myform">
<input type="text" id="username" placeholder="Username">
<input type="email" name="email" placeholder="Email">
<input type="password" id="password" placeholder="Password">
<input type="password" name="password2" placeholder="Confirm password">
</form>
```
Now that's all fine and dandy, but you wnat to make sure that your form data is validated bofore you go storing it.
This is made easy with the validateForm function.
The validateForm fucntion evaluations an array of expressions and returns true or false if the form data passes all of the tests or not.

```js
var myform = getWholeForm("myform");
if ( validateForm(myform, ["Form.password==Form.password2"]) ){
	app['users']['SignUp'](form.username,form);
}
```
The above code checks to see if the password field and confirm password field values match before signing up the user.
Notice that the properties are called using the "Form" object. That object stays the same no matter what your form object variable is actually called and is only used within the expressions inorder to reference the object that you passed to the function.
The Form object (as you might have guessed) holds all the properties for testing, so any property in your form object that you passed into the function is automatically inhrited by the Form object.

Lets say that you also want to test to make sure that there is an "@" symbol in the email field...
That can be done like so:

```js
var myform = getWholeForm("myform");
if ( validateForm(myform, ["Form.password==Form.password2","Form.email.replace('@','')!=Form.email"]) ){
	//...
}
```
As you can see, adding extra layers of validation is quick and easy to do with the validateForm function.


Well thats it for the documentation SO FAR.
More will be added soon enough, and if you have any questions or requests please leave them in the comments below!

[Back to the top](#contents)
