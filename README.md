# Create a simple eSignature solution on Power Apps V2

By Martin Boejstrup, Microsoft 3/2/2022

### Background

Sometimes you need a signature solution in Power Apps. Out of the box there is
two methods: Use the pen input control or use the 3. Part Digital signature
solution.

This solution uses a sign in request as an electronic signature, as a no/low
code using build-in features.  
![Diagram Description automatically
generated](media/02050747c2a56c46335ad9c17c5758f6.png)

The solution the standard “Office 365 login” to validate the user, send the
request back to the Power App. The app uses a cloud flow to call Microsoft Graph
to check which user did the login to ensure the login was done by the same user.

There are 4 elements:

-   Azure Ad App Registration: is used to be able to connect to Microsoft Graph.

-   Power Automate Cloud flow: is used to get the request from the app and send
    the sign in request to Microsoft Graph, and respond the result to the app.

-   Power Apps Canvas app: The user use the app and click resign. The app save
    the data and redirect the user the Office 365 login service. After the login
    the request is redirected back to the app, it load the data and call the
    cloud flow to validate the user that did the login.

-   Dataverse Table: is used to store the data and session id

# Import the sample solution

1.  Download the “eSignature with sign-in.zip” solution file and import it  
    ![Graphical user interface, application Description automatically
    generated](media/b6d8f65073a11d5b942320b9d94bfc0f.png)

2.  After import open the solution

3.  Open Settings of the Canvas App  
    ![Graphical user interface, application, Word Description automatically
    generated](media/fbe591b232c167ccb2197ce6b87a6777.png)

4.  Get the web link of your app  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/443c14cf480d20647c34efc3e05ecf4f.png)

Note: you can use the web link with or without the ?tenantid=xxxxx

# Create Application Registration in Azure AD

To make the Graph call we need an application key and a secret that have access
to sign in as the user. You need a Tenant Administrator to do that.

1.  Open Azure AD: <https://aad.portal.azure.com/>

2.  Click “App Registration” and “New application registration”  
    ![Graphical user interface, application Description automatically
    generated](media/42f16be3bb23416907fbef3a80dcd991.png)

3.  Add a name and click “Create”  
    ![A picture containing text Description automatically
    generated](media/e1b6d9e2f414dbf8c154c8934093ba85.png)

4.  Note down the Application Id and tenant id– we need that in the flow  
    ![Graphical user interface, text, application Description automatically
    generated](media/aa3edcc31639663cdebee21d9fd45bea.png)

5.  Click “Certificates & secrets” and “+ New client secret”  
    ![Graphical user interface, application Description automatically
    generated](media/a1e54a27541f6919fddb013bbcad257c.png)

6.  Add a description (can be any name), set the expirations, and click “Add”  
    ![Graphical user interface, application Description automatically
    generated](media/5839af04efd296f0a553f272899d5409.png)

7.  After it is saved the value of the key will shop up – note is down, we need
    it in the cloud flow  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/8261e1586670594996823187d4621b9f.png)

8.  Click “API permisions” and click “Grand admin consent”  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/a2377db9b4d88e5e6f0bfa21bf718321.png)

9.  Click “Yes”  
    ![Graphical user interface, text, application Description automatically
    generated](media/3c839652dc093d937e0c498c526c05b5.png)

10. Ensure the persissions  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/b902e853b280096020ab71ebd577c3fa.png)

11. In Overview click “Redirect URIs”  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/782ff22330ccc1754b1cdc9d211d22d6.png)

12. Click “Add a platform” and select “Web”  
    ![Graphical user interface, application, Word Description automatically
    generated](media/ce03699f8876f35807efdd55269efaf5.png)

13. Add the URL og the Power Apps  
    ![Graphical user interface, text, application Description automatically
    generated with medium
    confidence](media/0c221778ee4507365af495012ed67dcd.png)

14. Click “Configure”

15. Now you are done with the App registration.

# Update the cloud flow

1.  Open the “CheckTokenCode” cloud flow  
    ![Graphical user interface, application, Teams Description automatically
    generated](media/fbac37fd7fcc99e78698d042095fec4a.png)

2.  Update TenantID, ClientID, ClientSecret and Web url from the Canvas app  
    ![Graphical user interface, application, Teams Description automatically
    generated](media/a58db449e404d9133e2dc0cd0ba574ad.png)

3.  Save the flow and close it.

# Test the solution

1.  Open the web URL from the canvas app  
    ![Graphical user interface, application Description automatically
    generated](media/245872a4d2c7e39a550da24d2408f81a.png)

    1.  Enter sample data I AnswerA and B

    2.  Click “reSigin”

    3.  Enter Password and Sign in  
        ![Graphical user interface, application Description automatically
        generated](media/4fbfe6462c0f60a6327e4f430c56d3d5.png)

    4.  See that the data is loaded and the user email is validated  
        ![Graphical user interface, text Description automatically
        generated](media/7a7b4b4a7f0e743fe1198222f7fd1329.png)
