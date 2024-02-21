# Integration Guide for Infinity VR Oculus App

For Infinity VR Oculus App, due to the OS url length limit for launching default browser, we will compose the API for the client, the final redirection currently will hardcoded to PLAYINFINITY web main page. **Use stage domain for development and testing, but please config to production domain for production:**

### HTCAccountHost env

| ENV   | Resource Domain                                                                         |
| ----- | --------------------------------------------------------------------------------------- |
| STAGE | [​https://account-stage.playinfinity.com](https://account-stage.playinfinity.com)       |
| PRDO  | [​https://account.playinfinity.com​](https://account.playinfinity.com)                  |
| TEST  | [​https://account-test.dev.playinfinity.com](https://account-test.dev.playinfinity.com) |

### HTCProfileDefaultHost env

| ENV   | Resource Domain                                                                        |
| ----- | -------------------------------------------------------------------------------------- |
| STAGE | [https://account-profile-stage.htcvive.com](https://account-profile-stage.htcvive.com) |
| PROD  | [https://account-profile.htcvive.com](https://account-profile.htcvive.com)             |
| TEST  | [https://profiletest.htcwowdev.com](https://profiletest.htcwowdev.com)                 |

### HTCOrgProfileDefaultHost env

| ENV   | Resource Domain                                                                        |
| ----- | -------------------------------------------------------------------------------------- |
| STAGE | [https://account-stage-usw2.viveport.com](https://account-stage-usw2.viveport.com)     |
| PROD  | [https://account.viveport.com](https://account.viveport.com)                           |
| TEST  | [https://business-account-qa.htcwowdev.com](https://business-account-qa.htcwowdev.com) |

{% hint style="danger" %}
If you **need customization for post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% hint style="danger" %}
For MIXPANEL BI log sending, please **MUST** carry all **bi\_\*** fields, o.w. you may omit those fields if tend to not send MIXPANEL BI log.
{% endhint %}

{% swagger baseUrl="https://account.playinfinity.com" path="/api/gateway/v1/hmd/infinity-vr/oculus" method="get" summary="OAuth Authorize Gateway for Infinity VR Steam App" %}
{% swagger-description %}
OAuth Authorize API proxy for Infinity VR Oculus App, it set up and handled by account service gateway.
{% endswagger-description %}

{% swagger-parameter in="query" name="bi_sid" type="string" %}
**This field is also used for MIXPANEL data event.**\
**MUST carry all other bi\_\* fields to trigger MIXPANEL log sending.**\
\
BI session id, if not carried, will generate for it, please reuse this BI session id if present.&#x20;
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_did" type="string" %}
**MIXPANEL data distinct id event field.**\
**MUST carry all other bi\_\* fields to trigger MIXPANEL log sending.**\
\
This parameter is used to identify user session so that we could chain the behavior from upstream client to Account WEBSSO via **MIXPANEL** distinctId.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_rc" type="string" %}
**MIXPANEL data root client event field.**\
**MUST carry all other bi\_\* fields to trigger MIXPANEL log sending.**\
\
The first upstream client which is triggered by user. No matter how many middle clients which triggered via several flows, the rootClient is always the initiate client.\
**The value should be client name instead of UUID.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_tc" type="string" %}
**MIXPANEL data trigger client event field.** \
**MUST carry all other bi\_\* fields to trigger MIXPANEL log sending.**\
\
The client which is trigger WEBSSO SDK. If user open VIVEPORT Desktop and do sign-up flow via VIVEPORT Store, the trigger client should be VIVEPORT Store. \
**The value should be client name instead of UUID.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_fep" type="string" %}
**MIXPANEL data flow entry point event field.** \
**MUST carry all other bi\_\* fields to trigger MIXPANEL log sending.**\
\
The value is given by root client, which described as UI element to initiate the flow. Account WEBSSO SDK just pass this value to record the data value of flow entry point.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="pre_sign_up_url" type="string" %}
**URI encoded string** for visiting infinity web redeem or select plan pages**,** please append **`hl`** query parameter **to this url for specifying user locale**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="init_view" type="string" %}
currently as constant, set up to **`sign-up`**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="hl" type="string" %}
host language, can switch to other support locale, **if mis-matched, should fall back to english locale**
{% endswagger-parameter %}

{% swagger-response status="302" description="" %}
```bash
https://account.playinfinity.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%22285a6193-1059-4569-b679-59cf6772fe45%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fviveport-web-mock-site.com%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-up%22%2C%22viewToggles%22%3A%5B%22-sign-in%22%5D%2C%22requireAuthCode%22%3Afalse%2C%22preSignUpUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%7D
&response_type=token
&immediate=FALSE
&client_id=285a6193-1059-4569-b679-59cf6772fe45
```
{% endswagger-response %}
{% endswagger %}

### Example of OAuth Authorize Proxy Request for Infinity VR Steam App

```bash
https://account.playinfinity.com/api/gateway/v1/hmd/infinity-vr/oculus?hl=zh-TW&init_view=sign-up&pre_sign_up_url=https%3A%2F%2Fwww.playinfinity.com%2Fstore%2Fsetup%2Fplans%3Fhl%3Dzh-TW
```
