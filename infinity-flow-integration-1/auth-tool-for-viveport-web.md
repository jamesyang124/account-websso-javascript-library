# Auth Tool For VIVEPORT Web

## Infinity Auth Javascript Library

You can inject the script during enroll flow for different environment to get `_htcsso`  auth key cookie from your infinity callback. **All domains in each account's website url from this page should subject to change by environment**:

### HTCAccountHost env

| ENV   | Resource Domain                                                                          |
| ----- | ---------------------------------------------------------------------------------------- |
| STAGE | ​[https://account-stage.htcvive.com](https://account-stage.htcvive.com/)​                |
| PRDO  | ​[https://account.htcvive.com](https://account-stage.htcvive.com/)​                      |
| TEST  | ​[https://cstest.dev.usw2.cs-htc.co](https://cstest.dev.usw2.cs-htc.co/infinity/lib.js)​ |

### HTCProfileDefaultHost env

| ENV   | Resource Domain                                                                        |
| ----- | -------------------------------------------------------------------------------------- |
| STAGE | [https://account-profile-stage.htcvive.com](https://account-profile-stage.htcvive.com) |
| PROD  | [https://account-profile.htcvive.com](https://account-profile.htcvive.com)             |
| TEST  | [https://profiletest.htcwowdev.com](https://profiletest.htcwowdev.com)                 |

### HTCOrgProfileDefaultHost env

| ENV   | Resource Domain                                                                        |
| ----- | -------------------------------------------------------------------------------------- |
| STAGE | [https://account-stage-usw2.viveport.com](https://account-stage-usw2.viveport.com)     |
| PROD  | [https://account.viveport.com](https://account.viveport.com)                           |
| TEST  | [https://business-account-qa.htcwowdev.com](https://business-account-qa.htcwowdev.com) |

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% hint style="warning" %}
During the infinity sign-in/up flow, **please do not modify URL fragment for access token passing** before loading javascript library. Also the related query param `aid`, `cid`, `se` will be reserved for account team's response usage.
{% endhint %}

{% hint style="info" %}
Beside these params, `state` param will have full life cycle from first OAuth/Authorize API to the end of client's callback url, you could inspect the caller information based on this field, and extend it **but not alter any existed fields for data integrity**
{% endhint %}

## Basic Usage

To use this library, include the script in your page:

{% hint style="danger" %}
Please be aware this script will skip 3rd party cookie detection block page, only if you also load **htcaccount.js** so it would work. This script is target for specific enroll flow enhancement. As flow trigger point, please use **htcaccount.js**
{% endhint %}

```markup
<script src="https://account.htcvive.com/infinity/lib.js"></script>
```

This will autoload your current redirection url's query parameter (`window.location.search`)and hash (`window.location.hash`), so the proper auth information will be injected.

## Start Infinity Auth&#x20;

{% hint style="info" %}
To run the infinity flow, please call `window.InfinityAuth.redirectToAuthUrl()` with config parameter, please check below link for auth config set up:
{% endhint %}

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
&#x20;if you redirect to another page which **erased the** `auth_config` **query parameters**, please persist it before redirect, then call `window.InfinityAuth.redirectToAuthUrl(config)`with persisted`config`
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

| Parameter Name | Description                      |
| -------------- | -------------------------------- |
| au             | access token for this client id  |
| cid            | OAuth client id                  |
| se             | session expires at, as timestamp |
| uid            | user account id                  |
| life           | token expire time interval       |
| sc             | access token allowed scope       |

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
