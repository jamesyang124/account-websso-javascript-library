# Auth Configs for Web SSO V2

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
      <td style="text-align:left">Empty array, or as one of element in<code>[&quot;-sign-in&quot;, &quot;-promotion&quot;, &quot;+org-view&quot;, &quot;logo-htc&quot;]</code> for
        VIVE Video</td>
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
      <td style="text-align:left">distinctId</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">MIXPANEL distinctID. This parameter is used to identify user so that we
        could find the behavior from client side to Account WEBSSO via distinctId.</td>
    </tr>
  </tbody>
</table>