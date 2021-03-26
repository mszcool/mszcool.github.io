---
layout: post
title:  "ASP.NET 4.5.1, 'general' integration with OAuth2 and OAuth Authentication Servers"
date:   2013-12-20 12:00:00 +0100
categories: wordpressarchive
tags: Archive OAuth ASP.NET .NET Authentication Authorization Security
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

Over the past weeks I did work with several software vendors all having the same question: how does the integration of OAuth2 really look like in ASP.NET 4.5.1?

<!--more-->

Well, although that sounds like a simple question to answer with point to <http://www.asp.net/identity> (which is btw. a good starting-point to dig deeper to better understand what I am writing in this post), it is not, indeed!

What you’ll quickly see is that the walk-through samples on the official ASP.NET homepage show all sorts of code and how-to for integrating either with social identity providers (e.g. Facebook, Microsoft Account, Google, Yahoo) or with [Windows Azure Active Directory (short WAAD)](https://archive.is/o/VsJuo/www.windowsazure.com/en-us/services/active-directory/).

That is cool, but what if you’re really interested in the native OAuth-integration in ASP.NET? What if you’re interested to understand, how-to integrate your WebAPI with any OAuth authorization server, e.g. your own or a 3rd-party authorization server?
Questions that nearly all of the partners and customers I talked to over the past 3 weeks had these questions and the official docs did not really provide an easy-to-understand answer. With this blog-post I try to give that answer and an how-to for you.

### First – Understanding OAuth Implicit Grant-Flow

First of all, you need to understand, how OAuth implicit grant approximately works. In essence it is just a bunch of browser-redirects that do take place. The following picture stolen from MSDN explains, what’s going on:

![OAuth Flows Explained](/images/posts2013/20131220 - ASP.NET 4.5.1 and OAuth2 (0).png)

Whenever a users browses to a secured site (= resource provider, relying party) anonymously, then that secured site redirects the user to the authentication service. The user then authenticates against this service and assuming that was successful, the authentication service responds with a browser-page that contains an access token (typically signed, eventually encrypted). That browser page returned then posts the token to a target URL on the resource provider that is registered with the authentication service. The resource provider then decrypts and validates the signature of the token and then the user is authenticated against the resource/site. If the resource/site is a web page, it can then issue its own authentication cookie as usual, if it is a web API, the client (mostly an app on a device or a JavaScript client) can post the token to the WebAPI with every request as long as the token is valid (simplified!).

If that process takes place in an app, a very typical approach is to “host” a browser control that performs this process of browser-redirects, but instead of posting the token back to the resource provider, the client/app intercepts that post and grabs the token to include it in the authentication header for HTTP requests to the WebAPI. The following picture shows that:

![Browser Redirects Explained](/images/posts2013/20131220 - ASP.NET 4.5.1 and OAuth2 (1).png)

For example, for Windows 8 store apps, the [WebAuthenticationBroker-class](https://docs.microsoft.com/en-us/uwp/api/Windows.Security.Authentication.Web.WebAuthenticationBroker?redirectedfrom=MSDN&view=winrt-19041) encapsulates the basic process outlined in the previous image for you. It hosts a browser control to do the following:

1. Open an authentication URL on the authentication server
2. Perform all browser requests required for the authentication
3. When the authentication server responds with a redirect URL the broker knows, it 
4. intercepts this redirect URL and gives it to you so you can grab the token.
5. Finally it posts the token in the HTTP Authorization-header when calling the WebAPI (resource provider)

For this process to work, the client-app must be registered as a valid client at the authentication server and of course the WebAPI must also be registered as a valid resource provider in the authentication server. Typically auth-servers such as [Windows Azure AD](https://archive.is/o/VsJuo/www.windowsazure.com/en-us/services/active-directory/) or [Thinktecture Identity Server](https://archive.is/o/VsJuo/thinktecture.github.io/) do provide these kind of configurations. If you build your own authentication/authorization server, you should follow the same approach for security reasons.

### Second – Get up2speed with ASP.NET 4.5.1 and OWIN/Katana

It helps to understand, what OWIN and Katana are all about to fully understand the code that configures the OAuth2-providers for ASP.NET WebAPIs. But to be honest, if you’re not interested in these details and just want to get it working, you can skip this as well and accept code as it is:) 

So I keep it short as well: if you want to understand OWIN and Katana, watch this video on Channel9 – it really explains the strategy very well: <http://channel9.msdn.com/Shows/Web+Camps+TV/The-Katana-Project-OWIN-for-ASPNET>.

In essence one of the important aspects of OWIN/Katana is to decouple the integrated ASP.NET components a bit so that the whole framework gets less “monolithic” and can be easily composed of re-usable, small and slim modules as needed without loading always a big System.Web.dll into memory.

### NOW Rock – Connect your WebAPI with any OAuth2 Provider

All OAuth2-functionality starting with ASP.NET 4.5.1 and Visual Studio 2013 is implemented in OWIN/Katana-Modules. That said, when you want to integrate your WebAPI with an OAuth Authentication/Authorization server, you need the following NuGet-package which do contain the generic OWIN-implementation:

* Microsoft.Owin.Security.OAuth
* Newtonsoft.Json
* Microsoft.Owin
* Owin
* JSON Web Token Handler For the Microsoft .NET Framework

Note that when installing Microsoft.Owin.Security.OAuth that you get all packages as dependencies except the JSON Web Token Handler which you then need to add manually. The JSON Web Token Handler is required to implement the token validation functionality which is not part of the OWIN OAuth module.
After you have added that, you can register the OAuth-provider as shown in the code below in your OWIN configuration class – with the default ASP.NET templates in Visual Studio 2013 that is in App_Start\Startup.Auth.cs whereas if you do things by yourself you could add that code in your OWIN Startup-Class, directly:

```csharp
app.UseOAuthBearerAuthentication (
    new OAuthBearerAuthenticationOptions()
    {
        Realm = "http://mysimpletest",
        Provider = new OAuthBearerAuthenticationProvider(),
        AccessTokenFormat = new JwtFormat (
            allowedAudience: "http://mysimpletest/",
            issuerCredentialProvider: new SymmetricKeyIssuerSecurityTokenProvider (
                issuer: "http://identityserver.v2.thinktecture.com/trust/changethis",
                base64Key: "iGROpg/Q+8oFSihQKJ6+D6Vsu+GOyfEC4qcGKzY4qNQ="
            )
        )
    }
);
```

The code above registers the OAuth Bearer Authentication OWIN module on the IAppBuilder passed into the startup-class for ASP.NET OWIN. It specifies the following parameters:

* the realm for which it expects the token to be issued from the auth-server
* the authentication provider, which is OAuthBearer as we expect a bearer token
* the token access format with a token provider used for validating the token

In the sample above we use the SymmetricKeyIssuerTokenProvider which means the authentication server should use the symmetric key specified in the code above to validate the token.

To understand these values better, it is now time to look at the configuration of the Authentication Server. For implementing my sample I used the ThinkTecture Identity Server which (still) supports OAuth implicit grant (note that this functionality is expected to be moved to the ThinkTecture Authorization server – see their homepage for further details). Since in my sample I am using a Windows 8 Store App as a client to call my web API, I also need to register that store app as a valid client with the authentication server as shown below:

![Browser Redirects Explained](/images/posts2013/20131220 - ASP.NET 4.5.1 and OAuth2 (2).png)

As you can see in this picture, the Web API symmetric key and the realm do match what we have used in the code, above. The configured redirect-URL is not used since we’re accessing the WebAPI from a store app. The store APP is registered as a client as you can see from the second screen.

In the Windows 8 Store App I am using the [WebAuthenticationBroker](https://archive.is/o/VsJuo/msdn.microsoft.com/en-us/library/windows/apps/windows.security.authentication.web.webauthenticationbroker.aspx) from WinRT to kick-off the authentication flow. The WebAuthenticationBroker ecnapsulates a browser-control that performs the required process as shown in the first image of this blog post including all browser redirects. The process stops when that hosted browser control tries to post to the redirect URI entered in the client configuration as shown in the Thinktecture Identity Server screen-shot above.

In case of a Windows 8 app that “redirect URL” is not a physical URL, but it is something the browser-control hosted in the WebAuthenticationBroker will intercept. Many authentication servers use that URL with the access token provided as a query string parameter as shown below:

ms-app://s-1-15-2-210097793-3234850793-3288079826-3013899094-2116512904-1639073252-2090501734/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOi8vaWRlbnRpdHlzZXJ2ZXIudjIudGhpbmt0ZWN0dXJlLmNvbS90cnVzdC9jaGFuZ2V0aGlzIiwiYXVkIjoiaHR0cDovL215c2ltcGxldGVzdC8iLCJuYmYiOjEzODcxODc2ODMsImV4cCI6MTM4NzIyMzY4MywibmFtZWlkIjoidGVzdCIsInVuaXF1ZV9uYW1lIjoidGVzdCIsImF1dGhtZXRob2QiOiJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvYXV0aGVudGljYXRpb25tZXRob2QvcGFzc3dvcmQiLCJhdXRoX3RpbWUiOiIyMDEzLTEyLTE2VDA5OjU0OjQwLjM3N1oiLCJlbWFpbCI6InRlc3RAdGVzdGluZy5jb20ifQ.SmXI42aNXy6bwk8plObHK7vHUndqXDh1xsd14-PCPt0&token_type=urn:ietf:params:oauth:token-type:jwt&expires_in=35998

The WebAuthenticationBroker just waits for the hosted browser control to try to post to this URL, intercepts that process and then returns to your code so that you can extract the access token out of that:

```csharp
string thinktectureUrl = "https://mszspro8/idsrv/issue/oauth2/authorize";
string webApiUriForOauth = "http://mysimpletest/";
string thinktectureClientId = "testclient";

var endpoint = new Uri("https://mszw81-devvm/idsrv2/issue/oauth2/authorize");
var callbackUri = WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
            
var startUri = new Uri(
        string.Format(
        "{0}?client_id={1}&scope={2}&redirect_uri={3}&response_type=token",
            endpoint.AbsoluteUri,
            Uri.EscapeDataString(thinktectureClientId),
            Uri.EscapeDataString(webApiUriForOauth),
            callbackUri.AbsoluteUri));

var success = await WebAuthenticationBroker.AuthenticateAsync
                    (
                        WebAuthenticationOptions.None,
                        startUri
                    );

// Pick the token out of the response
if (success.ResponseStatus == WebAuthenticationStatus.Success)
{
    var parametersString = success.ResponseData.Substring(success.ResponseData.IndexOf("/#") + 2);
    var parameters = parametersString.Split('&');
    var token = parameters[0].Substring("access_token=".Length);

    // Now call the web API with the bearer header
    httpClient.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", token);

    var response = await httpClient.GetAsync("https://localhost:44304/api/values");

    await PrintWebAPISuccess(response);
}
else
{
    throw new Exception(success.ResponseData);
}
```

The code above is part of a Button-Click-Handler in a Windows Store App that calls the WebAPI. As you can see, the first thing it does is composing a URL to start the authentication process with as well as a redirect-URL to intercept for token posting (which is the ms-app:// URI retrieved with a utility function of the broker as follows: WebAuthenticationBroker.GetCurrentApplicationCallbackUri()).

The start-URL is one that is specified by the authentication server and the one used above is specific to the Thinktecture Identity Server. Windows Azure AD or other authentication servers will have a different start-URL.

But the process then is always the same – the WebAuthenticationBroker hosts a browser and starts kicking off the process with this start URL. And it stops processing by intercepting a post to the redirect / callback URI. 

After that process is completed, we take the response from the web authentication broker and parse the JWT-token out of the intercepted URL. Then we add the token to the HTTP header and call the web API. The following screen-shot shows that in action using the Thinktecture Identity Server from within our Windows Store App:

![Browser Redirects Explained](/images/posts2013/20131220 - ASP.NET 4.5.1 and OAuth2 (3).png)

The `.UseOAuthBearerAuthentication`-call we made in the WebAPI ensures that a token must be posted to the API so that it can be callend and it also sets the System.Threading.Thread.CurrentPrincipal so that we can query properties of the user contained in the token as follows:

```csharp
[Authorize]
public class ValuesController : ApiController
{
    // GET api/values
    public IEnumerable<string> Get()
    {
        // Claims APIs are abstract APIs that provide user information etc. independent of the Identity Provider used
        var claimsId = (ClaimsIdentity)System.Threading.Thread.CurrentPrincipal.Identity;
        foreach (var claim in claimsId.Claims)
        {
            // A claim is an attribute of a user, e.g. email, age/birthdate
            Debug.WriteLine(
                string.Format(
                    "-) {0} = {1}",
                        claim.Type,
                        claim.Value
                )
            );
        }

        return new string[] { "value1", "value2" };
    }
}
```

Please note the [Authorize]-attribute which ensures that the API cannot be called without a token.
The APIs used to query the attributes of a user are the “new Windows Identity Foundation” APIs which are now part of the .NET Framework. 

### Other OAuth OWIN Providers in ASP.NET 4.5.1

Now you might ask why we have other providers in the form of NuGet-packages if the Microsoft.Owin.Security.OAuth provider solves the problem in a general way. The answer is: simplicity!!! You have to admit that e.g. the following Code for Facebook or Windows Azure AD or Google is much simpler than the code above:

```csharp
new WindowsAzureActiveDirectoryBearerAuthenticationOptions
{
    Audience = ConfigurationManager.AppSettings["ida:Audience"],
    Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
});

app.UseGoogleAuthentication();

app.UseFacebookAuthentication(appId: "yourappid", appSecret: "yourappsecret");
```

All of these providers are doing nothing else but encapsulating what we’ve done manually using Microsoft.Owin.Security.Oauth in our WebAPI above. With Windows Azure AD and Windows Server AD / ADFS you have a few other neat libraries on the client (Active Directory Authentication Libraries (ADAL)) which do make things much simpler as outlined on Vittorio’s blog:

<http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/>

So stay tuned on more details, but I think the details above are exactly those which are not very well documented and I hope with this post I do fill that gap appropriately.

Merry Christmas!
