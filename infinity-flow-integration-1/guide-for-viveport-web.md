# Integration Guide for VIVEPORT Web

For VIVEPORT web, please follow below API call with some customized variables (annotate with **@param**), we also provide an example to follow for VIVEPORT Web at the end of this section.

{% hint style="success" %}
To quick access, we provide a light-weight javascript lib for integration. Please check below link if you don't need to customized the whole flow from the ground up.
{% endhint %}

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% hint style="danger" %}
For MIXPANEL BI log sending, please **MUST** carry all MIXPANEL data event fields, o.w. you may omit those fields if tend to not send MIXPANEL BI log.
{% endhint %}

| State JSON Parameter Name | Require  | Value                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| clientId                  | true     | @param { VIVEPORT web's OAuth client id }                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| redirectionUrl            | true     | @param { URL encoded account FE wrapped url }                                                                                                                                                                                                                                                                                                                                                                                                                              |
| flow                      | true     | "infinity"                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| initView                  | true     | "sign-in"                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| viewToggles               | true     | \[]                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| preSignUpUrl              | optional | @param { VIVEPORT web's select plan url }                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| postSignUpUrl             | optional | @param {VIVEPORT web's country setting url}                                                                                                                                                                                                                                                                                                                                                                                                                                |
| requireAuthCode           | true     | false                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| sessionId                 | optional | <p><strong>This field is also used for MIXPANEL data event.</strong></p><p><strong>MUST carry all other MIXPANEL data event fields to trigger MIXPANEL log sending.</strong></p><p><strong></strong></p><p>BI session id, if not carried, will generate for it, <strong>please reuse this BI session id if present.</strong></p>                                                                                                                                           |
| cookieConsent             | optional | <p>an array of strings to toggle different cookie consent in current flow.</p><p>For Example:</p><p><code>["performance", "functional"]</code></p><p>If you don't pass this information, the default value will be <code>["performance","functional","targeting","social-media"]</code>.</p><p></p><p>Currently support toggle string:</p><p></p><p>performance</p><p>functional</p><p>targeting</p><p>social-media</p>                                                    |
| distinctId                | optional | <p><strong>MIXPANEL distinct id data event field</strong>.</p><p><strong>MUST carry all other MIXPANEL data event fields to trigger MIXPANEL log sending.</strong></p><p><strong></strong></p><p>This parameter is used to identify user session so that we could chain the behavior from upstream client to Account WEBSSO via <strong>MIXPANEL</strong> distinctId.</p>                                                                                                  |
| rootClient                | optional | <p><strong>MIXPANEL root client data event field.</strong> </p><p><strong>MUST carry all other MIXPANEL data event fields to trigger MIXPANEL log sending.</strong></p><p><strong></strong></p><p>The first <strong>upstream</strong> client which is triggered by user.<br>No matter how many middle clients which triggered via several flows, the rootClient is always the initiate client. </p><p><strong>The value should be client name instead of UUID</strong></p> |
| triggerClient             | optional | <p><strong>MIXPANEL trigger client data event field.</strong> </p><p><strong>MUST carry all other MIXPANEL data event fields to trigger MIXPANEL log sending.</strong></p><p><strong></strong></p><p>The client which trigger WEBSSO SDK. If user open PC-Client and do sign-up flow via VIVEPORT Store, the trigger client should be VIVEPORT Store. <strong>The value should be client name instead of UUID</strong></p>                                                 |
| flowEntryPoint            | optional | <p><strong>MIXPANEL flow entry point data event field.</strong> </p><p><strong>MUST carry all other MIAXPANEL data event fields to trigger MIXPANEL log sending.</strong></p><p><strong></strong></p><p>The value is given by root client, which described as UI element to initiate the flow. Account WEBSSO SDK just pass this value to record the data value of flow entry point.</p>                                                                                   |
| prefillEmail              | optional | string text valid email format, ex: "email@address.com", this is used for org user invitation email flow.                                                                                                                                                                                                                                                                                                                                                                  |

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
    "postSignUpUrl": "https://viveport-web-mock-site.com/store/setup/country",
    "cookieConsent":["performance", "functional"],
    "distinctId":"b02f8000-32b3-11ea-aec2-2e728ce88125",
    "prefillEmail":"email@address.com",
    "triggerClient":"Infinity Web Store",
    "rootClient":"Infinity VR Steam",
    "flowEntryPoint":"Welcome Page"
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
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%2C%22postSignUpUrl%22%3A%22%22%2C%22cookieConsent%22%3A%5B%22performance%22%2C%22functional%22%5D%7D
&response_type=token
&immediate=FALSE
&client_id=33035df5-7ddd-4417-a20a-e56722489550
```
