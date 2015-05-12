
**Requirements - MUST HAVE**

	1. Ability to plug **any** authentication mechanism (JDBC, Ldap, Facebook, Twitter, Google, OAuth, Oauth2, etc.) 
		This means that we've got to be completely agnotics regarding how Authentication providers:
		1. Initially authenticate users (Username/Password, redirect to a provider specific login form: Twitter/Facebook/Google/others, etc.)
		
		2. 'Remember'  authenticated users between requests

		Authentication providers could use whatever they want (bearer token, JWT token, etc.). 
		Also, they may use cookies or something else such as request parameters, the browser local storage, etc. 
		The point is that we can no longer assume that cookie is the only mechanism to carry authentication information between requests

	2. Ability to chain multiple AuthProvider to Authenticate/Authorize users in a Vertx App.
	
	For instance, an applications may need to support authenticating users via Facebook or its own JDBC table. 
	Usually the user selects the service it want to use to login (in our case Facebook or custom)
	Same goes for Authorization, an application may need to lookup permissions from 2 differents databases or from a database and an LDAP directory for instance. 
	
	3. By default, we've got to be session-less. 
	
	Obviously, sessions must be supported but should have nothing to do with the fact that users are authenticated or not
	
	4. Add a new interface called 'User' to check roles/permissions and get the session object

**Requirements - NICE TO HAVE**

	1. Provide an api to list all roles and permissions assigned to a user.

	If you only need to secure a web page, you would not need this. 
	
	However, lets say you are coding a web page to search users and lets say that the application supports fine graine. 
	
	In that case, the search result should only return users you are allowed to see. The only way to do this is to know all roles/permissions assigned to the current user 
	
	2. Add support to get Users profile (via Pac4j for instance) 
	
	2. Extend the purpose of Vertx-Auth to become the umbrella for everything related to securing a Vertx app.
	Right now the only goal of Vertx-Auth is to secure accessing web pages. so its names is fine. 
	However, It may make sens to provide more than this CSRF, CORS, utility for encryption, etc. 
	
	@Paulo: you talked about something around storing certificates but I forgot exactly what it is. Sorry. Could you please add it here?
	