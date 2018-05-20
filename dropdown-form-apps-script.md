## Getting form values into Google Apps Script

It's fairly easy to create a user interface in Google Apps using HTML, but
obtaining form values is a bit of a challenge. And where does your UI go, anyway?
In this example the spreadsheet adds a menu to Google Sheets, creates a menu
item to add a sidebar to the spreadsheet, wait for user input, and
use Javascript to obtain form values and send them back to Google Apps Script
after the user clicks the OK button.

The result will look like this:

![Screen shot of the sidebar form with dropdowns](/assets/sidebar-form.png)

### Create a new spreadsheet

* From Google Docs, choose **New** > **Google sheets** > **Blank spreadsheet**
* Name sheet `DropdownToAppsScript1` (or whatever).

A blank sheet appears.

### Create code to build a mneu when the spreadsheet opens 

* Choose **Tools** > **Script editor**
* Replace the contents of `Code.gs` with the following:

```js
/* Built-in function that runs automatically when spreadsheet is opened. */
function onOpen() {
  SpreadsheetApp.getUi()
    /* Create a top-level menu right on Google Sheets. */
    /* The menu title 'Sidebar' has no special meaning. Feel free to replace it. */ 
    .createMenu('Sidebar')
    /* Menu item text is Tax report, and it runs the function named in the next param. */
    .addItem('Tax report', 'createForm')
    /* Update the Google sheets menu and make this new menu live. */
    .addToUi();
}

/* Called by the second param in .addItem. Creates a sidebar. */
function createForm() {
  /* 'theForm' was just to see if it had to be named 'index', which
      is what Google examples use. */
  var html=HtmlService.createHtmlOutputFromFile('theForm')
    .setTitle('Year and Month')
    .setWidth(300);
  SpreadsheetApp.getUi()
        .showSidebar(html);
}

/* When the user clicks the OK button in theForm.html, it calls
   a function on that form named sendFormValueForGs().
   Its purpose is to collect values entered on the form and call this function. 
   Both functions can be named whatever you want. */
function receiveFormValues(month,year) {
  Logger.log("receiveDropdownValues() month: " + month + ". year: " + year);
}
```

### onOpen() runs whene the spreadsheet loads and is good for initializing

`onOpen()` is an event that occurs when the spreadsheet loads or if the page is refreshed.

The call to `SpreadsheetApp.getUi().createMenu('Sidebar')` creates a new menu named **Sidebar**
at a peer level with the spreadsheet's other menus, such as **File** and **Edit**. You could call it
something else; the name Sidebar has no special meaning.

It is chained to the `addItem()` method, which creates the menu entry you will use
to create the entries on the **Sidebar** menu. `Tax report` is the menu item
you see, and `createForm` is a string representing the name of the function to be called
after the user chooses **Tax report** on the **Sidebar** menu.

```js
SpreadsheetApp.getUi().createMenu('Sidebar').addItem('Tax report', 'createForm')
```

There's one more thing in the call chain, and that's the `addToUi()` method, which physically
updates the web page with the menu item. So the full call is:

```js
SpreadsheetApp.getUi().createMenu('Sidebar').addItem('Tax report', 'createForm').addToUi();
```

For clarity and commenting the line is displayed like this:

```js
SpreadsheetApp.getUi()
  .createMenu('Sidebar')
  .addItem('Tax report', 'createForm')
  .addToUi();
```

### createForm() builds an HTML form using code

The `Tax report` menu item calls a Google Apps Script API function 
when the user selects it. We have decided to create a function
called `createForm()`. You could give it any name but its 
job is to create a user interface in HTML. It will be passed to
the Google Apps Script API call `HtmlService.createHtmlOutputFromFile()`.

* Save the Project. Name it `DropdownToAppsScriptValues` or whatever.

* Choose `File` > `New` > `Html file`

* In the `Create File` dialog enter the filename as 'theForm'.
You don't need to follow with '.html' because Apps Script supplies it for you.
You can name it anything; Google's examples use 'index'.

Note that because the calling the DOM via
Javascript is used to obtain values, you don't
need `<form>` tags.

```html
<!DOCTYPE html>
<html>
	<head>
		<base target="_top">
	</head>
	<body>
		<select id="month-dropdown">
			<option value="0">January</option>
			<option value="1">February</option>
			<option value="2">March</option>
			<option value="3">April</option>
			<option value="4">May</option>
			<option value="5">June</option>
			<option value="6">July</option>
			<option value="7">August</option>
			<option value="8">September</option>
			<option value="9">October</option>
			<option value="10">November</option>
			<option value="11">December</option>
		</select> 

		<select id="year-dropdown">
			<option value="2014">2014</option>
			<option value="2015">2015</option>
			<option value="2016">2016</option>
			<option value="2017">2017</option>
			<option value="2018" selected>2018</option>
			<option value="2019">2019</option>
		</select> 

		<input type="button" value="OK" onclick="sendFormValueForGs()" />    

		<script>
			/* Collect values from the form controls. Send them back to the .gs code.  */
			function sendFormValueForGs() {
				var month=document.getElementById("month-dropdown").value;
				var year=document.getElementById("year-dropdown").value;
				google.script.run.receiveFormValues(month,year);
			}
		</script>  
	</body>
</html>
```

* Save.


### Get permissions for this app to run.

This app can't run without permissions, so let's take care of that now.

* Choose `Run` > `onOpen`

An `Authorization required` dialog appears.

* Choose `Review Permissions`.

* Sign into your Google account as required, and give it the permissions it asks for.

* Return to your spreadsheet and refresh the web page.

The menu named `Sidebar` appears, and inside it is the `Tax report` menu item.

![Screen shot of menu named Sidebar generated with google apps script createmenu API](/assets/apps-script-createmenu.png)

* From the `Sidebar` menu, choose `Tax report`.

You may be asked to give permissions here.

![Screen shot of Authorization Required google apps script dialog](/assets/apps-script-authorization-required.png)

The sidebar appears after a moment.

* Choose a month and date from the sidebar, then click `OK`.

![Screen shot of the sidebar form with dropdowns](/assets/sidebar-dropdown.png)


When you review the execution log you'll see what values were chosen.




