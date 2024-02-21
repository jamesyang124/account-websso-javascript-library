# Integration Guide for VIVEPORT Desktop

For VIVEPORT Desktop, due to the OS url length limit for launching default browser, we will compose the API for the client but also need query params for dynamically assignment.&#x20;

Please follow below API call spec, we also provide an example to follow for VIVEPORT Desktop at the end of this section. **Use stage domain for development and testing, but please config to production domain for production:**

### HTCAccountHost env

| ENV   | Resource Domain                                                                          |
| ----- | ---------------------------------------------------------------------------------------- |
| STAGE | ​[https://account-stage.htcvive.com](https://account-stage.htcvive.com/)​                |
| PRDO  | ​[https://account.htcvive.com](https://account-stage.htcvive.com/)​                      |
| TEST  | ​[https://cstest.dev.usw2.cs-htc.co](https://cstest.dev.usw2.cs-htc.co/infinity/lib.js)​ |

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
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% swagger baseUrl="https://account.htcvive.com" path="/SS/api/gateway/v1/desktop/infinity" method="get" summary="OAuth Authorize Gateway For VIVEPORT Desktop" %}
{% swagger-description %}
OAuth Authorize API proxy for VIVEPORT Desktop, it set up and handled by account service gateway.
{% endswagger-description %}

{% swagger-parameter in="query" name="bi_sid" type="string" %}
BI session id, if not carried, will generate for it, please reuse this BI session id if present. \
**This field is also used for MIXPANEL data event.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_did" type="string" %}
**MIXPANEL distinct id data event field.**\
This parameter is used to identify user session so that we could chain the behavior from upstream client to Account WEBSSO via MIXPANEL distinctId.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_rc" type="string" %}
**MIXPANEL root client data event field.** \
The first upstream client which is triggered by user. No matter how many middle clients which triggered via several flows, the rootClient is always the initiate client. The value should be client name instead of UUID.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_tc" type="string" %}
**MIXPANEL trigger client data event field.** \
The client which is trigger WEBSSO SDK. If user open VIVEPORT Desktop and do sign-up flow via VIVEPORT Store, the trigger client should be VIVEPORT Store. \
**The value should be client name instead of UUID.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_fep" type="string" %}
**MIXPANEL flow entry point data event field.** \
The value is given by root client, which described as UI element to initiate the flow. Account WEBSSO SDK just pass this value to record the data value of flow entry point.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="sid" type="string" %}
specify session id for BI logging, url encoded.\
**THIS FIELD IS DEPRECATED, PLEASE USE `bi_sid` INSTEAD**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="redirection_url" type="string" %}
url-encoded (with upper cased URL percent encoding) VIVEPORT Desktop's local host url with dynamic port
{% endswagger-parameter %}

{% swagger-response status="302" description="" %}
```bash
https://csdev.htcwowdev.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%22cb2340ce-2cc4-46f8-ac05-f75d40a96699%22%2C%22redirectionUrl%22%3A%22http%3A%2F%2Flocalhost%3A5566%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-up%22%2C%22viewToggles%22%3A%5B%22-sign-in%22%5D%2C%22requireAuthCode%22%3Atrue%2C%22preSignUpUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%7D
&response_type=code
&immediate=FALSE
&client_id=cb2340ce-2cc4-46f8-ac05-f75d40a96699
```
{% endswagger-response %}
{% endswagger %}

### Example of OAuth Authorize Proxy Request for VIVEPORT Desktop

```bash
https://account.htcvive.com/SS/api/gateway/v1/desktop/infinity
?redirection_url=http%3A%2F%2Flocalhost%3A5566&sid=f3fe5e0a-445a-4562-a9a9-5408958b42d7
```

{% swagger baseUrl="https://account.htcvive.com" path="/SS/api/gateway/v1/desktop/infinity/rapid" method="get" summary=" OAuth Authorize Rapid Gateway For VIVEPORT Desktop" %}
{% swagger-description %}
OAuth Authorize API proxy for VIVEPORT Desktop, it set up and handled by account service gateway for rapid flow.
{% endswagger-description %}

