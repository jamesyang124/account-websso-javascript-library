# Auth Configs for SSO State Parameter

OAuth state parameter by standard is to allow client to pass its transient data in flow. HTC Account SSO also leverage this parameter for UI/UX customization. Below listed JSON fields are preserve fields and should be configured in login flow initialization, ex: **HTCAccount.login** api call for web client, or **OAuth authorize** api call for server client.

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

## Definition of Auth Config Fields

| Config JSON     | Require  | Value Type  | Value                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| --------------- | -------- | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sessionId       | optional | String      | BI session id, if not carried, will generate for it, **please reuse this BI session id if present.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| clientId        | required | String      | OAuth client id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| redirectionUrl  | required | String      | Client's redirection url, no URL-encoded required, **please ensure the trailing slash whether required from OAuthSetting**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| requireAuthCode | required | Boolean     | Indicate whether client need authorization code instead of access token, _is boolean type either **true** or **false**_                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| flow            | required | String      | constant value **infinity**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| initView        | required | String      | <p>for landing view, default should set to <strong>sign-in,</strong> or value as:<br><strong>1. sign-in</strong><br><strong>2. sign-up</strong><br><strong>3.</strong> <strong>auth-pincode</strong><br><strong>4. auth-eth</strong><br><strong>5. auth-migu</strong><br><strong>6. sign-up-mvr</strong></p>                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| viewToggles     | required | String List | <p>Empty array, or support any of:</p><ol><li><code>"-signup"</code></li><li><code>"-sign-in"</code></li><li><code>"-promotion"</code></li><li><code>"+org-view"</code></li><li><code>"logo-htc"</code></li><li><code>"+advanced-org-view"</code></li><li><code>"+signup"</code></li><li><code>"+signupbyphone"</code></li><li><code>"+signupbyemail"</code></li><li><code>"-signupswitch"</code></li><li><code>"-social-profile"</code></li><li><code>"-rememberacct"</code></li><li><code>"-forgotpwd"</code></li><li><code>"+create-org"</code></li></ol><p>Sample value: <code>["-signup", "-sign-in", "-promotion", "+org-view", "logo-htc"]</code></p><p></p><p>Please consult HTC Account Team to learn how to config toggles for customized UI view</p> |
| preSignUpUrl    | optional | String      | Indicate whether create account flow should redirect to this url first instead of switching to create account form. Only supply if needed, or may lead redirection loop. **Empty String is not allowed**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| postSignUpUrl   | optional | String      | if a user finish the account flow, the page will redirect to the path of postSignUpUrl immediately. **Empty String is not allowed.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| scopes          | required | String List | scope array, so the auth key can be constraint to certain scopes. Currently only preset for VIVEPORT Desktop                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| cookieConsent   | optional | String List | <p>An array of string to <strong>overwrite</strong> default cookie consents in account enroll flow.</p><p></p><p>For Example:<br></p><p><code>["performance", "functional"]</code></p><p></p><p>If no value specified for this property, the default value will be empty array and is the same as not carry this property, which means user does not consent to send 3rd party cookies</p><p></p><p>Currently support toggles:<br></p><ol><li><strong>performance</strong></li><li><strong>functional</strong></li><li><strong>targeting</strong></li><li><strong>social-media</strong></li></ol>                                                                                                                                                               |
| prefillEmail    | optional | String      | string text valid email format, ex: "email@address.com", this is used for org user invitation email flow.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| authorities     | optional | String      | <p>HTC SSO authentication strategy. Could be combination of below listed values, delimited by " " space.<br></p><ol><li><strong>htc.com</strong></li><li><strong>google.com</strong></li><li><strong>qq.com</strong></li><li><strong>steam.com</strong></li><li><strong>docomo.com</strong> </li><li><strong>walletconnect.com</strong></li><li><strong>metamask.com</strong></li></ol><p><strong></strong></p>                                                                                                                                                                                                                                                                                                                                                 |

### Authorities field

_You can omit this property and the WEBSSO library will set the default value based on user's GEO IP detection._

For GEO ip is located in non-China region, **authorities** default value is:

```javascript
authorities: "google.com facebook.com htc.com steam.com"
```

For GEO ip is located in China:

```javascript
authorities: "weibo.com qq.com htc.com"
```

If HTC WEBSSO library failed to get user's GEO ip, then the default value is `htc.com`.

You can remove specific authority by **prefixed minus sign, but the UI layout may not dynamically changed**.

```javascript
// Assume client is located in China
// Remove QQ social sign in button:
authorities: "weibo.com -qq.com htc.com"

// Or remove default authorities instead.
// Only permit Weibo and HTC sign-in:
authorities: "-weibo.com"
```

**Caveat!**

> In China region, `Facebook` and `Google` is not valid options for authorities. In Non-China region, `QQ` and `Weibo` also are invalid options.

### Example

```javascript
var authConfigs = {
  "sessionId": "4686d579-4176-46fc-8636-660643cf1f8f",
  "clientId": config.appid,
  "flow": "infinity",
  "initView": "sign-in",
  "scopes": ["email"],
  "redirectionUrl": "https://some-domain.example.com/some-path/",
  "viewToggles": ["-forgotpwd"],
  "requireAuthCode": false,
  "prefillEmail": "someUserEmail@example.com",
  "authorities": "htc.com google.com"
};

// HTCAccount.init already called.
HTCAccount.login(() => {}, {
  type: 'redreict',
  next_url: location.pathname,
  state: JSON.stringify(authConfigs)
});
```
