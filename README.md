# paypalcheckout-android

## Integration Guide

### build.gradle changes:

#### Project’s build.gradle file

Include the following under allprojects -> repositories

```
allprojects {

    repositories {
    
        ...
        
        maven { url "https://github.com/paypal/paypalcheckout-android/raw/nativeSDK" }
        maven { url "https://github.com/syrjs/maven/raw/master" }
        
        ...
        
    }
    
}
```



#### Module: app’s build.gradle file

Include the following under dependencies

```
dependencies {

  ...
  
  implementation 'com.android.support:customtabs:27.+'
  implementation 'com.paypal.pyplcheckout:nativexo:2.1'
  implementation 'syr.js.org:core:1.5.2'
  
  ...
  
}
```


### Setting up URI Schemes: This is used to listen to the redirect after checkout 

In your AndroidManifest.xml add, 

```
 <activity android:name="com.paypal.pyplcheckout.PYPLCheckoutReceiver">
   <intent-filter android:autoVerify="true">
       <action android:name="android.intent.action.VIEW" />

       <category android:name="android.intent.category.DEFAULT" />
       <category android:name="android.intent.category.BROWSABLE" />

       <data
          android:scheme="testapp" 
          android:host="paypalxo" />
    </intent-filter>
 </activity>
 
```


**Note: android:scheme under data is your app name, this should be the same as environment.kPYPLUrlScheme shown below.

**Note: android:host under data should equal "paypalxo".


### Invoking Checkout:

- Draw the PayPal checkout button onClick do the following

```
 PYPLCheckoutDelegate checkoutDelegate = new PYPLCheckoutDelegate() { 
           
           // handle return parameter for PayerID. This function will be called after the chckout has been completed
            
            @Override
            public void completeCheckout(HashMap<String, String> returnParams) {
            
                    //returnParams will contain all the params required to process checkout
                    
                    Log.i("returnParams", returnParams.toString());
                    
           }
 };
 
        // set the host
        PYPLCheckoutEnvironment environment = PYPLCheckoutEnvironment.getInstance();
        
        //This by default points to live, change it to sandbox for testing. Should be accompanied by a sandbox ec-token
        environment.kPYPLStageHost = "www.sandbox.paypal.com";
        
        //Set the checkoutDelegate
        environment.kCheckoutDelegate = checkoutDelegate;
        
        //This is false by default, setting this to true will always open the experience in the browser
        environment.kPYPLWebBrowserOnly = true;
        
        // set from the enviroment app name, this will be your app name/scheme you declared in the manifest
        environment.kPYPLUrlScheme = "testapp";
        
        //if your app is native, use this method
        //invoking checkout, you need to have an ec-token to call this. Replace the **Generated Ec-Token** with the one given by PayPal.
        PYPLCheckout.getInstance().startCheckoutWithECToken(this, **Generated Ec-Token**);
        
        //once the checkout is completed checkoutDelegate will receive the returned Params in a hash map
            
```





