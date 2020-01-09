# Integration Guide for VIVEPORT Web

For VIVEPORT web, please follow below API call with some customized variables \(annotate with **@param**\), we also provide an example to follow for VIVEPORT Web at the end of this section.

{% hint style="success" %}
To quick access, we provide a light-weight javascript lib for integration. Please check below link if you don't need to customized the whole flow from the ground up.

{% page-ref page="auth-tool-for-viveport-web.md" %}
{% endhint %}

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

<table>
  <thead>
    <tr>
      <th style="text-align:left">State JSON Parameter Name</th>
      <th style="text-align:left">Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">clientId</td>
      <td style="text-align:left">@param { VIVEPORT web&apos;s OAuth client id }</td>
    </tr>
    <tr>
      <td style="text-align:left">redirectionUrl</td>
      <td style="text-align:left">@param { URL encoded account FE wrapped url }</td>
    </tr>
    <tr>
      <td style="text-align:left">flow</td>
      <td style="text-align:left">&quot;infinity&quot;</td>
    </tr>
    <tr>
      <td style="text-align:left">initView</td>
      <td style="text-align:left">&quot;sign-in&quot;</td>
    </tr>
    <tr>
      <td style="text-align:left">viewToggles</td>
      <td style="text-align:left">[]</td>
    </tr>
    <tr>
      <td style="text-align:left">preSignUpUrl</td>
      <td style="text-align:left">@param { VIVEPORT web&apos;s select plan url }</td>
    </tr>
    <tr>
      <td style="text-align:left">postSignUpUrl</td>
      <td style="text-align:left">@param {VIVEPORT web&apos;s country setting url}</td>
    </tr>
    <tr>
      <td style="text-align:left">requireAuthCode</td>
      <td style="text-align:left">false</td>
    </tr>
    <tr>
      <td style="text-align:left">sessionId</td>
      <td style="text-align:left">BI session id, if not carried, will generate for it,<b> please reuse this BI session id if present.</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">cookieConsent</td>
      <td style="text-align:left">
        <p>an array of strings to toggle different cookie consent in current flow.</p>
        <p>For Example:</p>
        <p><code>[&quot;performance&quot;, &quot;functional&quot;]</code>
        </p>
        <p>If you don&apos;t pass this information, the default value will be <code>[&quot;performance&quot;,&quot;functional&quot;,&quot;targeting&quot;,&quot;social-media&quot;]</code>.</p>
        <p></p>
        <p>Currently support toggle string:</p>
        <p></p>
        <p>performance</p>
        <p>functional</p>
        <p>targeting</p>
        <p>social-media</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">distinctId</td>
      <td style="text-align:left">MIXPANEL distinctID. This parameter is used to identify user so that we
        could find the behavior from client side to Account WEBSSO via distinctId.</td>
    </tr>
  </tbody>
</table>#### Example of `state` param

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
    "distinctId":"b02f8000-32b3-11ea-aec2-2e728ce88125"
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

