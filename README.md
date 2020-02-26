# Notes

## IdentityService

### Config.cs
#### = code as configuration approach 
    * Apis = resources in system that you want to protect
    * Clients = client application that will access API.
              = client credentials grant type

### DiscoDoc
#### = http://localhost:5000/.well-known/openid-configuration
    * = standard endpoint in identity server to download necessary configuration data


## Api
#### = application that will be protected by Identity Server
    
    * `AddAuthentication` = adds the authentication services to DI and configures Bearer as the default scheme.
    * `UseAuthentication` = adds the authentication middleware to the pipeline so authentication will be performed automatically on every call into the host.
    * `UseAuthorization` = adds the authorization middleware to make sure, our API endpoint cannot be accessed by anonymous clients.

