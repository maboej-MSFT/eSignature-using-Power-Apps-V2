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

-   Azure AD App Registration: is used to be able to connect to Microsoft Graph.

-   Power Automate Cloud flow: is used to get the request from the app and send
    the sign in request to Microsoft Graph, and respond the result to the app.

-   Power Apps Canvas app: The user uses the app and click resign. The app saves
    the data and redirect the user the Office 365 login service. After the login
    the request is redirected back to the app, it loads the data and call the
    cloud flow to validate the user that did the login.

-   Dataverse Table is used to store the data and session id. But you can use
    whatever table to store the data and you can use another id for the session
    id, can be the unique id from the table record.

# Create Application Registration in Azure AD

To make the Graph call we need an application key and a secret that have access
to sign in as the user. You can use the same App Registration for many apps.

*You need a Tenant Administrator to do that.*

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

8.  Click “API permissions” and click “Grand admin consent”  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/a2377db9b4d88e5e6f0bfa21bf718321.png)

9.  Click “Yes”  
    ![Graphical user interface, text, application Description automatically
    generated](media/3c839652dc093d937e0c498c526c05b5.png)

10. Ensure the permissions  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/b902e853b280096020ab71ebd577c3fa.png)

11. You need to add WEB URL later in this guide.

# 

# Import the sample solution

1.  Download the “eSignaturewithSignin_x_x_x_x.zip” solution file and import it

2.  ![Graphical user interface, application Description automatically
    generated](media/b6d8f65073a11d5b942320b9d94bfc0f.png)

3.  Update the environments variable and click “Import”  
    ![Graphical user interface, application Description automatically
    generated](media/89535a7b49b1f83926d2fb2bc98728c0.png)

4.  After import open the solution

5.  Open Settings of the Canvas App  
    ![Graphical user interface, application, Word Description automatically
    generated](media/fbe591b232c167ccb2197ce6b87a6777.png)

6.  Get the web link of your app (without the ?tenantid), and App ID and save
    them for later.  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/4cc5faf4999bda9c31cb5e8347fdec41.png)

7.  Update the “eSignaturePowerAppID” environment variable:  
    ![Background pattern, rectangle Description automatically generated with
    medium confidence](media/de8e3ad35d60ef6c67f9120a038f40b6.png)

8.  Replace the current value with the appid you just saved  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/64f4bb6f4f00d8b875cd9725c7726184.png)

9.  Save the variable.

# Update the Application Registration in Azure AD

We need to add the app URL to the app registration, to be able to do the
redirect back to the app. There can be multiple URLs for many apps.

1.  In Overview click “Redirect URIs”  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/782ff22330ccc1754b1cdc9d211d22d6.png)

    1.  Click “Add a platform” and select “Web”  
        ![Graphical user interface, application, Word Description automatically
        generated](media/ce03699f8876f35807efdd55269efaf5.png)

    2.  Add the URL og the Web link to the app  
        ![Graphical user interface, text, application Description automatically
        generated with medium
        confidence](media/0c221778ee4507365af495012ed67dcd.png)

    3.  Click “Configure”

    4.  Now you are done with the App registration.

Note: there can be many webs added to the same App Registration.

# 

# Update the cloud flow

The cloud flow needs to bed turn off and on again to register the updated
environment variables.

1.  Turn the cloud flow “CheckTokenCode” off  
    ![Graphical user interface, application, Teams Description automatically
    generated](media/171982ad9473077952fc7d29b6fe413d.png)

2.  Turn the cloud flow “CheckTokenCode” on  
    ![Graphical user interface, application Description automatically
    generated](media/0839b2349ac07994ec7f009fb0a51b7a.png)

# Test the solution

1.  Open the web URL from the canvas app  
    ![Graphical user interface, application Description automatically
    generated](media/245872a4d2c7e39a550da24d2408f81a.png)

    1.  Enter sample data I AnswerA and B

    2.  Click “reSignin”

    3.  Enter Password and Sign in  
        ![Graphical user interface, application Description automatically
        generated](media/4fbfe6462c0f60a6327e4f430c56d3d5.png)

    4.  See that the data is loaded, and the user email is validated  
        ![Graphical user interface, text Description automatically
        generated](media/7a7b4b4a7f0e743fe1198222f7fd1329.png)
