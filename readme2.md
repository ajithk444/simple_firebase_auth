There are plenty of examples online about setting up Firebase for Flutter so I will jump right into the code instead of walking thru the basics. 

>See [Google CodeLabs Flutter for Firebase](https://codelabs.developers.google.com/codelabs/flutter-firebase/index.html?index=..%2F..index#5) for step by step instructions for setting up you project on iOS or Android

### Create a Test User in Firebase
Since we are just building the application and there is no functionalty to create users in the application right now, please login to you Firebase Console and add an user to your project. Please be sure to enable email authentication when updating the project in your Firebase Console.


### Cleaning Up the Default Flutter Project

```
flutter create simple_firebase_auth
```
1. open up the project and delete the existing `HomePage` and `HomePageState` widget
1. open up the project and delete the existing `HomePage` and `HomePageState` widget


### Determining a User On Application Launch
The first challange when working with the application is to determine which page to open when the application starts up. What we want to do here is determine if we have a user or not. We will be using the `AuthService` we just created combined with the `FutureBuilder` widget from flutter to render the correct first page of either a `HomePage` or a `LoginPage`

Go into the `main.dart` file in your project and make the following changes to the main widget of your application. Replace the `home` property on

```javascript
home: FutureBuilder<FirebaseUser>(
    // using the function from the service to get a user id there
    // is one.
    future: AuthService().getUser,

    // we will build the next widget based on if there is a user
    // of not returned from the call to the AuthService
    builder: (context, AsyncSnapshot<FirebaseUser> snapshot) {
        // when the Future is completed, check to see if we got data
        if (snapshot.connectionState == ConnectionState.done) {
           final bool loggedIn = snapshot.hasData;

           // if we got data, then render the HomePage, otherwise
           // render the login page
           return loggedIn ? HomePage() : LoginPage();
        } else {
           // if we are not done yet with the Future, load a spinner
           return LoadingCircle();
        }
    }),
```

### Authentication Service Wrapping Firebase Functionality

First the authentication service which is where we are just wrapping some of the basic firebase functions that we need for authentication and determining if there is already a user persisted from a previous session

```dart
import 'package:firebase_auth/firebase_auth.dart';
import 'dart:async';

class AuthService {
  final FirebaseAuth _auth = FirebaseAuth.instance;

  // return the Future with firebase user if one exists
  Future<FirebaseUser> get getUser => _auth.currentUser();

  // wrapping the firebase calls
  Future logout() {
    return FirebaseAuth.instance.signOut();
  }

  // wrapping the firebase calls
  Future createUser(
      {String firstName,
      String lastName,
      String email,
      String password}) async {
    var u = await FirebaseAuth.instance
        .createUserWithEmailAndPassword(email: email, password: password);

    UserUpdateInfo info = UserUpdateInfo();
    info.displayName = '$firstName $lastName';
    return await u.updateProfile(info);
  }

  // wrapping the firebase calls
  Future<FirebaseUser> loginUser({String email, String password}) {
    return FirebaseAuth.instance
        .signInWithEmailAndPassword(email: email, password: password);
  }
}

```

### Create the LoginPage Widget
Lets walk through the creation of the `LoginPage` for the application. We need capture an `email` and a `password` to pass to the `AuthService` to call the login function.

We are going to create a simple page with the required `TextFormField` widgets and one `RaisedButton` to click to make the login happen.

1. Open your editor and create a new file in the `lib` directory named `login_page.dart`
1. Paste the contents below into the file `login_page.dart`
```javascript
import 'package:flutter/material.dart';

class LoginPage extends StatefulWidget {
  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Login Page Flutter Firebase"),
      ),
      body: Center(
        child: Text('Login Page Flutter Firebase  Content'),
      ),
    );
  }
}

```
you should be able to run the code to see what the screen looks like now. You might want to change the default route or `home` property in `main.dart` widget to `LoginPage` while we work through the UI so you can see the changes with live reload

##### Style and Adding Text Fields

Lets make the body of the page a centered `Column` with the childre of the column being primarily the `TextFormField`s and the `RaisedButton`

the centered container to hold the form fields and buttons
```javascript
    body: Container(
      padding: EdgeInsets.all(20.0),
      child: Column()
    )
```
Next add the actual form field widgets and the buttons
```javascript
  body: Container(
    padding: EdgeInsets.all(20.0),
    child: Column(
      children: <Widget>[
        Text(
          'Login Information',
          style: TextStyle(fontSize: 20),
        ),
        TextFormField(
            keyboardType: TextInputType.emailAddress,
            decoration: InputDecoration(labelText: "Email Address")),
        TextFormField(
            keyboardType: TextInputType.emailAddress,
            obscureText: true,
            decoration: InputDecoration(labelText: "Password")),
        RaisedButton(child: Text("LOGIN"), onPressed: () {}),
      ],
    ),
  ),
```
### Create the HomePage Widget
For now, we will keep the home page simple since we are just trying to demonstrate how the flow works. Ignore the commented out `LogoutButton` widget, we will discuss that in a later section of the tutorial.

1. Open your editor and create a new file in the `lib` directory named `home_page.dart`
1. Paste the contents below into the file `home_page.dart`

```javascript
import 'package:flutter/material.dart';

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return  Scaffold(
      appBar: AppBar(
        title: Text("Home Flutter Firebase"),
        //actions: <Widget>[LogoutButton()],
      ),
      body: Center(
        child: Text('Home Page Flutter Firebase  Content'),
      ),
    );
  }
}
```
3. Open `main.dart` and add the following import statement
```javascript
import 'home_page.dart';
```
4. Change the `home` property from this:
```javascript
home: HomePage(title: 'Flutter Demo Home Page'),
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;to this
```javascript
home: HomePage(),
```
