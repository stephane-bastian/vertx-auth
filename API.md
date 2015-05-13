The goal is to fulfill the requirements and at the same time minimize the modifications we've got to make.


**User (kind of replace a Session)**

```
// RoutingContext now returns a User instead of a Session
// 
// note that a user is always returned, even when no authentication was explicitly set
// in that case, isAuthenticated returns false, and all methods hasXX return false as well
// logout is a no-op
interface User {
    String id();
    // provides access to data specific to the user
    Map<String, Object> data();
    long lastAccess();
    // internally, all methods bellow should delegate to the AuthProvider the user was built from
    void hasRole(String role, Handler<AsyncResult<Boolean>> handler));
    void hasRoles(Set<String> roles, Handler<AsyncResult<Boolean>> handler));
    void hasPermission(String permission, Handler<AsyncResult<Boolean>> handler));
    void hasPermissions(Set<String> permissions, Handler<AsyncResult<Boolean>> handler));
    boolean isLoggedIn();
    void logout(Handler<AsyncResult<Void>> resultHandler);
}
```

**AuthProvider (changed)**
```
interface AuthProvider {
    void hasRole(JsonObject principal, Handler<AsyncResult<Boolean>> handler));
    void hasPermission(JsonObject principal, String permission, Handler<AsyncResult<Boolean>> handler));
    // for a 'basic' login, the authenticationToken contains a username and password
    // but it could also contain a bearer token, a JWT token, etc... depending on the provider
    // note that the result handler now return a JsonObject
    // this is mainly so we can build an oAuthProvider. In this scenario, the authenticationToken contains an authorization code
    // the result of calling login return an accessToken along other information, refresh token and such
    void login(JsonObject authenticationToken, Handler<AsyncResult<JsonObject>> resultHandler);
}
```

**TODO**

AuthService API must be updated.

**ISSUES**

- Is is still possible for the AuthService to be deployed somewhere on the event bus? 
The reason is that now AuthProvider are the only one to know where to get their token from. 
For instance, they could use a JWT token that is stored in a Cookie or a request parameter.
So it seems that all AuthProvider should have acess to the request somehow or setup a handler

- While the API doesn't change much, internally all code related to Auth is tied to a Session, so there is quite a bit of work to refactor everything

**FAQ**
- How do I setup auth to chain multiple providers? Should we name provider so the caller can so something like authService.provider("facebook").login(xxx)?
 

