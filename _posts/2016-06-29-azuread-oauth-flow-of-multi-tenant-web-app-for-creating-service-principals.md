---
layout: post
title:  AzureAD - OAuth Flows for Multi-Tenant Web Applications that need to create Service Principals
date:   2016-06-29 12:00:00 +0100
categories: wordpressarchive
tags: Archive Azure Identity AzureActiveDirectory AzureAD OAuth .NET
excerpt_separator: <!--more-->
---

I am currently working with one if my main Global Independent Software Vendor (ISV) partners for on-boarding their solution into the Azure Marketplace. The main challenge that we face there is, that the solution needs to do some post-provisioning steps in the end-customer's target subscription as well as Azure Active Directory tenant:
* Creating a Service Principal that can be used by the Software inside of the provisioned VM in the end-customer's target directory.
* Using that service principal to read data from the end-customer's Azure Subscription.

Note: the end customer in this case is the customer, who purchases the product published by the ISV in the store!

Such cases typically require the creation of "multi-tenant" Azure Active Directory applications. And this application then needs to access the end-customer's target directory using the Azure AD Graph API. At the same time, creating service principals is not an easy task.

<!--more-->

### A Multi-Tenant Web App to create Service Principals as Sample
To make this as practical as possible, I decided to create a web app that creates service principals in the target Azure Active Directory of an end-customer that's using the web app.

This shows, how the general multi-tenancy challenge can be solved and at the same time provides a handy tool for creating Service Principals, which is a harder task on its own.

All the details for using the app and for cloning the source code are available on my GitHub-repository under the link below. In addition, I also run the app on my Azure Subscription as a free-tier Azure Web App.

