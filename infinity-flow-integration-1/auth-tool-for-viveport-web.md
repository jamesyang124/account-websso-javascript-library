# Auth Tool For VIVEPORT Web

## Infinity Auth Javascript Library

You can inject the script for different environment to get `_htcsso`  auth key cookie from your infinity callback. **All domains in each account's website url from this page should subject to change by environment**:

| ENV | RESOURCE PATH |
| :--- | :--- |
| PROD | [https://account.htcvive.com/infinity/lib.js](https://account.htcvive.com/infinity/tools.js) |
| STAGE | [https://account-stage.htcvive.com/infinity/lib.js](https://account-stage.htcvive.com/infinity/tools.js) |
| TEST | [https://cstest.dev.usw2.cs-htc.co/infinity/lib.js](https://cstest.dev.usw2.cs-htc.co/infinity/lib.js) |

{% hint style="warning" %}
During the infinity sign-in/up flow, **please do not modify URL fragment for access token passing** before loading javascript library. Also the related query param `aid`, `cid`, `se` will be reserved for account team's response usage.
{% endhint %}

{% hint style="info" %}
Beside these params, `state` param will have full life cycle from first OAuth/Authorize API to the end of client's callback url, you could inspect the caller information based on this field, and extend it **but not alter any existed fields for data integrity**
{% endhint %}

## Basic Usage

To use this library, include the script in your page:

```markup
<script src="https://account.htcvive.com/infinity/lib.js"></script>
```

This will autoload your current redirection url's query parameter \(`window.location.search`\)and hash \(`window.location.hash`\), so the proper auth information will be injected.

## Start Infinity Auth

To run the infinity flow, please call `window.InfinityAuth.redirectToAuthUrl()`  with config parameter:

| Config JSON  | Require | Value |
| :--- | :--- | :--- |
| sessionId | optional | BI session id, if not carried, will generate for it, **please reuse this BI session id if present.** |
| clientId | true | OAuth client id |
| redirectionUrl | true | Client's redirection url, no URL-encoded required, **please ensure the trailing slash whether required from OAuthSetting** |
| requireAuthCode | true | Indicate whether client need authorization code instead of access token |
| flow | true | constant value **infinity** |
| initView | true | either **sign-in** or **sign-up** for first rendered page, default should set to **sign-in** |
| viewToggles | true | Empty array, or as one of element in`["-sign-in", "-promotion", "+org-view", "logo-htc"]` for VIVE Video |
| preSignUpUrl | optional | Indicate whether create account flow should redirect to this url first instead of switching to create account form. Only supply if needed, or may lead redirection loop. **Empty String is not allowed** |
| postSignUpUrl | optional | if a user finish the account flow, the page will redirect to the path of postSignUpUrl immediately. **Empty String is not allowed.** |
| scopes | optional | scope array, so the auth key can be constraint to certain scopes. Currently only preset for VIVEPORT Desktop |

### Example For VIVEPORT Web Normal Sign In

```javascript
// https://account.htcvive.com/infinity/lib.js is loaded

var config = {
  "sessionId": "c4641f40-e6ee-44af-92bf-a3f94425ef0c",
  "clientId": "33035df5-7ddd-4417-a20a-e56722489550",
  "redirectionUrl": "https://mock-vivepoer-web.site.com/",
  "requiredAuthCode": false,
  "flow": "infinity",
  "initView": "sign-in",
  "viewToggles": [],
  "preSignUpUrl": "https://select-plan.com",
  "postSignUpUrl": "https://country-setting.com"
};

window.InfinityAuth.redirectToAuthUrl(config);
```

### Example For VIVEPORT Web Start Free Trial

```javascript
// https://account.htcvive.com/infinity/lib.js is loaded

var config = {
  "sessionId": "c4641f40-e6ee-44af-92bf-a3f94425ef0c",
  "clientId": "33035df5-7ddd-4417-a20a-e56722489550",
  "redirectionUrl": "https://mock-vivepoer-web.site.com/",
  "requiredAuthCode": false,
  "flow": "infinity",
  "initView": "sign-up",
  "viewToggles": [],
  "preSignUpUrl": "https://select-plan.com",
    "postSignUpUrl": "https://country-setting.com"
};

window.InfinityAuth.redirectToAuthUrl(config);
```

## Continue Infinity Auth By Auth Config

For VIVEPORT web integrated page, please call `window.InfinityAuth.redirectByAuthConfig()` which will fetch query parameter `auth_config` to continue the flow:

```javascript
// https://account.htcvive.com/infinity/lib.js is loaded

window.InfinityAuth.redirectByAuthConfig();
// should redirect to OAuth/Authorize url
```

{% hint style="warning" %}
 if you redirect to another page which **erased the** `auth_config` **query parameters**, please persist it before redirect, then call `window.InfinityAuth.redirectToAuthUrl(config)`with persisted`config`
{% endhint %}

## Finish Infinity Auth By Auth Config

In order to finish the infinity auth flow at the last `/OAuth/Authorize` call in country setting/payment page, **we currently suggest include WEB SSO javascript library**, so the auth key's data integrity could be preserved during continued flow in VIVEPORT web:

```javascript
// https://account.htcvive.com/htcaccount.js is loaded
// assume already finish infinity sign-up from account page,
// and at country setting/payment page

window.HTCAccount.redirectByAuthConfig();
// will fire OAuth/Authorize request with payload based on auth_config query param
```

{% hint style="warning" %}
if you redirect to another page which **erased the** `auth_config` **query parameters**, please **url-decoded then decode base 64 then parsed string as JSON** and persist it before redirect, then call `window.HTCAccount.redirectByAuthConfig(configs)`with persisted`configs`
{% endhint %}

## Get Auth Info

You can get auth info from `_htcsso`  cookie, the parsed and decoded result could also be fetched from `window.InfinityAuth.authInfo` :

```javascript
// https://account.htcvive.com/infinity/lib.js is loaded
// assume already finish infinity sign-in or sign-up flow

window.InfinityAuth.authInfo;
// respond below response
{
  "au": "JvamWG+M9f....E8PCapTx4VWDahZjCGsX"
  "cid": "33035df5-7ddd-4417-a20a-e56722489550",
  "se": 1551718105793
  "life": 1200000,
  "sc": "",
  "uid": "d2a76b89-4dca-43fe-8675-d63e123b9241"
}
```

| Parameter Name | Description |
| :--- | :--- |
| au | access token for this client id |
| cid | OAuth client id |
| se | session expires at, as timestamp |
| uid | user account id |
| life | token expire time interval |
| sc | access token allowed scope |

## Get Auth Config

You might need to persist auth config during integrated page redirection, so before redirection, the `auth_config` query param from upstream account web will maintain all the information, you can fetch it by `window.InfinityAuth.config`:

```javascript
// https://account.htcvive.com/infinity/lib.js is loaded
// assume already finish infinity sign-in or sign-up flow

window.InfinityAuth.config;
{
  "sessionId": "c4641f40-e6ee-44af-92bf-a3f94425ef0c",
  "clientId": "33035df5-7ddd-4417-a20a-e56722489550",
  "redirectionUrl": "https://mock-vivepoer-web.site.com/",
  "requiredAuthCode": false,
  "flow": "infinity",
  "initView": "sign-in",
  "viewToggles": [],
  "preSignUpUrl": "https://select-plan.com"
}
```

{% hint style="warning" %}
Once the auth config is persisted and page redirected, you should call`window.InfinityAuth.redirectToAuthUrl(config)`to continue infinity auth flow
{% endhint %}

## Clean Auth Cookie Example

It might have to clean the auth cookie to re-auth, then the `window.InfinityAuth.cleanAuthCookie()` will  help this without clumsy `document.cookie` operations:

```javascript
// https://account.htcvive.com/infinity/lib.js is loaded

window.InfinityAuth.cleanAuthCookie();
// this will remove _htcsso cookie
```

## 

