# Auth Configs for SSO State Parameter

OAuth state parameter by standard is to allow client to pass its transient data in flow. HTC Account SSO also leverage this parameter for UI/UX customization. Below listed JSON fields are preserve fields and should be configured in login flow initialization, ex: **HTCAccount.login** api call for web client, or **OAuth authorize** api call for server client.

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

## Definition of Auth Config Fields

<table>
  <thead>
    <tr>
      <th style="text-align:left">Config JSON</th>
      <th style="text-align:left">Require</th>
      <th style="text-align:left">Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">sessionId</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">BI session id, if not carried, will generate for it, <b>please reuse this BI session id if present.</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">clientId</td>
      <td style="text-align:left">true</td>
      <td style="text-align:left">OAuth client id</td>
    </tr>
    <tr>
      <td style="text-align:left">redirectionUrl</td>
      <td style="text-align:left">true</td>
      <td style="text-align:left">Client&apos;s redirection url, no URL-encoded required, <b>please ensure the trailing slash whether required from OAuthSetting</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">requireAuthCode</td>
      <td style="text-align:left">true</td>
      <td style="text-align:left">Indicate whether client need authorization code instead of access token</td>
    </tr>
    <tr>
      <td style="text-align:left">flow</td>
      <td style="text-align:left">true</td>
      <td style="text-align:left">constant value <b>infinity</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">initView</td>
      <td style="text-align:left">true</td>
      <td style="text-align:left">either <b>sign-in</b> or <b>sign-up</b> for first rendered page, default should
        set to <b>sign-in</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">viewToggles</td>
      <td style="text-align:left">true</td>
      <td style="text-align:left">
        <p>Empty array, or support any of:</p>
        <ol>
          <li><code>&quot;-signup&quot;</code>
          </li>
          <li><code>&quot;-sign-in&quot;</code>
          </li>
          <li><code>&quot;-promotion&quot;</code>
          </li>
          <li><code>&quot;+org-view&quot;</code>
          </li>
          <li><code>&quot;logo-htc&quot;</code>
          </li>
          <li><code>&quot;+advanced-org-view&quot;</code>
          </li>
          <li><code>&quot;+signup&quot;</code>
          </li>
          <li><code>&quot;+signupbyphone&quot;</code>
          </li>
          <li><code>&quot;+signupbyemail&quot;</code>
          </li>
          <li><code>&quot;-signupswitch&quot;</code>
          </li>
          <li><code>&quot;-social-profile&quot;</code>
          </li>
          <li><code>&quot;-rememberacct&quot;</code>
          </li>
          <li><code>&quot;-forgotpwd&quot;</code>
          </li>
          <li><code>&quot;+create-org&quot;</code>
          </li>
        </ol>
        <p>Sample value: <code>[&quot;-signup&quot;, &quot;-sign-in&quot;, &quot;-promotion&quot;, &quot;+org-view&quot;, &quot;logo-htc&quot;]</code>
        </p>
        <p></p>
        <p>Please consult HTC Account Team to learn how to config toggles for customized
          UI view</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">preSignUpUrl</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">Indicate whether create account flow should redirect to this url first
        instead of switching to create account form. Only supply if needed, or
        may lead redirection loop. <b>Empty String is not allowed</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">postSignUpUrl</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">if a user finish the account flow, the page will redirect to the path
        of postSignUpUrl immediately. <b>Empty String is not allowed.</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">scopes</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">scope array, so the auth key can be constraint to certain scopes. Currently
        only preset for VIVEPORT Desktop</td>
    </tr>
    <tr>
      <td style="text-align:left">cookieConsent</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">
        <p>An array of string to <b>overwrite </b>default cookie consents in account
          enroll flow.</p>
        <p></p>
        <p>For Example:
          <br />
        </p>
        <p><code>[&quot;performance&quot;, &quot;functional&quot;]</code>
        </p>
        <p></p>
        <p>If no value specified for this property, the default value will be empty
          array and is the same as not carry this property, which means user does
          not consent to send 3rd party cookies</p>
        <p></p>
        <p>Currently support toggles:
          <br />
        </p>
        <ol>
          <li><b>performance</b>
          </li>
          <li><b>functional</b>
          </li>
          <li><b>targeting</b>
          </li>
          <li><b>social-media</b>
          </li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">prefillEmail</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">string text valid email format, ex: &quot;email@address.com&quot;, this
        is used for org user invitation email flow.</td>
    </tr>
    <tr>
      <td style="text-align:left">authorities</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">
        <p>HTC SSO authentication strategy. Could be combination of below listed
          values, delimited by &quot; &quot; space.
          <br />
        </p>
        <ol>
          <li><b>htc.com</b>
          </li>
          <li><b>google.com</b>
          </li>
          <li><b>qq.com</b>
          </li>
          <li><b>steam.com</b>
          </li>
        </ol>
      </td>
    </tr>
  </tbody>
</table>

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

