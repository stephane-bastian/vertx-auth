The goal is to fulfill the requirements and at the same time minimize the modifications we've got to make.


**User (new)**

```
// RoutingContext no longer return a Session but instead it returns a User
// 
// note that a user is always returned, even when no authentication was explicitly set
// in that case, isAuthenticated returns false, and all methods hasXX return false as well
// logout is a no-op
interface User {
    // question: should the principal be a JsonObject or should it be a String?
    JsonObject getPrincipal();
    Session session();
    // internally, all methods bellow should delegate to the AuthProvider the user was built from
    void hasRole(String role, Handler<AsyncResult<Boolean>> handler));
    void hasRoles(Set<String> roles, Handler<AsyncResult<Boolean>> handler));
    void hasPermission(String permission, Handler<AsyncResult<Boolean>> handler));
    void hasPermissions(Set<String> permissions, Handler<AsyncResult<Boolean>> handler));
    boolean isAuthenticated();
    void logout(Handler<AsyncResult<Void>> resultHandler);
}
```

**Session (modified - removed everything related to Auth)**

Note that the Session now only contains session data. 
Should we call it SessionData to make it clear that its goal is only to carry data?

```
interface Session {
    Map<String, Object> data();
    // lets add a handler in case the caller needs to do something when the session is destroyed
    destroy(Handler<AsyncResult<Void>> resultHandler);
    <T> T get(String name);
    String id();    
    long lastAccessed();
    Session put(String name, Object value);
    <T> remove(String name);
    long timeout();
}
```

**AuthProvider (changed)**
```
interface AuthProvider {
    void hasRole(JsonObject principal, Handler<AsyncResult<Boolean>> handler));
    void hasPermission(JsonObject principal, String permission, Handler<AsyncResult<Boolean>> handler));
    // the only change so far is to merge the principal and credentials together
    // for a 'basic' login, authenticationData contains a username and password
    // but it could also contain a bearer token, a JWT token, etc... depending on the provider
    void login(JsonObject authenticationData, Handler<AsyncResult<Void>> resultHandler);
}
```

**TODO**

AuthService API must be updated.

**ISSUES**

- Is is still possible for the AuthService to be deployed somewhere on the event bus? 
The reason is that now AuthProvider are the only one to know where to get their token from. 
For instance, they could use a JWT token that is stored in a Cookie or a request parameter.
So it seems that all AuthProvider should have acess to the request somehow or setup a handler

- While the API doesn change much, internally all code related to Auth is tied to a Session, so there is quite a bit of work to refactor everything

**FAQ**

- How do I setup auth to chain multiple providers? Should we name provider so the caller can so something like authService.provider("facebook").login(xxx)?
 

