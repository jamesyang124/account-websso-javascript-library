# Auth Configs for Web SSO V2

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% hint style="danger" %}
For MIXPANEL BI log sending, please **MUST** carry all MIXPANEL data event fields, o.w. you may omit those fields if tend to not send MIXPANEL BI log.
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
      <td style="text-align:left">
        <p><b>This field is also used for MIXPANEL data event.</b>
        </p>
        <p><b>MUST carry all other MIXPANEL fields to trigger MIXPANEL log sending.</b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p>BI session id, if not carried, will generate for it, <b>please reuse this BI session id if present.</b>
        </p>
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
        <p>Empty array, or as one of element in<code>[&quot;-sign-in&quot;, &quot;-promotion&quot;, &quot;+org-view&quot;, &quot;logo-htc&quot;]</code> for
          VIVE Video.</p>
        <p>If you want to trigger advanced org flow, you must include these elements
          as below:</p>
        <p>[&quot;+advanced-org-view&quot;,&quot;+org-view&quot;]</p>
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
      <td style="text-align:left">distinctId</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">
        <p><b>MIXPANEL data event field</b>.</p>
        <p><b>MUST carry all other MIXPANEL fields to trigger MIXPANEL log sending.</b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p>This parameter is used to identify user session so that we could chain
          the behavior from upstream client to Account WEBSSO via <b>MIXPANEL </b>distinctId.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">rootClient</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">
        <p><b>MIXPANEL data event field. </b>
        </p>
        <p><b>MUST carry all other MIXPANEL fields to trigger MIXPANEL log sending.</b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p>The first <b>upstream </b>client which is triggered by user.
          <br />No matter how many middle clients which triggered via several flows, the
          rootClient is always the initiate client.</p>
        <p><b>The value should be client name instead of UUID</b>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">triggerClient</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">
        <p><b>MIXPANEL data event field. </b>
        </p>
        <p><b>MUST carry all other MIXPANEL fields to trigger MIXPANEL log sending.</b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p>The client which trigger WEBSSO SDK. If user open PC-Client and do sign-up
          flow via VIVEPORT Store, the trigger client should be VIVEPORT Store. <b>The value should be client name instead of UUID</b>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">flowEntryPoint</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">
        <p><b>MIXPANEL data event field. </b>
        </p>
        <p><b>MUST carry all other MIXPANEL fields to trigger MIXPANEL log sending.</b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p>The value is given by root client, which described as UI element to initiate
          the flow. Account WEBSSO SDK just pass this value to record the data value
          of flow entry point.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">prefillEmail</td>
      <td style="text-align:left">optional</td>
      <td style="text-align:left">string text valid email format, ex: &quot;email@address.com&quot;, this
        is used for org user invitation email flow.</td>
    </tr>
  </tbody>
</table>