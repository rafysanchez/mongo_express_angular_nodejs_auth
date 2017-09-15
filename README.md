# MEANauthapp


Complete MEAN stack application with authentication using json web tokens

### Version
1.0.0

## Usage
Using node v7.6.0 (npm v4.1.2)

### Installation

Install the dependencies

```sh
$ npm install
```
Run app

```sh
$ npm start
```


Go to angular-cli
& npm install && npm start




Fixed Issues and Errors:

SERVER SIDE
===========
	
1)	mongoose error in app.js

	Error when using: (deprecated) 
	// Connect To Database
	mongoose.connect(config.database);

	Solution:
	// Connect To Database
	mongoose.connect(config.database, {
	  useMongoClient: true,
	  /* other options */
	});

	source: (http://mongoosejs.com/docs/connections.html#use-mongo-client)


2)  JWT token error is users.js
	In User.js file

	Error:
	const token = jwt.sign(user, config.secret, {

	Solution:
	changed user to {data: user}



 CLIENT side:
 ============
	Fixed 404 error while registering
	In register.component.ts the onRegisterSubmit() method was not pointing to the right endpoint


	Updated code in auth.service.ts







CODE WITH CORRECT API ENDPOINTS: (auth.service.ts)
===============================
		import { Injectable } from '@angular/core';
		import {Http, Headers} from '@angular/http';
		import 'rxjs/add/operator/map';
		import {tokenNotExpired} from 'angular2-jwt';

		@Injectable()
		export class AuthService {
		  authToken: any;
		  user: any;
		  isDev:boolean;

		  constructor(private http:Http) {
		    this.isDev = true; // Change to false before deployment
		  }

		  registerUser(user){
		    let headers = new Headers();
		    headers.append('Content-Type','application/json');
		    return this.http.post('http://localhost:8080/users/register', user,{headers: headers})
		      .map(res => res.json());
		  }

		  authenticateUser(user){
		    let headers = new Headers();
		    headers.append('Content-Type','application/json');
		    return this.http.post('http://localhost:8080/users/authenticate', user,{headers: headers})
		      .map(res => res.json());
		  }

		  getProfile(){
		    let headers = new Headers();
		    this.loadToken();
		    headers.append('Authorization', this.authToken);
		    headers.append('Content-Type','application/json');
		    return this.http.get('http://localhost:8080/users/profile',{headers: headers})
		      .map(res => res.json());
		  }

		  storeUserData(token, user){
		    localStorage.setItem('id_token', token);
		    localStorage.setItem('user', JSON.stringify(user));
		    this.authToken = token;
		    this.user = user;
		  }

		  loadToken(){
		    const token = localStorage.getItem('id_token');
		    this.authToken = token;
		  }

		  loggedIn(){
		    return tokenNotExpired();
		  }

		  logout(){
		    this.authToken = null;
		    this.user = null;
		    localStorage.clear();
		  }
		}




CODE WITH WRONG API ENDPOINTS: (auth.service.ts)
===============================
		import { Injectable } from '@angular/core';
		import {Http, Headers} from '@angular/http';
		import 'rxjs/add/operator/map';
		import {tokenNotExpired} from 'angular2-jwt';

		@Injectable()
		export class AuthService {
		  authToken: any;
		  user: any;
		  isDev:boolean;

		  constructor(private http:Http) {
		    this.isDev = true; // Change to false before deployment
		  }

		  registerUser(user){
		    let headers = new Headers();
		    headers.append('Content-Type','application/json');
		    let ep = this.prepEndpoint('users/register');
		    return this.http.post(ep, user,{headers: headers})
		      .map(res => res.json());
		  }

		  authenticateUser(user){
		    let headers = new Headers();
		    headers.append('Content-Type','application/json');
		    let ep = this.prepEndpoint('users/authenticate');
		    return this.http.post(ep, user,{headers: headers})
		      .map(res => res.json());
		  }

		  getProfile(){
		    let headers = new Headers();
		    this.loadToken();
		    headers.append('Authorization', this.authToken);
		    headers.append('Content-Type','application/json');
		    let ep = this.prepEndpoint('users/profile');
		    return this.http.get(ep,{headers: headers})
		      .map(res => res.json());
		  }

		  storeUserData(token, user){
		    localStorage.setItem('id_token', token);
		    localStorage.setItem('user', JSON.stringify(user));
		    this.authToken = token;
		    this.user = user;
		  }

		  loadToken(){
		    const token = localStorage.getItem('id_token');
		    this.authToken = token;
		  }

		  loggedIn(){
		    return tokenNotExpired();
		  }

		  logout(){
		    this.authToken = null;
		    this.user = null;
		    localStorage.clear();
		  }

		  prepEndpoint(ep){
		    if(this.isDev){
		      return ep;
		    } else {
		      return 'http://localhost:8080/'+ep;
		    }
		  }
		}







