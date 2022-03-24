---
title: "JavaScript SDK"
icon: material/language-javascript
description: This is the official documentation for the Amplitude JavaScript SDK.
---

[![npm version](https://badge.fury.io/js/amplitude-js.svg)](https://badge.fury.io/js/amplitude-js)

This is the official documentation for the Amplitude JavaScript SDK.

???info "SDK Resources"
    - [JavaScript SDK Reference :material-book:](https://amplitude.github.io/Amplitude-JavaScript/)
    - [JavaScript SDK Repository :material-github:](https://github.com/amplitude/Amplitude-JavaScript)
    - [JavaScript SDK Releases :material-code-tags-check:](https://github.com/amplitude/Amplitude-Javascript/releases)

--8<-- "includes/ampli-vs-amplitude.md"

## SDK installation

### Installing via the Snippet

You can install the JavaScript SDK using a small snippet of code which you paste on your site to asynchronously load the SDK.
 On every page that you want to install Amplitude analytics, paste the code snippet just before the `</head>` tag, replacing API_KEY with your project's API key.
  You can find your project's API Key in your project's [Settings page](https://help.amplitude.com/hc/en-us/articles/360058073772).

```js
<script type="text/javascript">
(function(e,t){var r=e.amplitude||{_q:[],_iq:{}};var n=t.createElement("script")
;n.type="text/javascript"
;n.integrity="sha384-+EO59vL/X7v6VE2s6/F4HxfHlK0nDUVWKVg8K9oUlvffAeeaShVBmbORTC2D3UF+"
;n.crossOrigin="anonymous";n.async=true
;n.src="https://cdn.amplitude.com/libs/amplitude-8.17.0-min.gz.js"
;n.onload=function(){if(!e.amplitude.runQueuedFunctions){
console.log("[Amplitude] Error: could not load SDK")}}
;var s=t.getElementsByTagName("script")[0];s.parentNode.insertBefore(n,s)
;function i(e,t){e.prototype[t]=function(){
this._q.push([t].concat(Array.prototype.slice.call(arguments,0)));return this}}
var o=function(){this._q=[];return this}
;var a=["add","append","clearAll","prepend","set","setOnce","unset","preInsert","postInsert","remove"]
;for(var c=0;c<a.length;c++){i(o,a[c])}r.Identify=o;var u=function(){this._q=[]
;return this}
;var p=["setProductId","setQuantity","setPrice","setRevenueType","setEventProperties"]
;for(var l=0;l<p.length;l++){i(u,p[l])}r.Revenue=u
;var d=["init","logEvent","logRevenue","setUserId","setUserProperties","setOptOut","setVersionName","setDomain","setDeviceId","enableTracking","setGlobalUserProperties","identify","clearUserProperties","setGroup","logRevenueV2","regenerateDeviceId","groupIdentify","onInit","logEventWithTimestamp","logEventWithGroups","setSessionId","resetSessionId","setLibrary","setTransport"]
;function v(e){function t(t){e[t]=function(){
e._q.push([t].concat(Array.prototype.slice.call(arguments,0)))}}
for(var r=0;r<d.length;r++){t(d[r])}}v(r);r.getInstance=function(e){
e=(!e||e.length===0?"$default_instance":e).toLowerCase()
;if(!Object.prototype.hasOwnProperty.call(r._iq,e)){r._iq[e]={_q:[]};v(r._iq[e])
}return r._iq[e]};e.amplitude=r})(window,document);

amplitude.getInstance().init("API_KEY");
</script>
```

### Installing with npm or yarn

You can also install the [npm module](https://www.npmjs.com/package/amplitude-js) and embed the SDK directly into your product.

If you are using npm, use the following command:

```bash
npm install amplitude-js
```

If you are using yarn, use the following command:

```bash
yarn add amplitude-js
```

Now, import `amplitude` into your project:

```js
import amplitude from 'amplitude-js';
```

## Usage & Examples

### Initialization

Before you can instrument, you must initialize the SDK using the API key for your Amplitude project.
 Initialization creates a default instance, but you can create more instances using `getInstance` with a string name.

```js
var instance1 = amplitude.getInstance().init("API_KEY"); // initializes default instance of Amplitude client
var instance2 = amplitude.getInstance("instance-name").init("API_KEY"); // initializes named instance of Amplitude client
```

#### Initialization with options

Pass custom options in the `init` method. See a [list of options](https://github.com/amplitude/Amplitude-JavaScript/blob/main/src/options.js) on GitHub.

```js
var options = {};
var instance = amplitude.getInstance("instance").init("API_KEY", null, options); // initializes with the given options
```

### Set `userID`

Set `userID` when initializing the client, or after initialization with the `setUserId` method.

=== "Set `userID` on initialization"

    ```js
    var userId = "12345";
    amplitude.getInstance().init("API_KEY", userId); // initializes client with the given userId
    ```

=== "Set `userID` with the `setUserId` method"

    ```js
    var userId = "12345";
    amplitude.getInstance().setUserId(userId);
    ```

### EU data residency

Beginning with version 8.9.0, you can configure the server zone after initializing the client for sending data to Amplitude's EU servers. The SDK sends data based on the server zone if it's set.
 The server zone configuration supports dynamic configuration as well.

For earlier versions, you need to configure the `apiEndpoint` property after initializing the client.

!!!note
    For EU data residency, set up the project inside Amplitude EU. You must initialize the SDK with the API key from Amplitude EU.

```js
// For versions starting from 8.9.0
// No need to call setServerUrl for sending data to Amplitude's EU servers
amplitude.getInstance().init(euApiKey, null, {
  serverZone: 'EU',
  serverZoneBasedApi: true,
});

// For earlier versions
amplitude.getInstance().init(euApiKey, null, {
   apiEndpoint: 'https://api.eu.amplitude.com'
});
```

### Sending events

#### Basic events

Events represent how users interact with your application. For example, “Button Clicked” may be an action you want to note.

```js
const event = “Button Clicked”;
amplitude.getInstance().logEvent(event);
```

#### Events with properties

Events can also contain properties. They give context about the event taken. For example, “hover time” may be a relevant event property to “Button Clicked”.

```js
var event = "Button Clicked";
var eventProperties = {
    "hover time": "100ms"
};
amplitude.getInstance().logEvent(event, eventProperties);
```

Valid data types for event properties are string, array, object, boolean, and number. Object keys have a 1000 character limit.

#### Arrays in Event Properties

Arrays can be used as event property values. You can query array event properties by any subset of the individual properties in the array.

```js
var event = “Button Clicked”;
var eventProperties1 = {
    "selectedColors": ['red', 'blue']
};
amplitude.getInstance().logEvent(event, eventProperties1);

var eventProperties2 = {
    "selectedColors": ['red', 'green']
};
amplitude.getInstance().logEvent(event, eventProperties2);
```

### User properties

User properties help you understand your users at the time they performed some action within your app such as their device details, their preferences, or language.

#### Setting a user property

The Amplitude Identify object provides controls over setting user properties.
 First, an  Identify object must be instantiated, then you can call Identify methods on it, and finally the client makes a call with the Identify object.

```js
new amplitude.Identify(); // does nothing, must call one of the following methods and pass to client

var identify = new amplitude.Identify();
amplitude.getInstance().identify(identify); // makes identify call to amplitude with the properties of the identify object
```

#### `set`

`set`  sets the value of a user property. You can also chain together many set calls.

```js
var identify1 = new amplitude.Identify().set('key1', 'value1');
var identify2 = new amplitude.Identify().set('key2', 'value2').set('key3', 'value3');
amplitude.getInstance().identify(identify1);
amplitude.getInstance().identify(identify2);
```

#### `setOnce`

`setOnce` sets the value of a user property one time. Later calls using `setOnce` are ignored.

```js
var identify = new amplitude.Identify().setOnce('key1', 'value1');
amplitude.getInstance().identify(identify);
```

#### `add`

`add`  increments a user property by some numerical value. If the user property doesn't have a value set yet, it's initialized to `0`.

```js
var identify = new amplitude.Identify().add('value1', 10);
amplitude.getInstance().identify(identify);
```

#### Setting multiple user properties

You can use `setUserProperties` as a shorthand to set multiple user properties at one time. This method is a wrapper around `Identify.set` and `identify`.
 For example, set a user's city with this code:

```js
var userProperties = {
    city: "San Francisco"
};
amplitude.getInstance().setUserProperties(userProperties);
```

#### Arrays in user properties

Arrays can be used as user properties. You can directly set arrays or use `append` to generate an array.

```js
var values = ['value1', 'value2'];
var identify = new amplitude.Identify().set('key1', values);
amplitude.getInstance().identify(identify);
```

#### `prepend` and `append`

- `append` appends a value or values to a user property array.
- `prepend` prepends a value or values to a user property.

If the user property doesn't have a value set yet, it's initialized to an empty list before the new values are added.
 If the user property has an existing value and it's not a list, it's converted into a list with the new value added.

### User groups

--8<-- "includes/editions-growth-enterprise-with-accounts.md"

--8<-- "includes/groups-intro-paragraph.md"

!!! example

    If Joe is in 'orgId' '10' and '16', then the `groupName` would be '[10, 16]'). Here is what your code might look like:

    ```js
    amplitude.getInstance().setGroup('orgId', '[10,16]');
    ```

You can also use `logEventWithGroups` to set event-level groups, meaning the group designation applies only for the specific event being logged, and doesn't persist on the user unless you explicitly
 set it with `setGroup`.

```js
var eventProperties = {
  'key': 'value'
}

amplitude.getInstance().logEventWithGroups('initialize_game', eventProperties, {'sport': 'soccer'});
```

### Group identify

--8<-- "includes/editions-growth-enterprise-with-accounts.md"

--8<-- "includes/group-identify-considerations.md"

```js
var groupType = 'plan';
var groupName = 'enterprise';
var identify = new amplitude.Identify().set('key1', 'value1');

amplitude.getInstance().groupIdentify(groupType, groupName, identify);
```

An optional callback function can be supplied as a fourth argument to `groupIdentify`.

### Track Revenue

The preferred method of tracking revenue for a user is to use `logRevenueV2()` in conjunction with the provided Revenue interface.
 Revenue instances store each revenue transaction and let you define several special revenue properties that are used in Amplitude's Event Segmentation and Revenue LTV charts, such as `revenueType` and `productIdentifier`.
  You can also add event properties to revenue events via the eventProperties field. These Revenue instance objects are then passed into `logRevenueV2` to send as revenue events to Amplitude.
   This allows Amplitude to automatically display data relevant to revenue in the platform. You can use this to track both in-app and non-in-app purchases.

To track revenue from a user, call `logRevenueV2()` each time a user generates revenue. Here is an example:

```js
var revenue = new amplitude.Revenue().setProductId('com.company.productId').setPrice(3.99).setQuantity(3);
amplitude.getInstance().logRevenueV2(revenue);
```

Calling `logRevenueV2` generates a revenue event type:

- [Amplitude] Revenue: This event is logged for all revenue events, regardless of whether verification is enabled.

You can't change the default names given to these client-side revenue events in the raw data but you do have the option to change the [display name](https://help.amplitude.com/hc/en-us/articles/235649848#events).
 To learn more about tracking revenue, see the documentation [here](https://help.amplitude.com/hc/en-us/articles/115003116888).

!!!note
    Amplitude doesn't support currency conversion. Normalize all revenue data to your currency of choice before sending it to Amplitude.

| <div class="big-column">Name</div>  | Description  |
| --- | --- |
| `productId` | Optional. String. An identifier for the product. Amplitude recommends something like the "Google Play Store product ID". Defaults to `null`. |
| `quantity`| Required. Integer. The quantity of products purchased. Note: revenue = quantity * price. Defaults to 1. |
| `price` | Required. Double. The price of the products purchased, and this can be negative. Note: revenue = quantity * price. Defaults to `null`.|
| `revenueType` | Optional, but required for revenue verification. String. The revenue type. For example, tax, refund, income. Defaults to `null`. |
| `eventProperties`| Optional. Object. An object of event properties to include in the revenue event. Defaults to `null`. |

### Opt users out of tracking

You can turn off logging for a given user by calling `setOptOut`:

```js
amplitude.getInstance().setOptOut(true);
```

No events are saved or sent to the server while `setOptOut` is enabled. The opt out setting persists across page loads. You can re-enable logging by calling:

```js
amplitude.getInstance().setOptOut(false);
```

### Disable tracking

By default, the JavaScript SDK tracks some properties automatically. You can override this behavior by passing an object called `trackingOptions` when initializing the SDK,
 setting the appropriate options to `false`.

|<div class="big-column">Parameter</div> | Default Value|
|-------|------------|
|`city` | `true`|
|`country` | `true`|
|`carrier` | `true`|
|`device_manufacturer` | `true`|
|`device_model` | `true`|
|`dma` | `true`|
|`ip_address` | `true`|
|`language` | `true`|
|`os_name` | `true`|
|`os_version` | `true`|
|`platform` | `true`|
|`region` | `true`|
|`version_name` | `true`|

!!!warning
    The `trackingOptions` configurations prevent default properties from being tracked on new projects that have no existing data, not existing data.
     If you have a project with existing data that you would like to stop collecting the default properties for, please contact the Support team at support.amplitude.com.
      Note that the existing data isn't deleted.

### Setting custom user ID

If your app has its own login system that you want to track users with, you can call `setUserId` at any time:

```js
amplitude.getInstance().setUserId('USER_ID');
```

You can also add the User ID as an argument to the init call.

```js 
amplitude.getInstance().init('API_KEY', 'USER_ID');
```


Don't assign users a user ID that could change as each unique user ID is interpreted as a unique user in Amplitude. For more information see
 [Track unique users in Amplitude](https://help.amplitude.com/hc/en-us/articles/115003135607-Track-unique-users-in-Amplitude) in the Help Center.

### Logged out and anonymous users

--8<-- "includes/logged-out-and-anonymous-users.md"

```js
amplitude.getInstance().setUserId(null); // not string 'null'
amplitude.getInstance().regenerateDeviceId();
```

### Session tracking

Events triggered within 30 minutes of each other are counted towards the current session.
 The time of the first event marks the start time of a session and the last event triggered marks the end time of a session.
  You can change the session timeout window via the SDK configuration option field `sessionTimeout`.

#### Getting the session ID

In the JavaScript SDK, you can use the helper method `getSessionId` to get the value of the current `sessionId`:

```js
const sessionId = amplitude.getInstance().getSessionId();
```

### Configuring HTTP headers

If you are using a [domain proxy](https://developers.amplitude.com/docs/domain-proxies) that requires custom HTTP request headers, configure them with `options.headers` during initialization.

```js
amplitude.getInstance().init(APIKEY, null, {
  headers: {
     'x-session-id': appToken,
        'Content-Type': 'application/json;charset=utf-8'
  }
});
```

### Logging events to multiple projects

If you want to log events to multiple Amplitude projects, then must have separate instances for each Amplitude project.
 Each instance allows for independent `apiKeys`, `userIds`, `deviceIds`, and settings.

You must assign a name to each Amplitude project and instance and use that name consistently when fetching that instance to call functions.

!!!important
    After you have chosen a name for that instance you can't change it.
    Choose your instance names carefully. Every instance's data and settings are tied to its name, and you must use that instance name for all future versions of your project to maintain data continuity.
    Instance names don't need be the names of your projects in the Amplitude platform, but they need to remain consistent throughout your code. Each instance must also be initialized with the correct `apiKey`.

Instance names must be non-null and non-empty strings. Names are case insensitive, and you can fetch each instance name by calling.

Each new instance has its own `apiKey`, `userId`, `deviceId`, and settings.

The following is an example of how to set up and log events to two separate projects:

```js
// existing project, existing settings, and existing API key
amplitude.getInstance().init('12345', null, {batchEvents: true});
// new project, new API key
amplitude.getInstance('new_project').init('67890', null, {includeReferrer: true});

// need to reconfigure new project
amplitude.getInstance('new_project').setUserId('joe@gmail.com');
amplitude.getInstance('new_project').setUserProperties({'gender':'male'});
amplitude.getInstance('new_project').logEvent('Clicked');

var identify = new amplitude.Identify().add('karma', 1);
amplitude.getInstance().identify(identify);
amplitude.getInstance().logEvent('Viewed Home Page');
```

### Web attribution

While Amplitude’s JavaScript SDK doesn't collect web attribution data by default, setting it up is simple.
 The SDK can automatically collect this information if you enable attribution configuration options.

Amplitude supports automatically tracking the following through the SDK configuration options:

- The 5 standard UTM parameters from the user's browser cookie or URL parameters by using `includeUtm`.
- The referring URL and domain from includeReferrer.
- Google Click Identifier from URL parameters through `includeGclid`.
- Facebook Click Identifier from URL parameters through `includeFbclid`.  

#### Track UTM parameters

UTM parameters stand for Urchin Traffic Monitor parameters and are useful for analyzing the effectiveness of different ad campaigns and referring sites.
 UTM parameters are case sensitive so they're considered different values if the capitalization varies.

There are five different standard UTM parameters:

- `utm_source`: This identifies which website sent the traffic (for example: Google, Facebook).
- `utm_medium`: This identifies the link type that was used (for example: banner, button, email).
- `utm_campaign`: This identifies a specific campaign used (for example: summer_sale).
- `utm_term`: This identifies paid search terms used (for example:  product+analytics).
- `utm_content`: This identifies what brought the user to the site and is commonly used for A/B testing (for example: bannerlink, textlink).

Here is an example URL:

`https://www.amplitude.com/?utm_source=newsletter&utm_campaign=product_analytics_playbook&utm_medium=email&utm_term=product%20analytics&utm_content=bannerlink`

##### Enabling via SDK

In Amplitude, after you set the `includeUtm` option to true, the JavaScript SDK automatically pulls UTM parameters from the referring URL and include them as user properties on all relevant events:

- `includeGclid`: Gclid (Google Click Identifier) is a globally unique tracking parameter used by Google. If used, Google appends a unique parameter (for example: `"?gclid=734fsdf3"`) to URLs at runtime. By setting this to true, the SDK captures `initial_glid` and `gclid` as user properties.
- `includeFbclid`: Fbclid (Facebook Click Identifier) is a globally unique tracking parameter used by Facebook. If used, Facebook appends a unique parameter (for example: `"?fbclid=392foih3"`) to URLs at runtime. By setting this to `true`, the SDK captures `initial_fblid` and `fbclid` as user properties.
- `includeUtm`: If `true`, finds the standard UTM parameters from either the URL or the browser cookie and sets them as user properties. This sets `utm_source`, `utm_medium`, `utm_campaign`, `utm_term`, and `utm_content` as well as `initial_utm_source`, `initial_utm_medium`, `initial_utm_campaign`, `initial_utm_term`, and `initial_utm_content` as user properties for the user.
UTM parameters are captured once per session by default and occurs when the user loads your site and the Amplitude SDK for the first time.
 You can disable the once per session restriction through the `saveParamsReferrerOncePerSession` configuration option. When the SDK detects that it should start a new session,
  it pulls the UTM parameters that are available at the time. Those UTM parameters are set as user properties which persists for all the user's events going forward.
   However, initial UTM parameters are captured once for each user via a `setOnce` operation.

#### Track referrers

If you want to track how users are getting to your website, then track the referrer (the referring site).

Amplitude supports tracking these fields automatically:

- `referrer`: The last page the user was on (for example, `<https://amplitude.com/behavioral-analytics-platform?ref=nav>`).
- `referring_domain`: The domain that the user was last on (for example, `amplitude.com`).

##### Enabling via SDK

After you set the `includeReferrer` option to `true`, Amplitude captures the `referrer` and `referring_domain` for each session and set them as user properties on relevant events:

- `includeReferrer`: When `true`, captures the `referrer` and `referring_domain` for each session as user properties as well as the `initial_referrer` and `initial_referring_domain` user properties once for each user.
 The referrer is the entire URL while the `referring_domain` is the domain name from where the user came from.

Initial referring information is captured one time for each user via a `setOnce` operation.

#### First-touch attribution

Amplitude can capture the initial UTM parameters and referrer information for each user. The first-touch attribution values are set when a user's non-null UTM parameters are seen for the first time.
 These user properties are set once:

- `initial_utm_source`
- `initial_utm_medium`
- `initial_utm_campaign`
- `initial_utm_term`
- `initial_utm_content`
- `initial_referrer`
- `initial_referring_domain`
- `initial_gclid`
- `initial_fbclid`

This is done by setting the JavaScript SDK configuration options `includeReferrer`, `includeUtm`, and `includeGclid` to `true`.

!!!note
    Initial attribution information for users can change if they're merged with another user.

#### Last-touch attribution

In addition to first-touch attribution, Amplitude captures where a user came from for each of their sessions by setting these user properties:

- `utm_source`
- `utm_medium`
- `utm_campaign`
- `utm_term`
- `utm_content`
- `referrer`
- `referring_domain`
- `gclid`
- `fbclid`

This is done by setting the JavaScript SDK configuration options `includeReferrer`, `includeUtm`, and `includeGclid` to `true`.
 By default, the SDK only saves values at the start of the session, so if a user triggers some flow that causes them to land on the site again with a different set of UTM parameters within the same session, the second set isn't saved.

#### Multi-touch attribution

If you set `saveParamsReferrerOncePerSession` to `false` in your JavaScript SDK configuration, the SDK always capture any new values from the user. This updates these user properties throughout a session if they change:

- `utm_source`
- `utm_medium`
- `utm_campaign`
- `utm_term`
- `utm_content`
- `referrer`
- `referring_domain`
- `gclid`
- `fbclid`

Some customers also instrument these user properties as arrays to keep track of all the attribution parameters seen within the same session for a single user.

#### Logging captured attribution values via Amplitude

This is an advanced use case. 

!!!important

    These events count towards your event quota.

If you set `logAttributionCapturedEvent` to `true` in your JavaScript SDK configuration, the SDK logs an Amplitude event anytime new attribution values are captured from the user.

**Event Name:** [Amplitude] Attribution Captured

**Event Properties:**

- `utm_source`
- `utm_medium`
- `utm_campaign`
- `utm_term`
- `utm_content`
- `referrer`
- `referring_domain`
- `gclid`
- `fbclid`

### Google Tag Manager

Amplitude's JavaScript SDK supports integration with Google Tag Manager. See the [demo app](https://github.com/amplitude/GTM-Web-Demo) on GitHub for instructions on how to set it up.

### Dynamic configuration

Beginning with version 8.9.0, the JavaScript SDK allows users to configure their apps to use [dynamic configuration](../dynamic-configuration.md).
 This feature finds the best server URL automatically based on app users' location.

 To use, set `useDynamicConfig` to `true`.

- If you have your own proxy server and use `apiEndPoint` API, leave dynamic configuration off.
- If you have users in China Mainland, then we recommend using dynamic configuration.
- By default, this feature returns server URL of Amplitude's US servers, if you need to send data to Amplitude's EU servers, use `setServerZone` to set it to EU zone.

```js
amplitude.getInstance().init(euApiKey, null, {
  useDynamicConfig: true,
});
```

## Advanced topics

### COPPA Control

COPPA control in Amplitude's other SDKs disables tracking for IDFA, IDFV, city, location data (`location_lat` and `location_lng`), and IP address.
There's no interface for COPPA control in the JavaScript SDK because it doesn't track `location_lat`, `location_lng`, IDFA or IDFV. Instead, you can disable tracking for `city` and `ip_address` with `trackingOptions`.

```js
 var trackingOptions = {
        city: false,
        ip_address: false,
      };
```

### Getting the Device ID

You can get a user's current Device ID with the following code:

```js
var deviceId = amplitude.getInstance().getDeviceId() // existing device ID
```

### Setting configuration options

You can configure Amplitude by passing an object as the third argument to the init:

```js
amplitude.getInstance().init("API_KEY", null, {
    // optional configuration options
    saveEvents: true,
    includeUtm: true,
    includeReferrer: true
})
```

### Cookie management

#### Cookies created by the SDK

On initialization, the SDK creates a cookie that begins with the prefix `amp_` and ends with this first six digits of your API key.
 For example, `amplitude.getInstance().init("a2dbce0e18dfe5f8e74493843ff5c053")` would create a cookie with the key `amp_a2dbce`.

The cookie is used to keep track of metadata for the SDK:

- A randomly-generated device ID
- The current session ID
- The current user ID if a user ID is set
- The last event time
- Sequence IDs to put events and identify operations in the correct order

#### Disabling cookies

Amplitude cookies can be disabled altogether using the `disableCookies` option. When you disable cookies, the JavaScript SDK defaults to using `localStorage` to store its data.
 LocalStorage is a great alternative, but can't track cookies across domains.
 Because access to `localStorage` is restricted by subdomain, you can't track anonymous users across subdomains of your product (for example: `www.amplitude.com` vs `analytics.amplitude.com`).

#### SameSite

The JavaScript SDK defaults to setting the SameSite option on its cookies to `None`. This can be overridden with the `sameSiteCookie` option.
 `Lax` is recommended unless there are instances where you have third party sites that `POST` forms to your site.

#### HttpOnly cookies

An HttpOnly option isn't technologically possible for Amplitude's SDK cookies. The cookie is set on the client side and is used as a client-side data store. An SDK cookie can't set the HttpOnly flag.

#### Upgrading legacy cookies

Amplitude's legacy cookies were larger than the newer, more compact cookies.
 For users that have older cookies, the SDK only removes old cookies and starts using the new cookie format if the `cookieForceUpgrade` option is set to `true`.
 If you use the SDK in multiple products, and track anonymous users across those products, you be sure to set this option on all those products.

!!!note
    Upgrading cookies is only recommended if you are running into problems with oversized cookies.

### RequireJS

If you are using RequireJS to load your JavaScript files, then you can also use it to load the Amplitude JavaScript SDK script directly instead of using the loading snippet.
 If you take this approach you lose one of the key advantages of the snippet that lets your app to start and use the Amplitude SDK without having to wait for Amplitude to fully download.

```js
<script src='scripts/require.js'></script> <!-- loading RequireJS -->
<script>
 require(['https://cdn.amplitude.com/libs/amplitude-6.2.0-min.umd.gz.js'], function(amplitude) {
 amplitude.getInstance().init('API_KEY'); // replace API_KEY with your Amplitude API key.
 window.amplitude = amplitude; // You can bind the amplitude object to window if you want to use it directly.
 amplitude.getInstance().logEvent('Clicked Link A');
 });
 </script>
```

You can also define the path in your RequireJS configuration like this:

```js
<script src='scripts/require.js'></script> <!-- loading RequireJS -->
<script>
 requirejs.config({
  paths: {
   'amplitude': 'https://cdn.amplitude.com/libs/amplitude-6.2.0-min.umd.gz.js'
  }
 });

 require(['amplitude'], function(amplitude) {
  amplitude.getInstance().init('API_KEY'); // replace API_KEY with your Amplitude API key.
  window.amplitude = amplitude; // You can bind the amplitude object to window if you want to use it directly.
  amplitude.getInstance().logEvent('Clicked Link A');
 });
</script>
<script>
 require(['amplitude'], function(amplitude) {
  amplitude.getInstance().logEvent('Page loaded');
 });
</script>
```

### Cross domain tracking (JavaScript)

You can track anonymous behavior across two different domains. Anonymous users are identified by their Device IDs which must be passed between the domains. For example:

- Site 1: `www.example.com`
- Site 2: `www.example.org`

Users who start on Site 1 and then navigate to Site 2 need to have the Device ID generated from Site 1 passed as a parameter to Site 2. Site 2 then needs to initialize the SDK with that Device ID.
 The SDK can parse the URL parameter automatically if `deviceIdFromUrlParam` is enabled.

- From Site 1, grab the Device ID from `amplitude.getInstance().options.deviceId`.
- Pass the Device ID to Site 2 via a URL parameter when the user navigates. (for example: `www.example.com?amp_device_id=device_id_from_site_1`)
- Initialize the Amplitude SDK on Site 2 with `amplitude.init('API_KEY', null, {deviceIdFromUrlParam: true})`.

### Tracking UTM parameters, referrer, and gclid (JavaScript)

Amplitude supports automatically tracking:

- Standard UTM parameters from the user's cookie or URL parameters when the configuration option `includeUtm` is set to true during initialization.
- The referring URL when the configuration option includeReferrer is set to true during initialization.
- `gclid` (Google Click ID) from URL parameters when the configuration option includeGclid is set to true during initialization.

If tracking is enabled, then the SDK sets the values as user properties (for example,  `referrer` or `utm_source`) once per session. This is known as last touch attribution.
 The SDK also saves the initial values like `initial_referrer` and `initial_utm_source` using a `setOnce` operation.
 When these values are set, they never change. This is known as first touch attribution.

!!!note "`saveParamsReferrerOncePerSession`"
    By default, the SDK saves the values only at the start of the session. For example, if a user lands on your site with an initial set of UTM parameters and triggers some flow that causes them
     to land on your site again with a different set of UTM parameters within the same Amplitude session, the second set isn't saved.
      You can set the configuration option `saveParamsReferrerOncePerSession` to `false` to remove that restriction so that the SDK always captures new values from the user.

!!!note "`unsetParamsReferrerOnNewSession`"
    By default, the SDK carries over existing UTM Parameters and Referrer values at the start of a new session. For example,
     if a users session expires, the SDK maps the user's Referrer and UTM Parameters to existing values.
    To reset those values to null upon instantiating a new session, set `unsetParamsReferrerOnNewSession` to `true`.

### Callbacks for logEvent, Identify, and Redirect

You can pass a callback function to `logEvent` and identify, which gets called after receiving a response from the server.
This is useful if timing may cause an event to not be captured before the browser navigates away from a webpage.
 Putting the navigation in a callback to the `logEvent` method guarantees the event is captured before the navigation occurs. Here is a logEvent example:

```js
amplitude.getInstance().logEvent("EVENT_TYPE", null, callback_function);
```

Here is an identify example:

```js
var identify = new amplitude.Identify().set('key', 'value');
amplitude.getInstance().identify(identify, callback_function);
```

The status and response body from the server are passed to the callback function, which you might find useful. Here is an example of a callback function which redirects the browser to another site after a response:

```js
var callback_function = function(status, response) {
    if (status === 200 && response === 'success') {
        // do something here
    }
    window.location.replace('URL_OF_OTHER_SITE');
};
```

You can also use this to track outbound links to your website. For example, you would have a link like this:

```js
<a href="javascript:trackClickLinkA();">Link A</a>
```

Then, you would define a function that'scalled when the link is clicked like this:

```js
var trackClickLinkA = function() {
    amplitude.getInstance().logEvent('Clicked Link A', null, function() {
        window.location='LINK_A_URL';
    });
};
```

In the case that `optOut` is `true`, then no event is logged but the callback is called.
 In the case that `batchEvents` is `true`, if the batch requirements `eventUploadThreshold` and `eventUploadPeriodMillis` aren't met when `logEvent` is called, then no request is sent but the callback is still called.
 In these cases, the callback is called with an input status of 0 and a response of 'No request sent'.

### Error callbacks

You can pass a second callback to `logEvent` and identify that are called if the network request for the event fails.
 This is useful to detect if a user is using an ad blocker, or if there's an error from the Amplitude server due to an issue with the event format.
 You can use the error callback together with the success callback like this:

```js
var successCallback = function() {
    console.log('the event was logged successfully');
}

var errorCallback = function() {
    console.log('there was an error logging the event')
};

amplitude.getInstance().logEvent('event', null, successCallback, errorCallback);
```

### `init` callbacks

You can also pass a callback function to init, which is called after the SDK finishes its asynchronous loading. The instance is passed as an argument to the callback:

```js
amplitude.getInstance().init('API_KEY', 'USER_ID', null, function(instance) {
  console.log(instance.options.deviceId);  // access Amplitude's deviceId after initialization
});
```

### Using sendBeacon

In SDK version 8.5.0 and above, the SDK can send events using the browser's built-in navigator.sendBeacon API.
 Unlike standard network requests, events sent by sendBeacon are sent in the background, and are completed even if the user closes the browser or leaves the page.
 However, because these events are sent in the background, it's not known if the event uploads to the Amplitude server, and can't be saved to retry later.

To send an event using sendBeacon, set the transport SDK option to 'beacon' in one of two ways

```js
// set transport to 'beacon' when initializing an event
amplitude.getInstance().init('API_KEY', 'USER_ID', {transport: 'beacon'});

// set transport to 'beacon' after initialization
amplitude.getInstance().setTransport('beacon');

// this event will be sent using navigator.sendBeacon
amplitude.getInstance().logEvent('send event with beacon');

// set transport back to the default 'http' value
amplitude.getInstance().setTransport('http');

// this event will be sent using the standard xhr mechanism
amplitude.getInstance().logEvent('send event with http');

```

#### Using sendBeacon only when exiting page

Configuring sendBeacon to send data only when the user exits a page can be complicated, so the JavaScript SDK provides a convenient callback function that's called only when the user exits the page,
 and automatically switches the transport to 'beacon' for any logs sent in the callback. This callback is called `onExitPage` and is passed into the SDK on initialization, like so:

```js
var exitCallback = function {
  amplitude.getInstance().logEvent('Logging a final event as user exits via sendBeacon');
};

amplitude.getInstance().init('API_KEY', 'USER_ID', { onExitPage: exitCallback });
```

### Custom device IDs

By default, device IDs are randomly generated UUIDs, although you can define a custom device ID by setting it as a configuration option or by calling:

```js
amplitude.getInstance().setDeviceId('DEVICE_ID');
```

You can retrieve the Device ID that Amplitude uses with `Amplitude.getInstance().getDeviceId().` This method can return `null` if a `deviceId` hasn't been generated yet.

!!!note
    This isn't recommended unless you have your own system for tracking user devices. Make sure the `deviceId` you set is unique to prevent conflicts with other devices in your Amplitude data.
     It's best practice to use something like a UUID.

See [here](https://github.com/amplitude/Amplitude-Javascript/blob/master/src/uuid.js) for an example of how to generate on JavaScript.

--8<-- "includes/abbreviations.md"