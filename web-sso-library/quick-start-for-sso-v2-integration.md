# Quick Start For SSO V2 Integration

This section demonstrate how to integrate with new SSO flow and new UI for VIVEPORT, and vive related web services.

{% hint style="info" %}
Before using the SSO SDK, you should already registered OAuth setting with account  team. If not, please contact account team for more info.
{% endhint %}

## Step 1. Load SSO SDK

HTC Account Web SSO Javascript Library is obtained from `htcaccount.js`. You can call the `HTCAccount.init()` with config to initialize the library service.

A simeple example should as follow:

```markup
// this should based on your deployment environment
<script src="https://account.htcvive.com/htcaccount.js"></script>
<script>
  var config = {appid: "$APPID"};
  HTCAccount.init(config);
  HTCAccount.getLoginStatus(function() {
    // APP init
  });
  HTCAccount.getAuthResponseV2();
</script>
```

## Step 2. Setup Init Config to Launch SDK

The configuration for HTCAccount.init should have basic format as below, for more flag and detail please check API Specification **Initialization** section:

```javascript
window.HTCAccount.init({
    appid: "0058ef13-1fed-4a0a-b6fb-1181cc525507",
    scope: "email birthday"
});
```

{% page-ref page="api-specification.md" %}

| Config Param | Description | Type | Example |
| :--- | :--- | :--- | :--- |
| appid | OAuth client id | String | 0058ef13-1fed-4a0a-b6fb-1181cc525507 |
| scope | OAuth scopes string, delimited by space character | String | "email profile.write birthday" |

{% hint style="warning" %}
In API Specification section mention **authorities** flag to switch social buttons, in current phase SSO V2 UI have not implement this feature yet, so you can safely ignore this config. 
{% endhint %}

## Step 3. Fetch Login Status

Once initialization complete, we can get user login information from account main site, please check API Specification **Login Status** section for the format of **status** response JSON :

```javascript
window.HTCAccount.getLoginStatus(function(status){
   // User has signed in to HTC Account service.
   /*{
    "status": "connected",
    "authResponse": {
        "access_token": "r8f7dIgXHIYOKfVqGr7YY....",
        "client_id": "5431c830-388a-46dc-929b-c9f9c7b16031",
        "expire": 1471776618831,
        "life": 86400000,
        "scope": "",
        "uid": "92bbb0ba-b8e4-4ab4-b8a3-70f2249fd689"   
    }
   }*/

   // User does not sign in to HTC Account service.
   /*{
    "status": "unknown",
    "authResponse": false
   }*/
});
```

If `status` is connected, and can get `authResponse` info, then user should already logged in current website. o.w. we need user to log-in instead.

{% page-ref page="api-specification.md" %}

## Step 4. Trigger Login/Signup Flow

To start SSO V2 authentication flow, please call `redirecbyAuthConfig` with **authConfig, below is a minimum configuration set**:

{% hint style="info" %}
auth config have same and consistent format for SSO SDK or infinity lib 
{% endhint %}

```javascript
var minConfig = {
    "clientId": "33035df5-7ddd-4417-a20a-e56722489550",
    "redirectionUrl": "https://www.viveport.com",
    "flow": "infinity",
    "initView": "sign-in",
    "viewToggles": [],
    "requireAuthCode": false
  };
  
window.HTCAccount.redirectByAuthConfig(minConfig);
```

for more info, please check config definition at **Start Infinity Auth** section in below link

{% page-ref page="../infinity-flow-integration-1/auth-tool-for-viveport-web.md" %}

## Sample Code

Below example is assume operate on HTC Account Stage environment.

```markup
<script type="application/javascript">
  // PLEASE ENSURE SET UP HOST BEFORE LOADING SSO JS SDK 
  // IF NOT SET, IT WILL DEFAULT TO PRODUCTION HOST NAME
  window.HTCAccountHost = "account-stage.htcvive.com/";
  window.HTCProfileDefaultHost = "account-profile-stage.htcvive.com";
  window.HTCOrgProfileDefaultHost = "account-stage-usw2.viveport.com";
  
  var authConfigs = {
    "sessionId": "4686d579-4176-46fc-8636-660643cf1f8f",
    "clientId": "33035df5-7ddd-4417-a20a-e56722489550",
    "redirectionUrl": "https://store-stage-usw2.viveport.com/",
    "flow": "infinity",
    "initView": "sign-in",
    "viewToggles": [],
    "requireAuthCode": false,
    "preSignUpUrl": "https://store-stage-usw2.viveport.com/store/setup/plans"
  };
              
  var initConfig = {
    appid: authConfigs.clientId,
    scope: "email birthday"
  };

  window.HTCAccount.init(initConfig);
  window.HTCAccount.getLoginStatus(function(resp) {
     if (resp.status === 'connected') {
        // already have authe key, and should be able to fetch user auth info
        console.log(resp.authResponse);
        
        window.HTCAccount.getProfileV3(function(data) {
          // get user profile JSON from data parameter, 
          // please check API specification related section for detail
        });
     } else {
       // this may indicate user is not logged in yet, or unverified user
       // so require user to re-run login/sign-up flow
       
       // trigger login/sign-up flow by redirectByAuthConfig
       window.HTCAccount.redirectByAuthConfig(authConfigs);
     }
  });
</script>
<script src="https://account-stage.htcvive.com/htcaccount.js"></script>
```

