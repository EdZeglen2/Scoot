# Scoot
Scoot is an open-source, MIT licensed, JavaScript library intended for rapid application development (RAD) with Microsoft 
.HTA's using pure JavaScript, jQuery and Knock Out. 

Structure
---------

Basic structure for a Scoot application looks like this.

	/data
		_pages_shared.json
	/footer (optional)
		footer1.html (optional)
	/header (optional)
		header1.html (optional)
		header2.html (optional)
	/images (optional)
		/icons (optional)
	/pages (suggested)
		ManageUsers.hta (made up for demo)
		ManageReports.hta (made up for demo)
	/scripts
		/bootstrap
		/ko
		/scoot
	myApp.ico
	myApp.hta
	myApp.json

Usage
-----

Make sure you include both the Knock Out libaray and the Scoot library.

    <script type="text/javascript" src="../Scripts/ko/knockout-3.4.0-min.js"></script>
    <script type="text/javascript" src="../Scripts/Scoot/0.1.10/scoot-0.1.10-min.js"></script>

Using Scoot in a page is simple.

```javascript
<script language="javascript">

	var objPage = new Scoot.page("MyAppStartPage", true, "data");

	objPage.include('../header/header1.html', '#header'); // Optional Header include
	objPage.include('../footer/footer1.html', '#footer'); // Optional Footer include
    
</script>
```

Here objPage is declared a new Scoot.page(**name**, **isroot**, **data**). The first parameter is the *name* of the .hta file itself. The second parameter is
true or false. If true, this .hta page is the first and root page of the application. If false, then the .hta page is a child
page and will have access to shared data differently than a root page. The last is the name of the shared data folder. This is
usually "data". For more information see the Documentation [link?].

Scoot Application
---------
Application-Specific data is stored in JSON format inside the data folders _pages_shared.json file. This data is availabe to 
(and updateable by) any page in the Scoot application. Here is one example of application data stored in _pages_shared.json.

```javascript
{
	"_pages_shared": {
	"myapplication": {
		"appid": "CE75CCF4-32D0-4723-8E98-9879CF9FF984",
		"appname": "Name that does what I want",
		"appconn": "DRIVER=[somedriver];DATA SOURCE=[SQLINSTANCENAME];INITIALCATALOG=[fillthisin];USER ID=[etc];PWD=[etc];",
	},
	"exportfolder": "C:\\Users\\[someusername]\\export",
	"linkeduser": null,
	"linkedrole": null
	}
}
```
	
Scoot Page(s)
----------

Page-Specific data is stored in JSON format inside the [pagename].json file. For instance if your page includes a drop down for detail types. You could create 
MyAppStartPage.json with the following JSON. Note: This page is not required! Do not include it if you do not want to use page-specific data. All
of the Scoot.page() properties and methods are documented here [link?].

```javascript
{
	"items": {
		"detailtypes": [
			{ "name": "DetailType1", "value": "1" },
			{ "name": "DetailType2", "value": "2" },
			{ "name": "DetailType3", "value": "3" },
			{ "name": "DetailType4", "value": "4" }
		]
	}
}
```

A Basic Scoot Page
------------------

A Basic Scoot page includes Bootstrap, Knock Out and Scoot. This is a non-working example. Do not copy and paste. In this
example users are loaded into a knock out bound page from a database using a connectionstring.

```html
<html> 
<head> 
<title>Scoot Sample</title> 

	<meta http-equiv="x-ua-compatible" content="ie=9">

<HTA:APPLICATION  
	ID="objHTASample" 
	APPLICATIONNAME="ScootSample" 
	SCROLL="yes" 
	SINGLEINSTANCE="yes" 
	WINDOWSTATE="maximize" 
	NAVIGABLE="yes"
	ICON="sample.ico"
> 
</head> 

	<link rel="stylesheet" href="/Scripts/bootstrap-3.3.6-dist/css/bootstrap.min.css" />    

	<script type="text/javascript" src="/Scripts/jQuery/jquery-1.12.3.min.js"></script>
	<script type="text/javascript" src="/Scripts/bootstrap-3.3.6-dist/js/bootstrap.min.js"></script>
	<script type="text/javascript" src="/Scripts/ko/knockout-3.4.0.js"></script>
	<script type="text/javascript" src="/Scripts/Scoot/0.1.9/scoot-0.1.9.js"></script>

```
```javascript
<script language="javascript">

	var objPage = new Scoot.page("ScootSample", true, "data");

	objPage.include('/header/header1.html', '#header');
	objPage.include('/footer/footer1.html', '#footer');

</script>
```
```html
<body style="padding-top:70px;"> 

	<div id="header"></div>

	<div class="container" id="AppPage">

		<div class="row">
			<h3>Scoot Sample</h3>
			<hr />
		</div>

		<div class="row">

			<table class="table">
				<tHead>
					<tr>
						<th>Username</th>
						<th>Email</th>
					</tr>
				</tHead>
				<tBody data-bind="foreach: users">
					<tr>
						<td><span data-bind="text: UserName"></span></td>
						<td><span data-bind="text: Email"></span></td>
					</tr>
				</tBody>
			</table>

		</div>

		<div id="footer"></div>

	</div>
```
```javascript	
<script type="text/javascript" language="javascript" >

	var ViewModel = function (oPage) {
		var self = this;

		self.page = oPage;
		self.datalocation = oPage.spec + "\\data\\";
		self.ScootVersion = Scoot.versioninfo().number;

		self.appconn = self.page.shared("myapplication").appconn;
		self.appname = self.page.shared("myapplication").appname;

		self.users = ko.observableArray([]);
		self.selectedUser = ko.observable();

		self.Init = function () {
			self.LoadUsers();
		}

		self.LoadUsers = function () {

			self.users.removeAll();

			var sql = "SELECT * FROM aspnet_users u "
				+ " JOIN aspnet_Membership m on m.UserId = u.UserId "
				+ "WHERE u.ApplicationId = '" + self.application + "' "

			var _users = Scoot.data("ADO", self.appconn);
			_users.open(sql, self.users);

		}             

	}

	function BindToPage() {
		var vm = new ViewModel(objPage);
		ko.applyBindings(vm, document.getElementById("AppPage"));
		vm.Init();
	}

	$(document).ready(function () {
		// do not bind until all includes are loaded.   
		objPage.load(BindToPage);
	});

</script>
```
```html
</body> 
</html>
```
