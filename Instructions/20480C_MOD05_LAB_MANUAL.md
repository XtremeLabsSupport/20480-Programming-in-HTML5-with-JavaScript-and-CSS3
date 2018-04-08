## Module 5: Communicating with a Remote Server

## Lab: Communicating with a Remote Data Source

#### Scenario

You have been asked modify the Schedule page for the ContosoConf website. Previously, the session data was provided as a hard-coded array of data and the JavaScript code for the page displayed the data from this array. However, session information is not static; it may be updated at any time by the conference organizers and stored in a database. A web service is available that can retrieve the data from this database, and you decide to update the code for the Schedule page to use this web service rather than the hard-coded data currently embedded in the application.

In addition, the conference organizers have asked if it is possible for conference attendees to be able to indicate which sessions they would like to attend. This will enable the conference organizers to schedule popular sessions in larger rooms. The Schedule page has been enhanced to display star icons next to each session. An attendee can click a star icon to register their interest in that session. This information must be recorded in a database on the server, and you send this information to another web service that updates the corresponding data in the database.

A session may be very popular, so the web service will return the number of attendees who have selected it. You will need to handle this response and display a message to the attendee when they have selected a potentially busy session.

#### Objectives

After completing this lab, you will be able to:
1.	Write JavaScript code to retrieve data from a remote data source.
2.	Write JavaScript code to send data to a remote data source.
3.	Use the jQuery **ajax** method to simplify code that performs remote communications.

#### Lab Setup

Estimated Time: **60 minutes**

### Exercise 1: Retrieving Data

#### Scenario

In this exercise, you will retrieve and display the list of sessions from a web service.

First, you will create a function that constructs an HTTP request to get session data from a remote data source running on a web server. The function will send the request asynchronously, and you will define a callback function that receives the session data when the web service replies. The session data will be a JSON string that requires parsing into a JavaScript object. You will use the existing **displaySchedule** function to display the sessions on the page.

Network connections to remote sources and web servers are not totally reliable. Therefore, you will need to make your code robust enough to handle errors that can occur when receiving data. For testing purposes, a version of the web service that generates errors is also available, and you will use this web service to verify the error handling capabilities of your code.

Finally, you will run the application and view the Schedule page to verify that it displays the list of sessions correctly, and also that it correctly handles errors.

#### Task 1: Review the Schedule page.

1.	Start Visual Studio and open the **ContosoConf.sln** solution in the **Allfiles\Mod05\Labfiles\Starter\Exercise 1** folder.
2.	In the **ContosoConf** project, open the **scripts\pages\schedule.js** file. 
3.	Review the JavaScript code. 
- Notice that the previously hard-coded array of session data (in the **schedule** variable) has been replaced with an empty array.
- Also notice that the **createSessionElement** function has been modified to generate a star icon next to the session title.

>**Note:** The star icon is an **&lt;a&gt;** link element that is styled with a background image in the >shape of a star. To do this, the **&lt;a&gt;** element has the **class** attribute set to **star**, and the **schedule.css** file in the **styles\pages** folder contains the following style:
>    ```javascript
>        .star {
>            display: inline-block;
>            width: 15px;
>            height: 15px;
>            cursor: pointer;
>            background-image: url(../images/stars.png);
>            background-position: 0 0;
>        }
>    ```

#### Task 2: Create the downloadSchedule function.

1.	In the **schedule.js** file, find the comment that starts with the following text:
    ```javascript
        // TODO: Create a function called downloadSchedule
    ```
