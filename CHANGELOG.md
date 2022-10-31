# 0.3.0
- New callback behaviour via 2 new variants of the method `event()` with asynchronous callback response `onMopinionEvent()`, to receive a `MopinionEvent` from the feedback form.
- Supported callback `MopinionEvent` is when the form is displayed/closed or the user submitted the form.
- The callback includes an object `MopinionResponse` with optional data from some events.
- The new callback behaviour is optional. Existing code/methods continue to work unaltered.

# 0.2.2
- Artifact location change from bintray to jitpack as bintray stopped service. We no longer need `jcenter()` in the gradle files.
- New method `evaluate()` and its asynchronous callback response `onMopinionEvaluate()` to verify whether or not a form would be opened for a specified event. 
- New method `openFormAlways()`, to be used with the `onMopinionEvaluate()` method, to open a form regardless of any proactive conditions in the deployment.
- The deployment condition "Show only on specific OS version" is implemented to check the Android version. Earlier the version number was ignored, it only checked the OS.

# 0.1.7
- Fixed crashes for certain apps using an action bar and themes.

# 0.1.6 
- New method `removeData()` to remove all or a single key-value pair from the extra data previously supplied with the data(key,value) method.

# 0.1.4 
- New method `data()` to send extra data from the app to your form.