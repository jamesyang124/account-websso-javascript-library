# Integration Guide for Infinity VR Steam App

For Infinity VR Steam App, due to the OS url length limit for launching default browser, we will compose the API for the client, the final redirection currently will hardcoded to PLAYINFINITY web main page. **Use stage domain for development and testing, but please config to production domain for production:**

### HTCAccountHost env

| ENV | Resource Domain |
| :--- | :--- |
| STAGE | [​https://account-stage.playinfinity.com](https://account-stage.playinfinity.com) |
| PRDO | [​https://account.playinfinity.com​](https://account.playinfinity.com) |
| TEST | [​https://account-test.dev.playinfinity.com](https://account-test.dev.playinfinity.com) |

### HTCProfileDefaultHost env

| ENV | Resource Domain |
| :--- | :--- |
| STAGE | [https://account-profile-stage.htcvive.com](https://account-profile-stage.htcvive.com) |
| PROD | [https://account-profile.htcvive.com](https://account-profile.htcvive.com) |
| TEST | [https://profiletest.htcwowdev.com](https://profiletest.htcwowdev.com) |

### HTCOrgProfileDefaultHost env

| ENV | Resource Domain |
| :--- | :--- |
| STAGE | [https://account-stage-usw2.viveport.com](https://account-stage-usw2.viveport.com) |
| PROD | [https://account.viveport.com](https://account.viveport.com) |
| TEST | [https://business-account-qa.htcwowdev.com](https://business-account-qa.htcwowdev.com) |

{% hint style="danger" %}
If you **need customization for post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% api-method method="get" host="https://account.playinfinity.com" path="/api/gateway/v1/hmd/infinity-vr/steam" %}
{% api-method-summary %}
OAuth Authorize Gateway for Infinity VR Steam App
{% endapi-method-summary %}

{% api-method-description %}
OAuth Authorize API proxy for Infinity VR Steam App, it set up and handled by account service gateway.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="pre\_sign\_up\_url" type="string" required=true %}
**URI encoded string,** for visiting infinity web**,** please append **`hl`** query parameter **to this url for specifying user locale**
{% endapi-method-parameter %}

{% api-method-parameter name="init\_view" type="string" required=true %}
currently as constant, set up to **`sign-up`**
{% endapi-method-parameter %}

{% api-method-parameter name="hl" type="string" required=false %}
host language, can switch to other support locale, **if mis-matched, should fall back to english locale**
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=302 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```bash
https://account.playinfinity.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%22782f0e12-73ec-4b09-b7e2-6e3a0a417f09%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fviveport-web-mock-site.com%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-up%22%2C%22viewToggles%22%3A%5B%22-sign-in%22%5D%2C%22requireAuthCode%22%3Afalse%2C%22preSignUpUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%7D
&response_type=token
&immediate=FALSE
&client_id=782f0e12-73ec-4b09-b7e2-6e3a0a417f09
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Example of OAuth Authorize Proxy Request for Infinity VR Steam App

```bash
https://account.playinfinity.com/api/gateway/v1/hmd/infinity-vr/steam?hl=zh-TW&init_view=sign-up&pre_sign_up_url=https%3A%2F%2Fwww.playinfinity.com%2Fstore%2Fsetup%2Fmyredeem%3Fhl%3Dzh-TW
```

