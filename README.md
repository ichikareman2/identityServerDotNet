# Notes


## Projects


### IdentityServer
* Has package Microsoft.AspNetCore.Authentication.JwtBearer
#### Config.cs
* Code as configuration approach 
* Apis = resources in system that you want to protect
* Clients = client application that will access API. Client credentials grant type
#### DiscoDoc
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

---

## Links:
* https://identityserver4.readthedocs.io/en/latest/quickstarts/1_client_credentials.html
* https://github.com/IdentityServer/IdentityServer4

