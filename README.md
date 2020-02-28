# Notes


## Projects


### IdentityServer
* Has package Microsoft.AspNetCore.Authentication.JwtBearer
* `Config.cs`
  * Code as configuration approach 
  * Ids = **Identity resources** 
  * Apis = **resources** in system that you want to protect
  * Clients = client application that will access API. Client credentials grant type
* `DiscoDoc`
  * http://localhost:5000/.well-known/openid-configuration
  * Standard endpoint in identity server to download necessary configuration data

### Api
* Application that will be protected by Identity Server    
* `AddAuthentication` = adds the authentication services to DI and configures Bearer as the default scheme.
* `UseAuthentication` = adds the authentication middleware to the pipeline so authentication will be performed automatically on every call into the host.
* `UseAuthorization` = adds the authorization middleware to make sure, our API endpoint cannot be accessed by anonymous clients.

### Client
* Application that will access Api protected by IdentityServer
* Has package `IdentityModel` which is a client library that uses discovery endpoint. This way, you only need to know the base address of IdentityServer and the actual endpoint addresses can be read from the metadata.
* Copy and paste the access token from the console to `jwt.ms` to inspect the raw token
* To send the access token to the API you typically use the HTTP Authorization header. This is done using the SetBearerToken extension method:
```C#
// call api
var apiClient = new HttpClient();
apiClient.SetBearerToken(tokenResponse.AccessToken);

var response = await apiClient.GetAsync("http://localhost:5001/identity");
if (!response.IsSuccessStatusCode)
{
    Console.WriteLine(response.StatusCode);
}
else
{
    var content = await response.Content.ReadAsStringAsync();
    Console.WriteLine(JArray.Parse(content));
}
```
* By default an access token will contain claims about the scope, lifetime (nbf and exp), the client ID (client_id) and the issuer name (iss).
### MVC
* in `Startup.cs`, `AddAuthentication` adds the authentication services to DI
* We are using a cookie to locally sign-in the user (via `"Cookies"` as the `DefaultScheme`), and we set the `DefaultChallengeScheme` to `oidc` because when we need the user to login, we will be using the OpenID Connect protocol.
* We then use `AddCookie` to add the handler that can process cookies.
* `AddOpenIdConnect` to configure handler that performs OpenID Connect protocol.
  * `Authority` indicates where the trusted toekn service is.
  * We then identify this client via the `ClientId` and the `ClientSecret`. 
  * `SaveTokens` is used to persist the tokens from IdentityServer in the cookie.
* `UseAuthentication` in `Configure` is to ensure authentication services execute on each request. (Pipeline)
* `RequireAuthorization` method disables anonymous access for the entire application. You can also use the [Authorize] attribute, if you want to specify that on a per controller or action method basis.

---

## Testing the mvc client

1. Go to `localhost:5002` and you will be redirected to `localhost:5000` login page because you are unauthorized.
2. Login using the `TestUsers` credentials and you will be redirected to the returnUrl in querystring which is where you initially went: `localhost:5002`
   * On redirect to `localhost:5002`, the `Client`'s OpenID Connect authentication handler will process the response and sign in the user in its domain(locally) by setting a cookie.

---
## Sign out
* To logout, you need to clear the single sign-on session in `IdentityServer` aside from just clearing cookies in `mvc` (localhost:5002)
* This is already implemented in the OpenID Connect handler. simply call
  * ```c#
      SignOut("Cookies", "oidc");
    ```
  * which will clear cookies and make a roundtrip to the `IdentityServer`

---

## Links:
* https://identityserver4.readthedocs.io/en/latest/quickstarts/1_client_credentials.html
* https://github.com/IdentityServer/IdentityServer4

