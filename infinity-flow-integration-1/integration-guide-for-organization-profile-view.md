# Integration Guide for Organization Profile View

This page provide information to reuse HTCAccount web SSO SDK for infinity flow with organization profile view. Check sample code in last section for synchronous mode.

Please follow below API call spec, we also provide a sample code to follow. **Use STAGE domain for development and testing, but please config to PROD domain for production:**

### HTCAccountHost env

| ENV | Resource Domain |
| :--- | :--- |
| STAGE | ​[https://account-stage.htcvive.com](https://account-stage.htcvive.com/)​ |
| PRDO | ​[https://account.htcvive.com](https://account-stage.htcvive.com/)​ |
| TEST | ​[https://cstest.dev.usw2.cs-htc.co](https://cstest.dev.usw2.cs-htc.co/infinity/lib.js)​ |

### HTCProfileDefaultHost env

| ENV | Resource Domain |
| :--- | :--- |
| STAGE | [https://account-profile-stage.htcvive.com](https://account-profile-stage.htcvive.com) |
| PROD | [https://account-profile.htcvive.com](https://account-profile.htcvive.com) |
| TEST | [https://profiletest.htcwowdev.com](https://profiletest.htcwowdev.com) |

### HTCOrgProfileDefaultHost env

| ENV | Resource Domain |
| :--- | :--- |
| STAGE | [https://account-stage-usw2.viveport.com](https://account-stage-usw2.viveport.com) |
| PROD | [https://account.viveport.com](https://account.viveport.com) |
| TEST | [https://business-account-qa.htcwowdev.com](https://business-account-qa.htcwowdev.com) |

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

## Launch with Organization Profile View

{% hint style="info" %}
To enable organization profile view, must bring `+org-view`, `-promotion`, and `logo-htc` in `viewToggles` list field.

Then call `window.HTCAccount.redirectByAuthConfig`with config parameter, please check below link for auth config set up:

{% page-ref page="auth-configs-for-sso-v2.md" %}
{% endhint %}

### Example Auth Configs & Bootstrapping

#### General Organization Flow

This flow will only have first page with basic fields.

```javascript
// htcaccount.js is loaded
// window.HTCAccount.init already set 

var minConfig = {
    "clientId": "cc955ffe-b086-480c-84a3-42818f13839b",
    "redirectionUrl": "https://store-stage-usw2.viveport.com",
    "flow": "infinity",
    "initView": "sign-in",
    "viewToggles": ["+org-view", "-promotion", "logo-htc"],
    "requireAuthCode": false
  };
  
window.HTCAccount.redirectByAuthConfig(minConfig);
```

#### Advanced Organization Flow

This flow will have second page with **organization avatar, organization website url,** **organization phone number.**

```javascript
//If you want to trigger advanced org flow, you must include these elements as below:
//["+advanced-org-view","+org-view"]

//If you want to enable invitation mode, you should add prefillEmail field.
// `prefillEmail` field is optional. 

var minConfig = {
    "clientId": "cc955ffe-b086-480c-84a3-42818f13839b",
    "redirectionUrl": "https://store-stage-usw2.viveport.com",
    "flow": "infinity",
    "initView": "sign-in",
    "viewToggles": ["+org-view","+advanced-org-view", "-promotion", "logo-htc"],
    "requireAuthCode": false,
    "prefillEmail":"email@address.com"
  };
  
window.HTCAccount.redirectByAuthConfig(minConfig);
```

## Trigger Organization Profile On-demand Page

If client need to trigger on-demand page of create-org-profile flow, trigger the below API.

```javascript
//is_advanced_org_flow default is false.
//If client want to trigger advanced org view, the flag should be set true.
window.HTCAccount.createOrgProfileV2(is_advanced_org_flow);
```

Please ensure this flow need user already signed-in with auth key to complete flow, so that client should **ensure the `authkey` which is exist and valid**. If the `authkey` is invalid, the on-demand page will carry failed status with error code to **origin\_url** immediately. The callback status and error code is showed as below:

| Status | Code | Description |
| :--- | :--- | :--- |
| succeed | null | Create org profile successfully. |
| succeed | 4003 | Org profile is exist so that we don’t need to redirect to on-demand page. |
| failed | 9999 | Service error. |
| failed | 4002 | AuthKey is expired or invalid. |
| failed | 4004 | Failed to create org profile. |

It's response format would be as follow:

```javascript
https://${origin_url}?status=succeed
https://${origin_url}?status=failed&code=9999
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
    "clientId": "cc955ffe-b086-480c-84a3-42818f13839b",
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
        window.HTCAccount.getProfileV4(["username","backupEmail"],function(data) {
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

