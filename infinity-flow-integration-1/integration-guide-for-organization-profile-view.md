# Integration Guide for Organization Profile View

This page provide information to reuse HTCAccount web SSO SDK for infinity flow with organization profile view. Check sample code in last section for synchronous mode.

## Launch with Organization Profile View

{% hint style="info" %}
To enable organization profile view, must bring `+org-view`, `-promotion`, and `logo-htc` in `viewToggles` list field.

Then call `window.HTCAccount.redirectByAuthConfig`with config parameter, please check below link for auth config set up:

{% page-ref page="auth-configs-for-sso-v2.md" %}
{% endhint %}

### Example Auth Configs & Bootstrapping

```javascript
// htcaccount.js is loaded
// window.HTCAccount.init already set 

var minConfig = {
    "clientId": "33035df5-7ddd-4417-a20a-e56722489550",
    "redirectionUrl": "https://store-stage-usw2.viveport.com",
    "flow": "infinity",
    "initView": "sign-in",
    "viewToggles": ["+org-view", "-promotion", "logo-htc"],
    "requireAuthCode": false
  };
  
window.HTCAccount.redirectByAuthConfig(minConfig);
```

## Sample Code For Integrating Organization Profile View

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
    "viewToggles": ["+org-view", "-promotion", "logo-htc"],
    "requireAuthCode": false,
    "preSignUpUrl": "https://store-stage-usw2.viveport.com/store/setup/plans"
  };
              
  var initConfig = {
    appid: authConfigs.clientId,
    scope: "email birthday",
    authorities: ""
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