2.	Create an empty function named **downloadSchedule**. You will add code to this function in subsequent tasks to asynchronously get a list of sessions from a web service.
3.	Within this function define a variable named **request** and assign it a new **XMLHttpRequest** object.
4.	In the **downloadSchedule** function, use the **request** object to asynchronously retrieve data from the URL **/schedule/list** by performing a GET operation. 
- Call the **open()** method of the **request** object.
5.	After the previous statement, create an empty callback that handles the response from the web service and attach it to the **onreadystatechange** property of the **request** object.
6.	In the callback, perform the following operations:
- Ensure that the **readyState** property of the request object indicates the response is complete (it should have the value **4**).
- Parse the JSON string in the response into an object and assign this object to the **schedule** variable.
- Call the **displaySchedule** function to display the sessions on the page.
7.	After you have created the callback, add a statement to send the request to the server
- Use the **send()** method of the **request** object.
8.	Add a statement to the end of the **schedule.js** file that calls the **downloadSchedule** function after initializing the event handlers for the check boxes and list on the Schedule page. 

#### Task 3: Add error handling to the downloadSchedule function.

1.	In the **onreadystatechange** callback function, after receiving the response (check that the **readyState** of the **request** object is set to 4), add code to test the request status code. 

For non-200 status codes, the **response** object contains a **message** property. Display this message by using the built-in JavaScript **alert** function.
2.	Also, handle the exceptional case of the remote data source returning invalid JSON data that cannot be parsed. In this case, display a general error message using the **alert** function.

#### Task 4: Test the Schedule page.

1.	Run the application and view the **schedule.htm** page to verify that the list of sessions is displayed.
2.	Close Microsoft Edge.
3.	In the **schedule.js** file, change the requested URL to **/schedule/list?fail**, save the file, and then refresh the **schedule.htm** page in Microsoft Edge.
 
>**Note:** The URL **/schedule/list?fail** generates errors that enable you to test that the exception handling parts of your code work correctly.

End of RLO
4.	Run the application again and verify that the error message **Service currently unavailable** appears.
5.	After testing, close Microsoft Edge and change the URL back to **/schedule/list**.

>**Results:** After completing this exercise, you will have modified the code for the Schedule page to displays the list of sessions retrieved from a web service, and to handle errors that can occur when communicating with a remote service.

### Exercise 2: Serializing and Transmitting Data.

#### Scenario

In this exercise, you will record the sessions that an attendee selects by transmitting this data to a web service. In addition, you will check for potentially busy sessions and inform the attendee accordingly.

First, you will create a function that creates an XMLHttpRequest object that posts data to a web service indicating the session that a user has selected. You will encode the content of this request and set the HTTP request headers appropriately. Next, you will handle the response and display a warning message if the response indicates that the attendee has selected a popular session that is likely to be busy. Finally, you will run the application and view the Schedule page to verify that the busy session message is displayed.

#### Task 1: Send the request to indicate the session that an attendee has selected.

1.	Open the **ContosoConf.sln** solution in the **Allfiles\Mod05\Labfiles\Starter\Exercise 2** folder.
2.	In the **schedule.js** file, find the function named **saveStar**.
3.	Within the function, add code to construct an **XMLHttpRequest** object and make a synchronous POST request to the following URL:
    ```javascript
        "/schedule/star/" + sessionId
    ```

>**Note:** The **sessionId** variable is passed in as a parameter to the **saveStar** function; it contains the id of the session that the user has selected.

4.	In the **saveStar** function, use the **setRequestHeader** function to set the request **Content-Type** header to the following: 
    ```javascript
        application/x-www-form-urlencoded
    ```
5.	Use the **Send** function to send the request, with the following body:
    ```javascript
        "starred=" + isStarred
    ```
>**Note:** The **isStarred** parameter to the **saveStar** function is a Boolean value that indicates whether the attendee has selected or deselected the session on the **Schedule** page.

#### Task 2: Handle the web service response.

1.	In the **saveStar** function, after the code creates the POST request but before it actually sends the request, if the attendee has selected the session (**isStarred** is true), create a callback for the **onreadystatechange** property of the **request** object that performs the following operations:
- If the request status is 200, then parse the response into a JSON object. This object should have a **starCount** property (the web service that sends the response back formats the data in this way).
- If **starCount** is greater than 50, display the following message by using the **alert** function:
    ```javascript
        This session is very popular! Be sure to arrive early to get a seat.
    ```

