# Integration Guide for Infinity Desktop

For Infinity Desktop, due to the OS url length limit for launching default browser, we will compose the API for the client but also need query params for dynamically assignment. 

Please follow below API call spec, we also provide an example to follow for Infinity Desktop at the end of this section. **Use stage domain for development and testing, but please config to production domain for production:**

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
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% api-method method="get" host="https://account.playinfinity.com" path="/SS/api/gateway/v1/desktop/infinity/rapid" %}
{% api-method-summary %}
 OAuth Authorize Rapid Gateway For Infinity Desktop
{% endapi-method-summary %}

{% api-method-description %}
OAuth Authorize API proxy for Infinity Desktop, it set up and handled by account service gateway for rapid flow.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="bi\_sid" type="string" required=false %}
BI session id, if not carried, will generate for it, please reuse this BI session id if present.   
**This field is also used for MIXPANEL data event.**
{% endapi-method-parameter %}

{% api-method-parameter name="bi\_did" type="string" required=false %}
**MIXPANEL distinct id data event field.**  
This parameter is used to identify user session so that we could chain the behavior from upstream client to Account WEBSSO via MIXPANEL distinctId.
{% endapi-method-parameter %}

{% api-method-parameter name="bi\_rc" type="string" required=false %}
**MIXPANEL root client data event field.**  
The first upstream client which is triggered by user. No matter how many middle clients which triggered via several flows, the rootClient is always the initiate client.  
**The value should be client name instead of UUID.**
{% endapi-method-parameter %}

{% api-method-parameter name="bi\_tc" type="string" required=false %}
**MIXPANEL trigger client data event field.**   
The client which is trigger WEBSSO SDK. If user open VIVEPORT Desktop and do sign-up flow via VIVEPORT Store, the trigger client should be VIVEPORT Store.   
**The value should be client name instead of UUID.**
{% endapi-method-parameter %}

{% api-method-parameter name="bi\_fep" type="string" required=false %}
**MIXPANEL flow entry point data event field.**   
The value is given by root client, which described as UI element to initiate the flow. Account WEBSSO SDK just pass this value to record the data value of flow entry point.
{% endapi-method-parameter %}

{% api-method-parameter name="init\_view" type="string" required=false %}
should be **sign-in** or **sign-up, default is sign-in**
{% endapi-method-parameter %}

{% api-method-parameter name="sid" type="string" required=false %}
specify session id for BI logging.   
**THIS FIELD IS DEPRECATED, PLEASE USE `bi_sid` INSTEAD**
{% endapi-method-parameter %}

{% api-method-parameter name="redirection\_url" type="string" required=true %}
url-encoded \(with upper cased URL percent encoding\) Infinity Desktop's local host url with dynamic port.
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=302 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
https://account.playinfinity.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%2C%22postSignUpUrl%22%3A%22%22%7D
&response_type=code
&immediate=FALSE
&client_id=66af865d-f677-4b07-88b1-3a0d950831c2
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Example of OAuth Authorize Proxy Request for Infinity Desktop

```text
https://account.playinfinity.com/SS/api/gateway/v1/desktop/infinity/rapid
?redirection_url=http%3A%2F%2Flocalhost%3A5566&sid=f3fe5e0a-445a-4562-a9a9-5408958b42d7
```

