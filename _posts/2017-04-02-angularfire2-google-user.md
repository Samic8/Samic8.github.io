---
layout: post
title:  "Google login with angularfire2"
date:   2017-04-02 +1000
categories: angularfire2
---

This is a specfic use case of firebase, it took me a while to setup myself so I thought it might be worth sharing.

Assuming that we already have angular2 setup we want to install [`angularfire2`](https://github.com/angular/angularfire2)
{% highlight bash %}
npm install firebase angularfire2 --save
{% endhighlight %}

Next assuming that we already have a [firebase project setup](https://firebase.google.com/docs/web/setup). In the root app module we want to initialize our firebase project

{% highlight ts %}
import { AngularFireModule } from 'angularfire2';

const firebaseConfig = {
	apiKey: "...",
	authDomain: "myapp.firebaseapp.com",
	databaseURL: "https://myapp.firebaseio.com",
	storageBucket: "myapp.appspot.com",
	messagingSenderId: "..."
};

@NgModule({
	...
	imports: [
		'...',
		AngularFireModule.initializeApp(firebaseConfig),
	],
})
export class AppModule { }
{% endhighlight %}

We are using google as our user authentication as a example here, firebase has other authentication methods email/password, facebook, github etc. We can setup what type of authentication we want to use during our app's root module initilization. We want to use the angular fire module [`AuthProviders`](https://github.com/angular/angularfire2/blob/master/docs/5-user-authentication.md) to specifiy our authentication type, and then specify our method (popup, redirect etc.) using [`AuthMethods`](https://github.com/angular/angularfire2/blob/master/docs/5-user-authentication.md).

{% highlight ts %}
import { AngularFireModule, AuthMethods, AuthProviders } from 'angularfire2';

const firebaseAuthConfig = {
  provider: AuthProviders.Google,
  method: AuthMethods.Redirect
};

@NgModule({
	...
	imports: [
		'...',
		AngularFireModule.initializeApp(firebaseConfig, firebaseAuthConfig),
	],
})
export class AppModule { }
{% endhighlight %}

Since we have setup authentication in the application bootstrap we can call 
{% highlight ts %}
af.auth.login();
{% endhighlight %}
We are able to call `login()` without any parameters because we have already setup our authentication provider and method in our root module initilization.

Because we setup `redirect` as our method, calling the af.auth.login() function will redirect the current page to a google login page, and then redirect to back to our application.

Now that the user has logged in and has been redirected back to our app, we want to be able to retrieve some info about the logged in user. We can subscribe to the login event using 
{% highlight ts %}
this.af.auth.subscribe(auth: FirebaseAuthState => {
	auth = auth;
});
{% endhighlight %}

The `FirebaseAuthState` object returned has a uid property which we can use as a key to store that users data in firebase.

angular fire provides a database object which allows us to change data within our projects database.
{% highlight ts %}
this.shoppingList = this.af.database.list(`/users/${auth.uid}/shoppingList`);
{% endhighlight %}
The `list` function allows us to store a `<array>`, there is also a `object` method available.

The database.list() function returns a [`observable`](http://reactivex.io/documentation/observable.html) which we can display in our template using the `async` pipe
{% highlight html %}
<li *ngFor="let listItem of shoppingList | async">
	{{listItem}}
</li>
{% endhighlight %}

To add items to our 'shoppingList' we can use
{% highlight ts %}
this.af.database.list(`/users/${auth.uid}/shoppingList`).push('bread');
{% endhighlight %}