* The app running: [https://mszcoolserviceprincipal.azurewebsites.net](<https://mszcoolserviceprincipal.azurewebsites.net>)
  * Note: when using __Microsoft Accounts (MSA)__, you need to open the site __"in-private"/"incognito"__ mode!
* Source code: [<https://github.com/mszcool/azureAdMultiTenantServicePrincipal>](<https://github.com/mszcool/azureAdMultiTenantServicePrincipal>)

* Documentation: [<https://github.com/mszcool/azureAdMultiTenantServicePrincipal/blob/master/README.md>](<https://github.com/mszcool/azureAdMultiTenantServicePrincipal/blob/master/README.md>)

The documentation shows, how-to register a multi-tenant application in your Azure AD tenant to make such an application available as a multi-tenant application. It shows, how such an application is reflected in a customer's target Azure AD tenant and how-to manage access to it. 

The sample also demonstrates the various OAuth- and OpenIdConnect-flows which are needed in a simple yet practical and useful scenario. All of this should be easy to reflect to your own scenarios and I found that, despite Microsoft has decent docs for Azure Active Directory out there, such a sample is not easy to find in an end-2-end and focused way. That's what I tried to create.

### The basic/initial OpenIdConnect-Flow for Signing-In

So, let's start with digging into the OAuth details. First of all, all the theory is well-explained on the official Microsoft Azure and MSDN documentation pages (see last section of the article). 

I just get down at the protocol-trace level so that it's easy for developers to understand what's going on and how simple those protocols are, indeed. Also it should help configuring/using other frameworks on all sorts of platforms appropriately to fit into this model.

For all of the below I am using the [real deployment](<https://mszcoolserviceprincipal.azurewebsites.net>) of my Service Principal Web App Demo mentioned above (note: I might remove that deployment at any point in time since I've guidance on my GitHub-repo for how-to deploy it in your own Azure AD tenant, as well).

1. First the user browses to the target application which is secured by Azure AD.

2. That typically ends up in a redirect to Azure AD as an IDP to get an initial token. A typical Redirect Request for an OAuth Sign-In flow looks as follows (using line-breaks to make it easier to read):

    ```
    GET https://login.microsoftonline.com/common/oauth2/authorize?
        client_id=---your client id from azure ad app registration---
        &response_mode=form_post
        &response_type=code+id_token
        &scope=openid+profile
        &state=OpenIdConnect.AuthenticationProperties%3dW1HmJdRTdYw...
        &redirect_uri=https%3a%2f%2flocalhost%3a44330%2f HTTP/1.1
    Host: login.microsoftonline.com
    Connection: keep-alive
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Accept-Encoding: gzip, deflate, sdch, br
    Accept-Language: en-US,en;q=0.8

    ```
    
    * The __client_id__-parameter reflects the Client ID that is configured in Azure Active Directory for that application.
    * The __scope__-parameter contains various additional items used for token validation.
    * The __nonce__ is used to protect against token replay attacks (typically). It's value provided in the request must match the response and is unique per user session, typically.
    
3. When the user (assume Admin) signs in for the first time, a consent dialog is displayed. This is part of the OAuth Authorization flow and gives the user a chance to "Accept" or decline the permissions the app needs. Since that is handled by Azure AD as an IdP, we don't look into the details of the requests issued there.

    ![Consent](https://raw.githubusercontent.com/mszcool/azureAdMultiTenantServicePrincipal/master/Docs/Usage-Figure01-Sign-In.png)
    
4. Once the user accepted this consent, Azure AD posts a token to a target URL which was specified in the earlier request with the __redirect_uri__ parameter. Let's look at the details (again with newlines for readability):

    ```
    POST https://localhost:44330/ HTTP/1.1
    Host: localhost:44330
    Connection: keep-alive
    Content-Length: 2428
    Cache-Control: max-age=0
    Origin: https://login.microsoftonline.com
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36
    Content-Type: application/x-www-form-urlencoded
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Referer: https://login.microsoftonline.com/common/Consent/Grant
    Accept-Encoding: gzip, deflate, br
    Accept-Language: en-US,en;q=0.8
    Cookie: OpenIdConnect.nonce.Z9f6E8u...
    
    code=AAABA...
    ```

   That post contains an OAuth-Authorization code in the body. This Code can be used to request tokens from Azure AD for downstream API-calls of APIs which are also secured by Azure AD. Of course, the code will only work for APIs to which the app has been given permissions in the Azure AD portal.
   
   ![Permissions of the App](https://raw.githubusercontent.com/mszcool/azureAdMultiTenantServicePrincipal/master/Docs/Figure02-App-Permissions.png)
   
   For the "Service Principal Demo App" those permissions are highlighted in the screen shot above. The Code therefore would work for requests of tokens for the Azure Active Directory Graph API (identified as https://graph.windows.net) and the Azure Service Management and Resource Manager APIs (identified as https://management.core.windows.net).
   
5. When the Service Principal Web App receives the request, it actually uses it to request an additional token that permits the app to call into Azure Active Directory Graph APIs. This is another token-request which the app tries to execute when it received the post above.

    ```
    POST https://login.microsoftonline.com/common/oauth2/token HTTP/1.1
    Accept: application/json
    x-client-last-request: a5db36d8-ab46-4dfc-b96e-9dc31cf06a5c
    x-client-last-response-time: 1284
    x-client-last-endpoint: token
    x-client-SKU: PCL.Desktop
    x-client-Ver: 3.10.0.0
    x-client-CPU: x64
    x-client-OS: Microsoft Windows NT 10.0.10586.0
    x-ms-PKeyAuth: 1.0
    client-request-id: 1eb9034c-e02c-4e7b-8c4f-0fe5e2faabfe
    return-client-request-id: true
    Content-Type: application/x-www-form-urlencoded
    Host: login.microsoftonline.com
    Content-Length: 1079
    Expect: 100-continue
    
    resource=https%3A%2F%2Fgraph.windows.net&client_id=---your client id from azure ad app registration---&client_secret=---your client secret configured in the azure ad portal&grant_type=authorization_code&code=---previously received authorization code---&redirect_uri=https%3A%2F%2Flocalhost%3A44330%2F
    ```

    Such a request would the respond with a new OAuth Bearer Token that would permit us to call into the Azure AD Graph APIs. This token needs to be added to the HTTP Authorize header on each request, then. Here's an example response for the request above:
    
    ```
    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    Server: Microsoft-IIS/8.5
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-request-id: fb2db119-ca9e-421d-8007-6ae7e97d163e
    client-request-id: 1eb9034c-e02c-4e7b-8c4f-0fe5e2faabfe
    x-ms-responsehealth: TargetId=ESTSFE_IN_329;Action=None;Category=None;Health=0;Load=9;
    P3P: CP="DSP CUR OTPi IND OTRi ONL FIN"
    Set-Cookie: esctx=AAABAA ...; domain=.login.microsoftonline.com; path=/; secure; HttpOnly
    Set-Cookie: x-ms-gateway-slice=productionb; path=/; secure; HttpOnly
    Set-Cookie: stsservicecookie=ests; path=/; secure; HttpOnly
    X-Powered-By: ASP.NET
    Date: Wed, 29 Jun 2016 21:31:37 GMT
    Content-Length: 3826
    
    {
      "token_type": "Bearer",
      "scope": "Directory.AccessAsUser.All Directory.ReadWrite.All Group.ReadWrite.All User.Read",
      "expires_in": "3599",
      "ext_expires_in": "3600",
      "expires_on": "1467239498",
      "not_before": "1467235598",
      "resource": "https://graph.windows.net",
      "access_token": "eyJ0eXAiOiJK...",
      "refresh_token": "AAABAAAAiL9Kn2..."
    }
    ```
    
    The response is a JSON-response containing some helpful details about the issued token as well as a refresh-token to renew the actual access token. Note: if you need to get a new access token with the refresh token, you still need to have the Client ID and the App Secret available in that refresh-request.
    
    
### OAuth Admin Consent for Multi-Tenant Azure AD Apps

Yikes, the biggest challenge I faced with the tool when building it was, that ordinary Azure AD Users (role = 'User') where not able to use it. You had to be a 'Global Admin' to execute it. 

The main reason for that was, that my app requires "acting as the Signed-in User" against Azure AD Graph API. And for that, the Azure AD team changed the default behavior for a good reason a while ago (well, in March 2015): <https://blogs.msdn.microsoft.com/aadgraphteam/2015/03/18/update-to-graph-api-consent-permissions/>.

So, to enable ordinary users to make use of such applications, a Global Admin first needs to "approve" the application for the target directory by running through an OAuth Admin Consent. This is a special type of consent that asks the Global Admin if he wants to make the permissions the App requires available to ordinary users inside of the Organization (technically: in the target directory against the multi-tenant app tries to work depending on the signed-in user).

The steps are:

1. The Global Admin needs to Sign-in into the application.

2. The application needs to provide the appropriate "on-boarding"-function, which essentially initiates the Admin-Consent against the target directory of the signed-in user. I did this by just adding a button to my app that starts the Admin Consent.

   ![Admin Consent Function](https://raw.githubusercontent.com/mszcool/azureAdMultiTenantServicePrincipal/master/Docs/Usage-Figure05-AdminConsent1.png)
   
3. All that button does is composing a URL that goes against the Azure Active Directory OAuth endpoints to walk through the Admin Consent. This leads to the following request that initiates the Admin Consent:

    ```
    GET https://login.windows.net/yourazureadtenantid/oauth2/authorize?
        api-version=1.0
        &response_type=code
        &client_id=yourazureadappid
        &resource=https://management.core.windows.net/
        &redirect_uri%20=https://mszcoolserviceprincipal.azurewebsites.net/Home/CatchConsentResult
        &prompt=admin_consent 
        HTTP/1.1
    Host: login.windows.net
    Connection: keep-alive
    Cache-Control: max-age=0
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
    Referer: https://mszcoolserviceprincipal.azurewebsites.net/
    Accept-Encoding: gzip, deflate, sdch, br
    Accept-Language: en-US,en;q=0.8    
    ```
    
    The __really important__ aspect of that request is the query-string parameter __prompt=admin_consent__ which does the work. I combine the request with issuing an authorization code right away, but I do think that's optional (would need to read back in the specs:)).
    
4. After that initial admin-consent is completed, every other ordinary user (role = 'user') can sign-in to the application and make use of it. The admin-consent literally approved the application through an administrator for the organization for security reasons.
    
### The Graph API calls with the issued tokens

Finally, with that Access Token we can make calls into the Azure AD Graph API. The sample-calls for creating a Service Principal are similar to the following types of requests.

1. First, the app tries to find if the needed "Application" for the Service Principal has been created in Azure AD, already:
  
      ```
      GET https://graph.windows.net/yourazureadtenantid/applications()?$filter=identifierUris/any(iduri:iduri%20eq%20'http%3A%2F%2Fyourappidurienteredinthescreen')&api-version=1.6 HTTP/1.1
      DataServiceVersion: 3.0;NetFx
      MaxDataServiceVersion: 3.0;NetFx
      Accept: application/json;odata=minimalmetadata
      Accept-Charset: UTF-8
      DataServiceUrlConventions: KeyAsSegment
      User-Agent: Microsoft Azure Graph Client Library 2.1.1
      Authorization: Bearer eyJ0eXAiOiJK...
      X-ClientService-ClientTag: Office 365 API Tools 1.1.0612
      Host: graph.windows.net
      Connection: Keep-Alive

      ```
      
      The request above looks, if an Application is registered in the target tenant __yourazureadtenantid__ with the App ID URI __http://yourappidurienteredinthescreen__. The HTTP-response will have an OData-based JSON with the resulting elements in it if an App exists, already.
      
      ```
      {
        "odata.metadata": "https://graph.windows.net/yourazureadtenantid/$metadata#directoryObjects/Microsoft.DirectoryServices.Application",
        "value":[
          ...
        ]
      }
      ```
    
2. If no application exists, it actually creates the application by posting an ApplicationEntity into the Graph API:

   ```
   POST https://graph.windows.net/yourazureadtenantid/applications?api-version=1.6 HTTP/1.1
   DataServiceVersion: 3.0;NetFx
   MaxDataServiceVersion: 3.0;NetFx
   Content-Type: application/json;odata=minimalmetadata
   Accept: application/json;odata=minimalmetadata
   Accept-Charset: UTF-8
   DataServiceUrlConventions: KeyAsSegment
   User-Agent: Microsoft Azure Graph Client Library 2.1.1
   Authorization: Bearer eyJ0eXAiOiJKV1...
   X-ClientService-ClientTag: Office 365 API Tools 1.1.0612
   Host: graph.windows.net
   Content-Length: 201
   Expect: 100-continue
    
   {
    "odata.type": "Microsoft.DirectoryServices.Application",
    "displayName": "YourAppDisplayName",
    "identifierUris@odata.type": "Collection(Edm.String)",
    "identifierUris": [
        "http://YourAppIdUri"
    ]
   }
   ```

   This post will return with a detailed JSON object which contains all the details about the created App including it's AppId.

3. Then the application does the same for checking if a Service Principal exists for the Application previously created, already.

   ```
   GET https://graph.windows.net/yourazureadtenantid/servicePrincipals()?$filter=appId%20eq%20'b3ccae52-19bc-45a1-a4e4-f572f6963213'&api-version=1.6 HTTP/1.1
   DataServiceVersion: 1.0;NetFx
   MaxDataServiceVersion: 3.0;NetFx
   Accept: application/json;odata=minimalmetadata
   Accept-Charset: UTF-8
   DataServiceUrlConventions: KeyAsSegment
   User-Agent: Microsoft Azure Graph Client Library 2.1.1
   Authorization: Bearer eyJ0eXAiOiJKV1...
   X-ClientService-ClientTag: Office 365 API Tools 1.1.0612
   Host: graph.windows.net
   ```

   The response will again contain an OData JSON document with the service principal if it exists, already. I am skipping the details for now...
   
4. Finally, if the Service Principal does not exist, the app creates one with a password credential attached to it. That means this principal can be used by service- and backend-applications.

   ```
   POST https://graph.windows.net/yourazureadtenantid/servicePrincipals?api-version=1.6 HTTP/1.1
   DataServiceVersion: 3.0;NetFx
   MaxDataServiceVersion: 3.0;NetFx
   Content-Type: application/json;odata=minimalmetadata
   Accept: application/json;odata=minimalmetadata
   Accept-Charset: UTF-8
   DataServiceUrlConventions: KeyAsSegment
   User-Agent: Microsoft Azure Graph Client Library 2.1.1
   Authorization: Bearer eyJ0eXAiOiJKV1...
   X-ClientService-ClientTag: Office 365 API Tools 1.1.0612
   Host: graph.windows.net
   Content-Length: 627
   Expect: 100-continue
    
   {
    "odata.type": "Microsoft.DirectoryServices.ServicePrincipal",
    "accountEnabled": true,
    "appId": "b3ccae52-19bc-45a1-a4e4-f572f6963213",
    "displayName": "tttttteeeeeeeessssstttt",
    "passwordCredentials@odata.type": "Collection(Microsoft.DirectoryServices.PasswordCredential)",
    "passwordCredentials": [
        {
            "customKeyIdentifier": null,
            "endDate": "2017-06-29T21:43:15.6654372Z",
            "keyId": "0259571d-a663-4507-94e9-9381629e2116",
            "startDate": "2016-06-29T21:43:15.6639533Z",
            "value": "pass@word1"
        }
    ],
    "servicePrincipalNames@odata.type": "Collection(Edm.String)",
    "servicePrincipalNames": [
        "b3ccae52-19bc-45a1-a4e4-f572f6963213",
        "http://tttttteeeeeeeessssstttt"
    ]
   }
   ```

__Note:__ One piece missing is to assign appropriate roles for executing on Service Management Operations for Azure Resource Manager Rolebased Access Control so that the Service Principal can execute the needed operations against the management APIs.

### Do you really need to know all of these details?

With that we went through all the protocol details for the OAuth, OpenIdConnect and Graph API calls that are needed to accomplish an end-2-end task. It's actually a very practical look at how all these "sequence diagrams" that are talking about OAuth are looking in the real world.

My intent to show these details was, to help people which are working with programming languages and runtimes that do not have nice SDKs available for encapsulating those protocol details to at least have a high-level overview and starting-point without reading the OAuth and OpenIdConnect specs. I know it's high-level, but it's practical. 

### OAuth and OpenId Connect Azure AD Resources

The following links do explain all the different query string parameters of the OAuth/OpenIdConnect flows with Azure AD. They are a great resource to better understand the http-requests I've outlined above.

* <https://msdn.microsoft.com/en-us/library/azure/dn645542.aspx>
* <https://msdn.microsoft.com/en-us/library/azure/dn645541.aspx>
* <http://openid.net/specs/openid-connect-core-1_0.html>
* <http://oauth.net/2/> 
* <http://tools.ietf.org/html/rfc6749>

### SDKs for languages and Runtimes

Fortunately, if you are a .NET, Java, Node.js, PHP or Python developer, there are numerous examples and resources available. Also for Azure AD's Graph API there's a nice tool available to dig into all the JSON and protocol details.

Here are the most important links:
* Azure AD Authentication Library for Java
  * <https://azure.microsoft.com/en-us/documentation/articles/active-directory-devquickstarts-webapp-java/>
  * <https://github.com/AzureAD/azure-activedirectory-library-for-java>
  * <https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect>
* Azure AD Authentication Library for .NET
  * <https://azure.microsoft.com/en-us/documentation/articles/active-directory-devquickstarts-dotnet/>
  * <https://github.com/AzureAD/azure-activedirectory-library-for-dotnet>
  * <https://blogs.technet.microsoft.com/enterprisemobility/2016/05/18/adal-net-v3-reaches-ga/>
* Azure AD Authentication Library for JavaScript
  * <https://azure.microsoft.com/en-us/documentation/articles/active-directory-devquickstarts-angular/>
  * <http://www.cloudidentity.com/blog/2014/10/28/adal-javascript-and-angularjs-deep-dive/>
  * <https://github.com/AzureAD/azure-activedirectory-library-for-js>
* Azure AD Node.js Integration Sample
  * <https://azure.microsoft.com/en-us/documentation/articles/active-directory-devquickstarts-webapi-nodejs/>
  * <https://azure.microsoft.com/en-us/documentation/articles/active-directory-devquickstarts-openidconnect-nodejs/>

For Graph API there are also good samples and SDKs out there:
* Graph API and Java:
  * <https://github.com/Azure-Samples/active-directory-java-graphapi-web>
  * <https://github.com/AzureAD/azure-activedirectory-library-for-java>
  * <https://azure.microsoft.com/en-us/documentation/samples/active-directory-java-graphapi-web/>
* Graph API and .NET:
  * <https://raw.githubusercontent.com/mszcool/azureAdMultiTenantServicePrincipal> 
  * <https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web>
  * <https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient/>
* Graph API for JavaScript and Node.js
  * <https://www.npmjs.com/package/azure-graphapi>
* Azure AD Graph API Explorer (good for learning and manually crafting requests)
  * <http://graphexplorer.cloudapp.net/>

I hope that was helpful and gives you a great background or even a handy tool to create Service Principals. My partner needed the understanding of how-to build such multi-tenant Azure AD applications that do access the Azure AD Graph API and they needed to create Service Principals out of such a multi-tenant web application. So I thought it's worth spending the additional time and getting it documented!


