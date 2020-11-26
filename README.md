# Mopinion Mobile SDK for android
The Mopinion Mobile SDK can be used to collect feedback from Android apps based on events.
To use Mopinion mobile feedback forms in your app you can include the SDK as a Library in your Android Studio project.

There are also other Mopinion SDK's available:

- [iOS SDK (React Native required)](https://github.com/mopinion/mopinion-sdk-ios)
- [iOS web SDK](https://github.com/mopinion/mopinion-sdk-ios-web)
- [Android SDK (React Native required)](https://github.com/mopinion/mopinion-sdk-android)

## Release notes for version 0.2.2
### New features in 0.2.2
- The new method `evaluate()` and its asynchronous callback response `onMopinionEvaluate()` allow you to verify whether or not a form would be opened for a specified event. 
- The new method `openFormAlways()`, to be used with the `onMopinionEvaluate()` method, allows you to open a form regardless of any proactive conditions set in the deployment.


## install

The Mopinion Mobile SDK Library can be installed by adding it to the `build.gradle` file of your project. The minimal required Android API level is 19.

You can see what your mobile forms will look like in your app by downloading our [Mopinion Forms](https://play.google.com/store/apps/details?id=com.mopinion.news) preview app in the Google Play Store.

### dependencies

- [Google Volley](https://github.com/google/volley)

### Android Studio

In the main project `build.gradle` file add the following:

```gradle
allprojects {
    repositories {
        google()
        jcenter()
        maven {
            url  "https://dl.bintray.com/mopinion/MopinionSDK"
        }
    }
}
```

In the `build.gradle` file of your main module, add the Mopinion SDK Library:

```gradle
android {
	...
	defaultConfig {
		...
		ndk {
		        abiFilters "armeabi", "armeabi-v7a", "arm64-v8a", "x86", "x86_64"
	    }
	}
}
...
dependencies {
    ...
	implementation('com.mopinion.mopinionsdkweb:mopinionsdkweb:0.2.1@aar')
	implementation 'com.android.volley:volley:1.1.1'
}
```

The SDK needs to connect to the Mopinion servers so the internet permission should be added to your `AndroidManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.app">
    
		<uses-permission android:name="android.permission.INTERNET" />
		
		<application
		...
```

### implement the SDK

In the activity source file, include these lines to import, open and call the MopinionSDK:

```java
import com.mopinion.mopinionsdkweb.*;
...
Mopinion M = new Mopinion(Context context, String key, boolean log);
M.event(String event);
```

* The `context` is the Activity context from which you would like to activate the feedback form, normally `this`.
* The `key` should be replaced with your specific deployment key. This key can be found in your Mopinion account at the `Feedback forms` section under `Deployments`.
* The `log` flag can be set to `true` while developing the app to see Logcat messages in Android Studio from the Mopinion SDK library. (The default is `false` if not supplied.)
* The `event` is a specific event that can be connected to a feedback form action in the Mopinion system.  
The default `_button` event triggers the form, but an unlimited number of custom events can also be added.

### example:
```java
import com.mopinion.mopinionsdkweb.*;
...
Mopinion M = new Mopinion(this, "12345abcde");
M.event("_button");
```

Note that the event call is asynchronous.

## extra data

From version `0.1.4` it's also possible to send extra data from the app to your form. 
This can be done by adding a key and a value to the `data()` method.
The data should be added before the `event()` method is called if you want to include the data in the form that comes up for that event.

```java
M.data(String key, String value);
```

### Example:

```java
import com.mopinion.mopinionsdkweb.*;
...
Mopinion M = new Mopinion(this, "12345abcde");
...
M.data("first name", "Andy");
M.data("last name", "Rubin");
...
M.event("_button");
```

## clear extra data

From version `0.1.6` it's possible to remove all or a single key-value pair from the extra data previously supplied with the `data(key,value)` method. To remove a single key-value pair use this method:

```java
M.removeData(String key)
```

### Example:

```java
M.removeData("first name")
```

To remove all supplied extra data use this method without arguments:

```java
M.removeData()
```

Example:

```java
M.removeData()
```

## Evaluate if a form will open
The event() method of the SDK autonomously checks deployment conditions and opens a form, or not.

From SDK version `0.2.2` you can use the evaluate() and related methods to give your app more control on opening a form for proactive events or take actions when no form would have opened.

It can also be used on passive events, but such forms will always be allowed to open.

###Procedure overview

1. Call the `evaluate()` method and pass it a callback function that implements the `Mopinion.MopinionOnEvaluateListener` interface.
2. In your callback function, check the response parameters and retrieve the `formKey` if there is any.
3. Optionally, pass the `formKey` to the method `openFormAlways()` to open your form directly, ignoring any conditions in the deployment.

### evaluate() method
Evaluates whether or not a form would have opened for the specified event. If without errors, the callback handler will receive the onMopinionEvaluate call with the response.

```java
public void evaluate(String event, MopinionOnEvaluateListener callbackHandler)

```
Parameters:

* `event`: The name of the event as definied in the deployment. For instance "_button".
* `callbackHandler`: The method implementing the `Mopinion.MopinionOnEvaluateListener` interface to handle the `onMopinionEvaluate()` callback function.

### onMopinionEvaluate() method
Method where the app receives the response of the evaluate call. Defined by the `Mopinion.MopinionOnEvaluateListener` interface. Note that in case of any system errors this may not be called at all.

```java
void onMopinionEvaluate(boolean hasResult, String event, @Nullable String formKey, @Nullable JSONObject response)
```
Parameters:

* `hasResult`: if true then the form identified by the formKey would have opened. If false then the form would not have opened and the formKey might be null in case no forms were found associated with the event.
* `event`: the original event name that was passed to the evaluate call to check in the deployment.
* `formKey`: identifying key of the first feedback form found associated with the event. Only one formKey will be selected even if multiple forms matched the event name in the deployment.
* `response`: optional object for extra response details on success/failure and forms. Reserved for future extensions.

### openFormAlways() method
Opens the form specified by the formkey, regardless of any proactive conditions set in the deployment.

```java
public void openFormAlways(String formKey) 
```
Parameters:

* `formKey`: key of a feedback form as provided by the onMopinionEvaluate() call.

###Example of using evaluate()
This snippet of pseudo code highlights the key points on how the aforementioned procedure fits together to implement the `Mopinion.MopinionOnEvaluateListener` interface.

```java
...
import com.mopinion.mopinionsdkweb.Mopinion;
...
public class MainActivity extends AppCompatActivity
                            implements Mopinion.MopinionOnEvaluateListener {
...
	Mopinion M=null;
...
    public void doSomething() {                        
        Mopinion M = new Mopinion(this, yourdeploymentkey);
        M.evaluate(event, this);    // check if a form would open
        // the actual result will be in the onMopinionEvaluate call
	}
...
	// callback handler
    @Override
    public void onMopinionEvaluate(boolean hasResult, String event, @Nullable String formKey, @Nullable JSONObject response) {
        if(hasResult) {
            // at least one form was found and all optional parameters are non-null
            M.openFormAlways(formKey); // because conditions can change every time, use the form key to open it directly
        }else{
            if(formKey !=null) {
				// Found form wouldn't open for event                
				M.openFormAlways(formKey); // we'll open it anyway using the formKey
            }else{
				// no form found for event
				...
            }
        }
    }
...
```
It's also possible to integrate the callback handler directly in the evaluate() call, then you don't need to implement the interface at your class level. The evaluate call would then look like this:

```java
...
		M.evaluate(event, new Mopinion.MopinionOnEvaluateListener() {
            @Override
            public void onMopinionEvaluate(boolean hasResult, String event, @Nullable String formKey, @Nullable JSONObject response) {
                // here the code to check the parameters like hasResult, formKey, etc
					...
            }
        });

```

## Edit triggers
In the Mopinion deployment editor you can define event names and triggers that will work with the SDK event names that you used in your app.
Login to your Mopinion account and go to Data collection, Deployments to use this functionality.

The custom defined events can be used in combination with rules/conditions:

* trigger: `passive` or `proactive`. A passive form always shows when the event is triggered. A proactive form only shows once, you can set the refresh time after which the form should show again.  
* percentage (proactive trigger): % of users that should see the form  
* date: only show the form at, after or before a specific date or date range  
* time: only show the form at, after or before a specific time or time range  
* target: the OS the form should show (iOS or Android) 