{% swagger-parameter in="query" name="client_id" type="string" %}
Default value is for VIVEPORT desktop, please carry this field with proper client id value as enterprise SDK client
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_sid" type="string" %}
BI session id, if not carried, will generate for it, please reuse this BI session id if present. \
**This field is also used for MIXPANEL data event.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_did" type="string" %}
**MIXPANEL data event field.** \
This parameter is used to identify user session so that we could chain the behavior from upstream client to Account WEBSSO via MIXPANEL distinctId.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_rc" type="string" %}
**MIXPANEL data event field.** \
The first upstream client which is triggered by user. No matter how many middle clients which triggered via several flows, the rootClient is always the initiate client. **The value should be client name instead of UUID.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_tc" type="string" %}
**MIXPANEL data event field.** \
The client which is trigger WEBSSO SDK. If user open VIVEPORT Desktop and do sign-up flow via VIVEPORT Store, the trigger client should be VIVEPORT Store. \
**The value should be client name instead of UUID.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_fep" type="string" %}
**MIXPANEL data event  field.** \
The value is given by root client, which described as UI element to initiate the flow. Account WEBSSO SDK just pass this value to record the data value of flow entry point.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="init_view" type="string" %}
should be **sign-in** or **sign-up, default is sign-in**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="sid" type="string" %}
specify session id for BI logging\
**THIS FIELD IS DEPRECATED, PLEASE USE `bi_sid` INSTEAD**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="redirection_url" type="string" %}
url-encoded VIVEPORT Desktop's local host url with dynamic port.
{% endswagger-parameter %}

{% swagger-response status="302" description="" %}
```
https://csdev.htcwowdev.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%2C%22postSignUpUrl%22%3A%22%22%7D
&response_type=code
&immediate=FALSE
&client_id=cb2340ce-2cc4-46f8-ac05-f75d40a96699
```
{% endswagger-response %}
{% endswagger %}

### Example of OAuth Authorize Proxy Request for VIVEPORT Desktop

```
https://account.htcvive.com/SS/api/gateway/v1/desktop/infinity/rapid
?redirection_url=http%3A%2F%2Flocalhost%3A5566&sid=f3fe5e0a-445a-4562-a9a9-5408958b42d7
```

{% swagger baseUrl="https://account.htcvive.com" path="/SS/api/gateway/v2/desktop/infinity/rapid" method="get" summary=" OAuth Authorize Rapid Gateway For VIVEPORT Desktop" %}
{% swagger-description %}
OAuth Authorize API proxy for VIVEPORT Desktop, it set up and handled by account service gateway for rapid flow.
{% endswagger-description %}

{% swagger-parameter in="query" name="client_id" type="string" %}
Default value is for VIVEPORT desktop, please carry this field with proper client id value as enterprise SDK client
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_sid" type="string" %}
BI session id, if not carried, will generate for it, please reuse this BI session id if present. \
**This field is also used for MIXPANEL data event.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_did" type="string" %}
**MIXPANEL data event field.** \
This parameter is used to identify user session so that we could chain the behavior from upstream client to Account WEBSSO via MIXPANEL distinctId.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_rc" type="string" %}
**MIXPANEL data event field.** \
The first upstream client which is triggered by user. No matter how many middle clients which triggered via several flows, the rootClient is always the initiate client. **The value should be client name instead of UUID.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_tc" type="string" %}
**MIXPANEL data event field.** \
The client which is trigger WEBSSO SDK. If user open VIVEPORT Desktop and do sign-up flow via VIVEPORT Store, the trigger client should be VIVEPORT Store. \
**The value should be client name instead of UUID.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="bi_fep" type="string" %}
**MIXPANEL data event  field.** \
The value is given by root client, which described as UI element to initiate the flow. Account WEBSSO SDK just pass this value to record the data value of flow entry point.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="init_view" type="string" %}
should be **sign-in, auth-eth,** or **sign-up, default is sign-in**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="sid" type="string" %}
specify session id for BI logging\
**THIS FIELD IS DEPRECATED, PLEASE USE `bi_sid` INSTEAD**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="redirection_url" type="string" %}
url-encoded VIVEPORT Desktop's local host url with dynamic port.
{% endswagger-parameter %}

{% swagger-response status="302" description="" %}
```
https://csdev.htcwowdev.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%2C%22postSignUpUrl%22%3A%22%22%7D
&response_type=code
&immediate=FALSE&client_id=cb2340ce-2cc4-46f8-ac05-f75d40a96699
```
{% endswagger-response %}
{% endswagger %}

### Example of OAuth Authorize Proxy Request for VIVEPORT Desktop with wallet login

```
https://account.htcvive.com/SS/api/gateway/v2/desktop/infinity/rapid
?redirection_url=http%3A%2F%2Flocalhost%3A5566&sid=f3fe5e0a-445a-4562-a9a9-5408958b42d7
```
