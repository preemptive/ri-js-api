# Introduction

PreEmptive Analytics is a suite of products that allows you to gather information about how your application or site is used as your users interact with it, in real time. This data can be sent to different endpoints. By default, the API sends the data to the [Runtime Intelligence portal], which allows for aggregation and analysis. 

# Including instrumentation in your site

1. Include the API library in each page of your site or application, or in a common header file using one of the following:

    a. The latest version of the API library:


        <script type="text/javascript" src="//cdn.preemptive.com/sdk/latest/ri.min.js"></script>


    b. The latest revision of a specific version of the API library (eg. 1.0.x):


        <script type="text/javascript" src="//cdn.preemptive.com/sdk/1.0/ri.min.js" /></script>


    c. A specific revision of the API library (eg. 1.0.4):


        <script type="text/javascript" src="//cdn.preemptive.com/sdk/1.0.4/ri.min.js" /></script>

    
    For debugging purposes, you may replace `.min.` with `.src.` in the path to load the un-minified version of the API library.

2. Call the `RI.appStart()` function, passing the settings that you wish to use.

3. Call additional API functions to record session and feature usage, and to report errors as they occur.


# Initialization and session lifecycle

## RI.appStart( *settings*, *properties* )
Initializes the PreEmptive Analytics API and send a session start message. Run loaded plugins' `start` functions. This function must be called before any other messages can be sent.

* `settings` : An object containing initialization settings. Supported keys are:
  * `companyId`  : **Required**. Your company ID string. If using the Runtime Intelligence portal, this is assigned to you.
  * `appId`      : **Required**. A self-generated [UUID] string to represent this application or site.
  * `appName`    : **Required**. A string containing the name of this application or site.
  * `appVersion` : **Required**. A string containing the version number of this application or site.
  * `endPoint`   : **Optional**. A URI string specifying the endpoint to use when sending messages. If omitted, the default endpoint URI will send messages to the commercial Runtime Intelligence endpoint.
  * `instanceId` : **Optional**. A variable to access or function to call to retrieve a user-specific identifier (such as a hashed login name or serial number) for compiling usage data by individual user. If omitted, no user-specific data is collected or displayed.
  * `optIn`      : **Optional**. A variable to access or function to call to retrieve a boolean indicating whether the user has explicitly opted in or out of data collection. You are free to use whatever mechanism you want to provide this value. If omitted, the alternative opt-out mechanisms are checked. If no opt-out value is located, the API defaults to sending analytic message data.
* `properties` : An optional object containing arbitrary string or numeric properties to attach to the session start message.

Sample Code:

	var config={
		companyId: "9cc594a3-07a7-4273-9579-03f71e81ca6a",
		appId: "fcc594a3-07a7-4273-9579-03f71e81ca6a",
		appName="Sample Code",
		appVersion="1.0"
	};
	//optional properties
	var props={
		stringprop: "test value",
		numprop: 1234.5678
	};
	RI.appStart(config, props);


## RI.appStop( *properties* )
Send a session stop message. Run loaded plugins' `stop` functions. Prevent further messages from being sent. Use of this function is optional, and is useful if your application or site has the concept of an explicit session end (such as a 'log out' action).

* `properties` : An optional object containing arbitrary string or numeric properties to attach to the session stop message.

Sample Code:

    RI.appStop();

# Feature usage

## RI.featureTick( *featureName*, *properties* )
Send an instantaneous feature message.

* `featureName` : A string containing the name of the feature to report.
* `properties` : An optional object containing arbitrary string or numeric properties to attach to the feature tick message.

Sample Code:

    RI.featureTick("foobar");

## RI.featureStart( *featureName*, *properties* )
Send a feature start message. Paired with a feature stop message of the same name, the duration of the feature use will be calculated. Nested feature starts are supported.

* `featureName` : A string containing the name of the feature to report.
* `properties` : An optional object containing arbitrary string or numeric properties to attach to the feature start message.

Sample Code:

    RI.featureStart("sample");

## RI.featureStop( *featureName*, *properties* )
Send a feature stop message. This must be paired with a feature start message of the same name. A lone feature stop message will be ignored.

* `featureName` : A string containing the name of the feature to report.
* `properties` : An optional object containing arbitrary string or numeric properties to attach to the feature stop message.

Sample Code:

    RI.featureStop("sample");

# Error reporting

## RI.errorUncaught( *error*, *contact*, *comment*, *properties* )
Report an [`Error`][Error] object that has been not been caught within any `catch` block and has been surfaced by the runtime's `onError` event. The error details as well as a stack trace will be captured by the endpoint.

* `error`      : The [`Error`][Error] object to report.
* `contact`    : An optional string containing contact information provided by the user. You are free to gather this information via whatever mechanism you choose. You should collect this information after the error has occurred, so that the end user can choose to provide a contact address to be used for following up on the error being reported.
* `comment`    : An optional string containing a free-form comment provided by the user. You are free to gather this information via whatever mechanism you choose. You should collect this information after the error has occurred, so that the end user can choose to provide some details about what they were doing when they experienced the error.
* `properties` : An optional object containing arbitrary string or numeric properties to attach to the error reporting message.

Sample Code:

    RI.errorUncaught(new Error("uh oh.."), "foobar@example.com", "optional comment...");


