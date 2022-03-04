# Description of the login request

In the eSignature solution we make at launch command to call the login request,
and this is the description on that request.

GET request to the Office 365 login service, example:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ HTTP GET
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=23f97599-2ed4-4514-82f7-2a7958de68cf 
&response_type=code
&redirect_uri=https://apps.powerapps.com/play/e4a1565e-7a59-421c-957c-5b0c5662fa10
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=123455667788
&prompt=login
&login_hint=user@contoso.com
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| Parameter     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | The {tenant} value in the path of the request can be used to control who can sign into the application. Valid values are common, organizations, consumers, and tenant identifiers. For guest scenarios where you sign a user from one tenant into another tenant, you must provide the tenant identifier to sign them into the resource tenant. For more information, see [Endpoints](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-protocols#endpoints). |
| client_id     | The Application (client) ID that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app.                                                                                                                                                                                                                                                                                                                                       |
| response_type | Must include code for the authorization code flow. Will ensure that the token code is returned.                                                                                                                                                                                                                                                                                                                                                                                                 |
| redirect_uri  | The redirect_uri of your app, where authentication responses can be sent and received by your app. This is the web link for the Power App                                                                                                                                                                                                                                                                                                                                                       |
| response_mode | query: Default when requesting an access token. Provides the code as a query string parameter on your redirect URI.                                                                                                                                                                                                                                                                                                                                                                             |
| state         | A value included in the request that is also returned in the token response. It can be a string of any content that you wish. I this solution we use state to store the record id.                                                                                                                                                                                                                                                                                                              |
| prompt        | prompt=login forces the user to enter their credentials on that request, negating single-sign on.                                                                                                                                                                                                                                                                                                                                                                                               |
| login_hint    | You can use this parameter to pre-fill the username and email address field of the sign-in page for the user.                                                                                                                                                                                                                                                                                                                                                                                   |

Sample redirect request:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ HTTP
// Line breaks for legibility only

https://apps.powerapps.com/play/d4c83750-0ffa-4ec6-9a7d-9a1b296e18d5?
code=0.AToA11n0GJ4ERUaFZ9DGXu70Lpl1-SPULhRFgvcqeVjeaM86ABI.AQABAAIAAAD--DLA3VO7QrddgJg7WevrS55rPZy8LidYmj3NcvqPOU1Bx8A7N-weH8ixtliXDXpevJrOmbLUBKtCHGWU9u8RNTfVJw3afp5dPpbrQel7-ZcCV3M1o7chPIgbW002B4pD4WBh9_Nevo6ZY6f1039JGEx1-dueEI2NDaR2X0ly-jzgdP0MVKkleVa0Tc6fj4l4NdFFvnDA5n4R9UDVeDBvDGrU0cPXmoQ4F0ldbOxHLdKRr3G-t-UwYkFnpmJIji-z34UGVnCF1SwfvsBWPoAKp6yg5GHMNAAYDBK6uoPw1Lm2cYZJf2YJecBsOvYbU1xEk6mOJAnMYQP4ZAoF_u63I3V3Y-eZlKzKOmYOnFQrKn2hj96AZdsiqhRvcLTyJChWxbgAz6zo3gw0QxliZ4iyzNtTWeI1Kmlm3QzQaawTlaytHz9SR5uUEfL5-RDsErDQcXtJbcP1VTTx6kS_qdK8zMvVTxnulDxtc8_TNn2jGgJUyd5C3C9rVx-4msyaDv97NEidooENhJr-4qiDC8C33N4pcmLbB-JPvW8eCD6hB8hVJ4TKJPVtNKH1sz8VNsS-W-xNWTLrCwAzBUCd2hn8dbr7DAG4Dv6P1ZE2GxTGv0vJzA8uB6-4CL8VqbxjSq2cqaWDfaXJIk5zkOdQbTrnIAA&state=5b6cb125-b465-403e-a391-09f073c94231
&session_state=31c2f646-dd55-4e13-839e-a1de4fa9284a
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The “code” is the token code, we can use this to validate the user.

The “session_state” is returning the same state string we send to the login
request.

# To validate the user – make at Microsoft Graph request

In the solution this I done by the cloud flow “CheckTokenCode”, but here is the
background.

First we need to get a access token to Microsoft Graph using the token code:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ HTTP POST
https://login.microsoftonline.com/{tenant}/oauth2/token

// Line breaks for legibility only

grant_type=authorization_code
&redirect_uri=https://apps.powerapps.com/play/d4c83750-0ffa-4ec6-9a7d-9a1b296e18d5
&client_id=23f97599-2ed4-4514-82f7-2a7958de68cf
&client_secret=8Rr7Q~bFM93ddddpYesOXI5U9K-lShrLY98L5zm
&responce_type=token
&scope=user.read
&code=0.AToA11n0GJ4ERUaFZ9DGXu70Lpl1-SPULhRFgvcqeVjeaM86ABI.AQABAAIAAAD--DLA3VO7QrddgJg7WevrS55rPZy8LidYmj3NcvqPOU1Bx8A7N-weH8ixtliXDXpevJrOmbLUBKtCHGWU9u8RNTfVJw3afp5dPpbrQel7-ZcCV3M1o7chPIgbW002B4pD4WBh9_Nevo6ZY6f1039JGEx1-dueEI2NDaR2X0ly-jzgdP0MVKkleVa0Tc6fj4l4NdFFvnDA5n4R9UDVeDBvDGrU0cPXmoQ4F0ldbOxHLdKRr3G-t-UwYkFnpmJIji-z34UGVnCF1SwfvsBWPoAKp6yg5GHMNAAYDBK6uoPw1Lm2cYZJf2YJecBsOvYbU1xEk6mOJAnMYQP4ZAoF_u63I3V3Y-eZlKzKOmYOnFQrKn2hj96AZdsiqhRvcLTyJChWxbgAz6zo3gw0QxliZ4iyzNtTWeI1Kmlm3QzQaawTlaytHz9SR5uUEfL5-RDsErDQcXtJbcP1VTTx6kS_qdK8zMvVTxnulDxtc8_TNn2jGgJUyd5C3C9rVx-4msyaDv97NEidooENhJr-4qiDC8C33N4pcmLbB-JPvW8eCD6hB8hVJ4TKJPVtNKH1sz8VNsS-W-xNWTLrCwAzBUCd2hn8dbr7DAG4Dv6P1ZE2GxTGv0vJzA8uB6-4CL8VqbxjSq2cqaWDfaXJIk5zkOdQbTrnIAA
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This will return an access token we can use the make the Microsoft Grap call:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ HTTP GET
https://graph.microsoft.com/v1.0/me

{
  "Authorization": "{access token}"
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This request will return all the basic info:

Sample

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
"@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users/$entity",
  "businessPhones": [],
  "displayName": "PowerPlatform Administrator",
  "givenName": "PowerPlatform",
  "jobTitle": "Power dude",
  "mail": "poweradmin@MABOEJTest.onmicrosoft.com",
  "mobilePhone": null,
  "officeLocation": null,
  "preferredLanguage": "en-US",
  "surname": "Administrator",
  "userPrincipalName": "poweradmin@MABOEJTest.onmicrosoft.com",
  "id": "a8d122ba-d7ba-4e90-b1a2-02e2d7dd578a26"
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Link to the Microsoft Docs : [Microsoft identity platform and OAuth 2.0
authorization code flow - Microsoft identity platform \| Microsoft
Docs](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)