#### Task 3: Test the Schedule page.

1.	Run the application and view the **schedule.htm** page.
2.	Click the star next to the **New Technologies in Enterprise** session.
3.	Verify that an alert is displayed (this session is popular).
4.	Click the star next to **Diving in at the deep end with Canvas**.
5.	Verify that no alert is displayed (this session is less popular).
6.	Close Microsoft Edge.

>**Results:** After completing this exercise, you will have updated the Schedule page to send attendee selections to a web service, and to display a message when a session is popular.

### Exercise 3: Refactoring the Code by Using the jQuery ajax Method.

#### Scenario

The existing code using an **XMLHttpRequest** object works, but it is somewhat verbose. The **XMLHttpRequest** object also requires you to carefully set HTTP headers and encode the content appropriately; otherwise request data may not be transmitted correctly. In this exercise, you will refactor the JavaScript code for the Schedule page to make it simpler and more maintainable, by using the jQuery **ajax()** function.

First, you will refactor the **downloadSchedule** function by replacing the use of an **XMLHttpRequest** object with a call to the jQuery **ajax** method. Then you will refactor the **saveStar()** function in a similar manner. Using the **ajax()** function will simplify the code by automatically encoding the request content and setting HTTP headers. Finally, you will run the application and view the Schedule page to verify that it still displays sessions and responds to star clicks as before.

#### Task 1: Refactor the downloadSchedule function.

1.	Open the **ContosoConf.sln** solution in the **Allfiles\Mod05\Labfiles\Starter\Exercise 3** folder.
2.	Near the bottom of the **schedule.htm** file, before the reference to the **schedule.js** script, add a reference to the jQuery JavaScript file **jquery.min.js** located in the **scripts** folder. 

This action makes the **jQuery** object available for use in the JavaScript code for the Schedule page.

3.	In the **schedule.js** file in the **scripts/pages** folder, refactor the **downloadSchedule** function to use the jQuery **ajax()** function.
- The ajax options object must have a **type** property of **GET** and a **url** property of **/schedule/list**.
- Use the jQuery **done()** function to provide a callback function that handles the web service response; the response will contain a property called **schedule** that you should assign to the local **schedule** array variable, and then call the **displaySchedule()** function.

>**Note:** The **done** callback is passed an argument containing the parsed JSON response object, so you do not need to use the **JSON.parse** method to extract the session data.

- Use the jQuery **fail()** function to provide a callback function that handles any errors that might occur; simply display the message **Schedule list not available** in this callback

#### Task 2: Refactor the saveStar function.

1.	In the **schedule.js** file, refactor the **saveStar** function to use the jQuery **ajax()** function.
- The ajax options object must have a **type** property of **POST** and **url** property of **"/schedule/star/" + sessionId**.
- The data passed by using the ajax options object that specifies whether a session has been selected should be a JavaScript object and not a string.
- Use the jQuery **done()** function to implement a callback function that receives the web service response. In this callback, if the **starCount** property in the response object passed to the callback is greater than 50, display the message **This session is very popular! Be sure to arrive early to get a seat**.

#### Test the Schedule page.

1.	Run the application and view the **schedule.htm** page.
2.	Verify that the list of sessions appears correctly.
3.	Click the star icon next to **New Technologies in Enterprise** and verify that an alert is displayed.
4.	Click the star icon next to **Diving in at the deep end with Canvas** and verify that no alert is displayed.
5.	Close Microsoft Edge.
6.	In **schedule.js**, change the URL in the **downloadSchedule** function to be **/schedule/list?fail**.
7.	Run the application and view the **schedule.htm** page. Verify that the error message **Schedule list not available** is displayed.
8.	Close Microsoft Edge.

>**Result:** After completing this exercise, you will have refactored the JavaScript code that sends and receives data to use the jQuery **ajax** method.

©2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the  [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are  **not**  included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided &quot;as-is.&quot; Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.