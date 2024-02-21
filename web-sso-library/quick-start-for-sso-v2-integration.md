# Integration Guide

This section demonstrate how to integrate with web SSO flow from scratch. If you would like to integrate with server, please refer to [server to server SSO integration](../server-to-server-sso/server-to-server-sso-integration.md) **or jump to last section for web SSO flow sample code as quick start.**

{% hint style="info" %}
Since Feb. 2024, the WEB SSO SDK V1 will silently bump to V3, and compatible with Chrome's updated 3rd party cookie policy. The integration should be the same for V1 & V3 SDK. If client's integration is before Feb. 2024, please send us feedback if regression issue occurred.
{% endhint %}

{% hint style="info" %}
Before using the SSO SDK, you should already registered OAuth setting with account  team. If not, please contact HTC Account team for more info.
{% endhint %}

## Step 0. Set Up HTCAccountHost Global Variable

For non-production development, the client should set up **which HTC account environment you will request for, and put it before load** `htcaccount.js` **script:**

```markup
//stage
// put it before htcaccount.js script tag
<script type="application/javascript">
    window.HTCAccountHost = "account-stage.htcvive.com";
    window.HTCProfileDefaultHost = "account-profile-stage.htcvive.com";
    window.HTCOrgProfileDefaultHost = "account-stage-usw2.viveport.com";
    
    // SDK function call should execute after DOMContentLoaded event occurred
    // https://developer.mozilla.org/en-US/docs/Web/API/Document/DOMContentLoaded_event
    // ex:
    // addEventListener("DOMContentLoaded", (event) => { ..... });
    // onDOMContentLoaded = (event) => { ..... };
    // $(document).ready(function() { .... });

</script>
// htcaccount.js resource domain should based on integration environment,
// please refer >> HTC Account Server Host Environment << table
<script src="https://account-stage.htcvive.com/htcaccount.js"></script>

//above global variables are set to PROD env by default
```

### HTC Account Server Host Environment

| ENV   | Resource Domain                    |
| ----- | ---------------------------------- |
| STAGE | ​https://account-stage.htcvive.com |
| PRDO  | ​https://account.htcvive.com       |

### HTC Account Profile Server Host Environment

| ENV   | Resource Domain                           |
| ----- | ----------------------------------------- |
| STAGE | https://account-profile-stage.htcvive.com |
| PROD  | https://account-profile.htcvive.com       |

### HTCOrgProfileDefaultHost env

| ENV   | Resource Domain                         |
| ----- | --------------------------------------- |
| STAGE | https://account-stage-usw2.viveport.com |
| PROD  | https://account.viveport.com            |

## Step 1. Load SSO SDK

HTC Account Web SSO Javascript Library is obtained from `htcaccount.js`. You can call the `HTCAccount.init()` with config to initialize the library service.

A simple example should as follow:

```markup
// this should based on your deployment environment
<script src="https://account.htcvive.com/htcaccount.js"></script>
<script>
  addEventListener("DOMContentLoaded", (event) => {
      var config = { appid: "$APPID", scope: "email" };
      HTCAccount.Event.subscribe('auth.login', function(resp) {
          // check auth status, then call login or getAuthResponseV2 function
      });
      
      // besure to sub. events before init action,
      // so first init event could be dispatched.
      HTCAccount.init(config);
  });
</script>
```

