# Auth Configs for Infinity Flow

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

## Definition of Auth Config Fields

| Config JSON | Require | Value |
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

