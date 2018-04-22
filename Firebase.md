# Firebase
*	We're going integrate Firebase as our database/CRUD in our web apps... AKA *persistent data*
*	Firebase is a noSQL database that is like a JS object with key-value pairs

## Getting started
*	**Rules** allow who can read/write to the database
	* 	For dev purposes: set true for all read/write accesses

### Firebase.js
*	We want to create a firebase directory and file 
*	This imports all the **named exports** from firebase and stores it in variable firebase
	* 	This gives us access to all the methods inside it e.g. ``` firebase.method()```

```javascript
import * as firebase from 'firebase';
```

*	We then want to **copy the config** provided to a config const and then initialize with it

### To test connection
```javascript
firebase.database().ref().set({
	name: 'Jordy Guntur'
});
```

*	We want to import the firebase file to *app.js*
	* 	```import './firebase/firebase```
*  You can then check the data in the database console to see if it works

## Writing to the Database
*	Firebase can work with all the types you can think of
* 	You can also nest objects that you are writing to the database

```javascript
firebase.database().ref().set({
	name: 'Jordy',
	nest: {
		nested: 'test 1',
	}
});
```

*	Green means nothing changed and orange means that something did change
* 	You can reference out database from firebase.database()
	* 	``` const database = firebase.database()```
*	**What you pass into ref() is what part of the database that you are changing, without it, it will overwrite the entire database**

```javascript
// Writing to the age within the database
database.ref('age').set(27)
```
 
```javascript
// Writing to a nested object within the database
database.ref('location/city').set('NY'))
```

# ES6 Promises
*	Allows us to do something after a long running task completes
* 	```promise.then()``` registers a callback when a **promise is resolved**
*  	When a promise is resolved or rejected, it cannot be resolves or rejected again
	*  	Only first resolve will register and it only accepts a string or an object

```javascript
// Function that is called after a promise is resolved
promise.then((data) => {
	console.log(data);
})
```

*	To fix that the function only runs on being resolved, we can catch when it rejects and console.log the error

```javascript
promise.then((data) => {
	console.log(data);
}).catch((error) => {
	console.log(error);
});
```

*	You can actually do the exact same thing by passing the second function as an argument:

```javascript
promise.then((data) => {
	console.log(data);
}, (error) => {
	console.log(error);
});
```

*	Either one works but the one with catch is kinda more explicit and clear
* 	Most of the time, you don't need to make your own promise and its provided by the library you are using
*	Set() returns a promise so we can chain our .then() function with that

## Removing Data from Firebase
*	You can either call remove on the reference or just set it to null

```javascript

```

