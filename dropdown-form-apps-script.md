## Getting form values into Google Apps Script

### Goal
Put up a sidebar in a Google Sheet. Give it month and year dropdowns. Transmit those values to Google Apps Script.


### Create a new spreadsheet

* From Google Docs, choose `New` > `Google sheets` > `Blank spreadsheet`
* Name sheet `DropdownToAppsScript1` (or whatever)
A blank sheet appears.
* Choose `Tools` > `Script editor`
* Replace the code in `Code.gs` with the following:

```js
/* Built-in function that runs automatically when spreadsheet is opened. */
function onOpen() {
  SpreadsheetApp.getUi()
    /* Create a top-level menu right on Google Sheets. */
    /* The mneu title 'Sidebar' has no special meaning. Feel free to replace it. */ 
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
   Both functions can be called whatever you want. */
function receiveFormValues(month,year) {
  Logger.log("receiveDropdownValues() month: " + month + ". year: " + year);
}
```

* Save the Project. Name it `DropdownToAppsScriptValues` or whatever.

* Choose `File` > `New` > `Html file`

* In the `Create File` dialog enter the filename as 'theForm'.

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