## RI.errorCaught( *error*, *contact*, *comment*, *properties* )
Report an [`Error`][Error] object that has been caught within a `catch` block. The error details as well as a stack trace will be captured by the endpoint.

* `error`      : The [`Error`][Error] object to report.
* `contact`    : An optional string containing contact information provided by the user. You are free to gather this information via whatever mechanism you choose. You should collect this information after the error has occurred, so that the end user can choose to provide a contact address to be used for following up on the error being reported.
* `comment`    : An optional string containing a free-form comment provided by the user. You are free to gather this information via whatever mechanism you choose. You should collect this information after the error has occurred, so that the end user can choose to provide some details about what they were doing when they experienced the error.
* `properties` : An optional object containing arbitrary string or numeric properties to attach to the error reporting message.

Sample Code:
    
    try{
		throw new Error("something bad happened!");
	}catch(e){
	    RI.errorCaught(e, "foobar@example.com", "optional comment...");
	}

## RI.errorThrown( *error*, *contact*, *comment*, *properties* )
Report an [`Error`][Error] object that has been thrown by the `throw` keyword. The error details as well as a stack trace will be captured by the endpoint.

* `error`      : The [`Error`][Error] object to report.
* `contact`    : An optional string containing contact information provided by the user. You are free to gather this information via whatever mechanism you choose. You should collect this information after the error has occurred, so that the end user can choose to provide a contact address to be used for following up on the error being reported.
* `comment`    : An optional string containing a free-form comment provided by the user. You are free to gather this information via whatever mechanism you choose. You should collect this information after the error has occurred, so that the end user can choose to provide some details about what they were doing when they experienced the error.
* `properties` : An optional object containing arbitrary string or numeric properties to attach to the error reporting message.

Sample Code:
    
	var e=new Error("something bad happened!");
	RI.errorThrown(e, "foobar@example.com", "optional comment...");

# Attaching arbitrary properties (extended keys)

All functions have a last, optional parameter named `properties` which is a hash to be used for attaching arbitrary string or numeric properties (known as *extended keys*) to the message to be sent. The type - string or numeric - of the property is used by the [Runtime Intelligence portal] when determining what metrics to display. For example, numeric properties will show an average. This is useful for counts of things, but less useful if the value you attach is more arbitrary (for example, a zip code). In these instances, you will want to use the explicit syntax described below to indicate that the portal should interpret these values as strings.

The `properties` hash accepts properties as either `key: value` (where the type of the value is inferred automatically) or `key: object` (where the type of the value is explicitly specified by a property name in the object). If the value is a variable, its value is used as the value. If the value is a function, it will be executed and its return value will be used as the value. If the value is some other non-string/non-number object, it will be converted to a string and that string used as the value. If the value is null or empty string, the extended key will be omitted.

The object used in a `key: object` property must contain a `key: value` pair in the form `{s: "42"}` for a string value or `{n: 42}` for a numeric value. Any other keys are ignored. If both are specified, the string value (`s` key) is used. If a numeric value is selected but the value is not a number, the extended key will be omitted.

Sample Code:

	var strfn=function(){
		return "foo bar";
	};
	var numfn=function(){
		return 123.456;
	};
	var props={
		string1: "foo bar",
		string2: {s: "foo bar"},
		string3: strfn,
		num1: 123.456,
		num2: {n: 123.456},
		num3: numfn
	};	

# Opt-out mechanisms

While you are free to collect analytics data in any way and in accordance with any privacy policy you wish, allowing your users to opt-in to collecting usage information is not only standard in the industry, it's also the right thing to do for your users. The PreEmptive Analytics API supports three methods for allowing your users to opt-in or out of data collection.

1. The PreEmptive Analytics API will disable data collection if the user has enabled the [Do Not Track] feature available in many modern browsers. The browser must support accessing the value of the Do Not Track preference via Javascript. If the user is using IE8+ or higher and has enabled InPrivate Filtering, data collection will also be disabled. If you wish to provide more inclusive support for the Do Not Track preference, you can capture the value of the **`DNT`** HTTP header in your server-side code and provide it to the API via one of the other two opt-out mechanisms.

 Note: The PreEmptive [Runtime Intelligence] endpoint also respects the **`DNT`** HTTP header and will discard any message that has it enabled. It is recommended that developers disable their browser's [Do Not Track] feature while adding analytics to their applications.

2. Set a cookie named `RI_sendDisabled` to either `0` for opt-in or `1` for opt-out. A value of `1` (for opt-in) will override the user's Do Not Track preference, if specified.

3. Collect the user's opt-out preference manually, and pass it to the `RI.appStart()` function by using the `optIn` property of the `settings` object. You can set the value of the `optIn` property to a variable to access or function to call to retrieve a boolean indicating whether the user has explicitly opted in or out of data collection. If a boolean value is provided here (either opt-in or opt-out) it will override both the value of the `RI_sendDisabled` cookie and the user's Do Not Track preference. If the value is `null` or some other type, it will be ignored.




[Runtime Intelligence]: http://preemptive.com/products/runtime-intelligence/overview
[Runtime Intelligence portal]: http://www.runtimeintelligence.com
[Sign Up]: http://preemptive.com/landing/eval-request
[UUID]: http://en.wikipedia.org/wiki/Universally_unique_identifier
[Error]: https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Error
[Do Not Track]: http://donottrack.us
[preemptive/ri-js-plugins]: http://github.com/preemptive/ri-js-plugins
