# Integration Guide for VIVEPORT Web

For VIVEPORT web, please follow below API call with some customized variables \(annotate with **@param**\), we also provide an example to follow for VIVEPORT Web at the end of this section.

{% hint style="success" %}
To quick access, we provide a light-weight javascript lib for integration. Please check below link if you don't need to customized the whole flow from the ground up.

{% page-ref page="auth-tool-for-viveport-web.md" %}
{% endhint %}

| State JSON Parameter Name | Value |
| :--- | :--- |
| clientId | @param { VIVEPORT web's OAuth client id } |
| redirectionUrl | @param { URL encoded account FE wrapped url } |
| flow | "infinity" |
| initView | "sign-in" |
| viewToggles | \[\] |
| preSignUpUrl | @param { VIVEPORT web's select plan url } |
| postSignUpUrl | @param {VIVEPORT web's country setting url} |
| requireAuthCode | false |
| sessionId | BI session id, if not carried, will generate for it, **please reuse this BI session id if present.** |

#### Example of `state` param

```javascript
var state = {
    "sessionId": "c4641f40-e6ee-44af-92bf-a3f94425ef0c",
    "clientId": "",
    "redirectionUrl": "",
    "flow": "infinity",
    "initView": "sign-up",
    "viewToggles": ["-sign-in"],
    "requiredAuthCode": false, 
    "preSignUpUrl": "https://viveport-web-mock-site.com/plan/selection",
    "postSignUpUrl": "https://viveport-web-mock-site.com/store/setup/country"
}
```

#### Example of `redirection_url` param

```javascript
// by javascript syntax
var redirectionUrl = 
"https://account.htcvive.com/htcaccount/callback/" +
`?client_id=${configs.clientId}` +
`&origin=${encodeURIComponent(configs.redirectionUrl)}` +
`&type=redirect`;
```

#### Example of composing OAuth Authorize URL

```javascript
var requestUrl = 
"https://account.htcvive.com/SS/Services/OAuth/Authorize" +
`?client_id=${encodeURIComponent(configs.clientId)}` +
`&response_type=token` +
`&immediate=FALSE` +
`&state=${encodeURIComponent(JSON.stringify(configs))}` +
`&redirection_url=${encodeURIComponent(redirectionUrl)}`;
```

### Example of OAuth Authorize API Request for VIVEPORT Web

VIVEPORT web should compose API to enable infinity authentication flow, note that `state` query parameter will also include `client_id` and `redirection_url` for message passing:

```bash
https://account.htcvive.com/SS/Services/OAuth/Authorize
?redirection_url=https%3A%2F%2Fcsdev.htcwowdev.com%2Fhtcaccount%2Fcallback%2F%3Fclient_id%3D33035df5-7ddd-4417-a20a-e56722489550%26origin%3Dhttps%253A%252F%252Fid-dev-websso.htcwowdev.com%252F19%252Fdev.html%26type%3Dredirect
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%2C%22postSignUpUrl%22%3A%22%22%7D
&response_type=token
&immediate=FALSE
&client_id=33035df5-7ddd-4417-a20a-e56722489550
```