{% hint style="info" %}
Client could add **hl** query parameter for specifying web SSO's UI/UX locale to align client site's locale. Please check [this section](../server-to-server-sso/server-to-server-sso-integration.md#hl-query-parameter) for more info**.**
{% endhint %}

## Step 2. Setup Init Config to Launch SDK

The configuration for HTCAccount.init should have basic format as below, for more flag and detail please check API Specification **Initialization** section

{% hint style="danger" %}
The **`scope`** field should reflect to client's OAuth setting applied scope list, other wise the request **will be blocked by validation check or user profile access**.

**Please check with account team if lost its registered OAuth setting.**&#x20;
{% endhint %}

```javascript
window.HTCAccount.init({
    // appid is registered OAuth client id
    appid: "0058ef13-1fed-4a0a-b6fb-1181cc525507",
    scope: "email birthday"
});
```

{% content-ref url="api-specification.md" %}
[api-specification.md](api-specification.md)
{% endcontent-ref %}

| Config Param | Description                                       | Type   | Example                              |
| ------------ | ------------------------------------------------- | ------ | ------------------------------------ |
| appid        | OAuth client id                                   | String | 0058ef13-1fed-4a0a-b6fb-1181cc525507 |
| scope        | OAuth scopes string, delimited by space character | String | "email profile.write birthday"       |

{% hint style="warning" %}
In API Specification section mention **authorities** flag to switch social buttons, in current phase SSO V2 UI have not implement this feature yet, so you can safely ignore this config.&#x20;
{% endhint %}

## Step 3. Fetch Login Status

Once initialization complete, we can get user login information from HTC Account main site by subscribe **auth.login** event :

```javascript
window.HTCAccount.Event.subscribe('auth.login', function(response){
   // User has signed in to HTC Account service.
   // response JSON would be
   /*
   {
      "status": "connected",
      "authResponse": {
        "access_token": "r8f7dIgXHIYOKfVqGr7YY....",
        "client_id": "5431c830-388a-46dc-929b-c9f9c7b16031",
        "expire": 1471776618831,
        "life": 86400000,
        "scope": "",
        "uid": "92bbb0ba-b8e4-4ab4-b8a3-70f2249fd689"   
      }
   }
   */

   // User does not sign in to HTC Account service.
   // response JSON would be
   /*
   {
      "status": "unknown",
      "authResponse": false
   }
   */
});
```

If `status` is connected, and can get `authResponse` info, then user should already logged in current website. o.w. we need user to log-in instead.

{% content-ref url="api-specification.md" %}
[api-specification.md](api-specification.md)
{% endcontent-ref %}

## Step 4. Trigger Login/Signup Flow

To start SSO V2 authentication flow, please call `login` with **authConfig, below is a minimum configuration set**:

{% hint style="info" %}
auth config have same and consistent format for SSO SDK or infinity lib&#x20;
{% endhint %}

```javascript
var minConfigs = {
    "clientId": "33035df5-7ddd-4417-a20a-e56722489550",
    "redirectionUrl": "https://www.viveport.com",
    "flow": "infinity",
    "initView": "sign-in",
    "viewToggles": [],
    "scope": "email",
    "requireAuthCode": false
  };

window.HTCAccount.login(() => {}, { 
   type: 'redirect',
   // next_url is client site's redirection url in relative path format
   // if redirection url not have path part, we can omit this field
   // and nex_url will fallback to window.location.href instead
   // this next_url will finally as full url as state JSON's redirection_url field
   next_url: location.pathname,
   state: JSON.stringify(minConfigs)
});
```

For more info, please check config definition in below link

{% content-ref url="../infinity-flow-integration-1/auth-configs-for-sso-v2.md" %}
[auth-configs-for-sso-v2.md](../infinity-flow-integration-1/auth-configs-for-sso-v2.md)
{% endcontent-ref %}

## Example Code

Below example is assume operate on HTC Account Stage environment.

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% hint style="danger" %}
The **`scope`** field should reflect to client's OAuth setting applied scope list, other wise the request **will be blocked by validation check or user profile access**.

**Please check with account team if lost its registered OAuth setting.**&#x20;
{% endhint %}

The **`scope`** field should reflect to client's OAuth setting applied scope list, other wise the request will be blocked by validation check.

{% hint style="info" %}
**Please check with account team if lost its registered OAuth setting.**&#x20;
{% endhint %}

```markup
<script type="application/javascript">
  // PLEASE ENSURE SET UP HOST BEFORE LOADING SSO JS SDK 
  // IF NOT SET, IT WILL DEFAULT TO PRODUCTION HOST NAME
  window.HTCAccountHost = "account-stage.htcvive.com/";
  window.HTCProfileDefaultHost = "account-profile-stage.htcvive.com";
  window.HTCOrgProfileDefaultHost = "account-stage-usw2.viveport.com";
  
  var initConfig = {
    appid: "33035df5-7ddd-4417-a20a-e56722489550",
    // scope list should based on appid's oauth set up scope, 
    // please check with account team if don't know registered scopes.
    scope: "email birthday"
  };
  
  var authConfigs = {
    "sessionId": "4686d579-4176-46fc-8636-660643cf1f8f",
    "clientId": initConfig.clientId,
    "flow": "infinity",
    "initView": "sign-in",
    "viewToggles": [],
    "scope": initConfig.scope,
    "requireAuthCode": false
  };
  
  $(document).ready(function() {
   
    window.HTCAccount.Event.subscribe('auth.login', function(resp) {
       if (resp.status === 'connected') {
          // already have authe key, and should be able to fetch user auth info
          console.log(resp.authResponse);
        
          window.HTCAccount.getProfileV4(["username","backupEmail"],function(data) {
            // get user profile JSON from data parameter, 
            // please check API specification related section for detail
          });
        
       } else {
         // this may indicate user is not logged in yet, or unverified user
         // so require user to re-run login/sign-up flow
       
         // will immediately trigger login/sign-up flow by 302 redirection
         // usually can put this command in other action ex: user click sign-in
         window.HTCAccount.login(() => {}, { 
            type: 'redirect',
            next_url: location.pathname,
            state: JSON.stringify(authConfigs)
         });
       }
    });
    
    window.HTCAccount.init(initConfig);
  });
</script>
<script src="https://account-stage.htcvive.com/htcaccount.js?hl=zh_TW"></script>
```
