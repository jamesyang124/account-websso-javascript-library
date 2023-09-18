# Auth Configs for SSO State Parameter

OAuth state parameter by standard is to allow client to pass its transient data in flow. HTC Account SSO also leverage this parameter for UI/UX customization. Below listed JSON fields are preserve fields and should be configured in login flow initialization, ex: **HTCAccount.login** api call for web client, or **OAuth authorize** api call for server client.

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

## Definition of Auth Config Fields

<table data-header-hidden><thead><tr><th>Config JSON</th><th width="150">Require</th><th width="150">Value Type</th><th>Value</th></tr></thead><tbody><tr><td>Config JSON</td><td>Require</td><td>Value Type</td><td>Value</td></tr><tr><td>sessionId</td><td>optional</td><td>String</td><td>BI session id, if not carried, will generate for it, <strong>please reuse this BI session id if present.</strong></td></tr><tr><td>clientId</td><td>required</td><td>String</td><td>OAuth client id</td></tr><tr><td>redirectionUrl</td><td>required</td><td>String</td><td>Client's redirection url, no URL-encoded required, <strong>please ensure the trailing slash whether required from OAuthSetting</strong></td></tr><tr><td>requireAuthCode</td><td>required</td><td>Boolean</td><td>Indicate whether client need authorization code instead of access token, <em>is boolean type either <strong>true</strong> or <strong>false</strong></em></td></tr><tr><td>flow</td><td>required</td><td>String</td><td>constant value <strong>infinity</strong></td></tr><tr><td>initView</td><td>required</td><td>String</td><td><p>for landing view, default should set to <strong>sign-in,</strong> or value as:<br><strong>1. sign-in</strong><br><strong>2. sign-up</strong><br><strong>3.</strong> <strong>auth-pincode</strong><br><strong>4. auth-eth</strong><br><strong>5. auth-migu</strong><br><strong>6. sign-up-mvr</strong></p><p><strong>7. auth-list</strong></p></td></tr><tr><td>viewToggles</td><td>required</td><td>String List</td><td><p>Empty array, or support any of:</p><ol><li><code>"-signup"</code></li><li><code>"-sign-in"</code></li><li><code>"-promotion"</code></li><li><code>"+org-view"</code></li><li><code>"logo-htc"</code></li><li><code>"+advanced-org-view"</code></li><li><code>"+signup"</code></li><li><code>"+signupbyphone"</code></li><li><code>"+signupbyemail"</code></li><li><code>"-signupswitch"</code></li><li><code>"-social-profile"</code></li><li><code>"-rememberacct"</code></li><li><code>"-forgotpwd"</code></li><li><code>"+create-org"</code></li><li><code>"verifybyurl"</code></li></ol><p>Sample value: <code>["-signup", "-sign-in", "-promotion", "+org-view", "logo-htc"]</code></p><p></p><p>Please consult HTC Account Team to learn how to config toggles for customized UI view</p></td></tr><tr><td>preSignUpUrl</td><td>optional</td><td>String</td><td>Indicate whether create account flow should redirect to this url first instead of switching to create account form. Only supply if needed, or may lead redirection loop. <strong>Empty String is not allowed</strong></td></tr><tr><td>postSignUpUrl</td><td>optional</td><td>String</td><td>if a user finish the account flow, the page will redirect to the path of postSignUpUrl immediately. <strong>Empty String is not allowed.</strong></td></tr><tr><td>scopes</td><td>required</td><td>String List</td><td>scope array, so the auth key can be constraint to certain scopes. Currently only preset for VIVEPORT Desktop</td></tr><tr><td>cookieConsent</td><td>optional</td><td>String List</td><td><p>An array of string to <strong>overwrite</strong> default cookie consents in account enroll flow.</p><p></p><p>For Example:<br></p><p><code>["performance", "functional"]</code></p><p></p><p>If no value specified for this property, the default value will be empty array and is the same as not carry this property, which means user does not consent to send 3rd party cookies</p><p></p><p>Currently support toggles:<br></p><ol><li><strong>performance</strong></li><li><strong>functional</strong></li><li><strong>targeting</strong></li><li><strong>social-media</strong></li></ol></td></tr><tr><td>prefillEmail</td><td>optional</td><td>String</td><td>string text valid email format, ex: "email@address.com", this is used for org user invitation email flow.</td></tr><tr><td>authorities</td><td>optional</td><td>String</td><td><p>HTC SSO authentication strategy. Could be combination of below listed values, delimited by " " space.<br></p><ol><li><strong>htc.com</strong></li><li><strong>google.com</strong></li><li><strong>qq.com</strong></li><li><strong>weibo.com</strong></li><li><strong>steam.com</strong></li><li><strong>docomo.com</strong> </li><li><strong>walletconnect.com</strong></li><li><strong>metamask.com</strong></li><li><strong>azuread.com</strong></li></ol><p></p></td></tr><tr><td>viewPort</td><td>optional</td><td>string</td><td>scalable viewport text value, range from > 0.0 and &#x3C; 2.0. Currently value 1.335 should fit in for width 800 web view in-vr apps.</td></tr></tbody></table>

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
