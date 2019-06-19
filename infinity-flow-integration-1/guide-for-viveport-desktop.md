# Integration Guide for VIVEPORT Desktop

For VIVEPORT Desktop, due to the OS url length limit for launching default browser, we will compose the API for the client but also need query params for dynamically assignment. 

Please follow below API call spec, we also provide an example to follow for VIVEPORT Desktop at the end of this section. **Use stage domain for development and testing, but please config to production domain for production:**

### HTCAccountHost env

| ENV | Resource Domain |
| :--- | :--- |
| STAGE | ​[https://account-stage.htcvive.com](https://account-stage.htcvive.com/)​ |
| PRDO | ​[https://account.htcvive.com](https://account-stage.htcvive.com/)​ |
| TEST | ​[https://cstest.dev.usw2.cs-htc.co](https://cstest.dev.usw2.cs-htc.co/infinity/lib.js)​ |

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

{% api-method method="get" host="https://account.htcvive.com" path="/SS/api/gateway/v1/desktop/infinity" %}
{% api-method-summary %}
OAuth Authorize Gateway For VIVEPORT Desktop
{% endapi-method-summary %}

{% api-method-description %}
OAuth Authorize API proxy for VIVEPORT Desktop, it set up and handled by account service gateway.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="redirection\_url" type="string" required=true %}
url-encoded VIVEPORT Desktop's local host url with dynamic port
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=302 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```bash
https://csdev.htcwowdev.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%22cb2340ce-2cc4-46f8-ac05-f75d40a96699%22%2C%22redirectionUrl%22%3A%22http%3A%2F%2Flocalhost%3A5566%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-up%22%2C%22viewToggles%22%3A%5B%22-sign-in%22%5D%2C%22requireAuthCode%22%3Atrue%2C%22preSignUpUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%7D
&response_type=code
&immediate=FALSE
&client_id=cb2340ce-2cc4-46f8-ac05-f75d40a96699
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Example of OAuth Authorize Proxy Request for VIVEPORT Desktop

```bash
https://account.htcvive.com/SS/api/gateway/v1/desktop/infinity
?redirection_url=http%3A%2F%2Flocalhost%3A5566
```

{% api-method method="get" host="https://account.htcvive.com" path="/SS/api/gateway/v1/desktop/infinity/rapid" %}
{% api-method-summary %}
 OAuth Authorize Rapid Gateway For VIVEPORT Desktop
{% endapi-method-summary %}

{% api-method-description %}
OAuth Authorize API proxy for VIVEPORT Desktop, it set up and handled by account service gateway for rapid flow.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="redirection\_url" type="string" required=true %}
url-encoded VIVEPORT Desktop's local host url with dynamic port.
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=302 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
https://csdev.htcwowdev.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%2C%22postSignUpUrl%22%3A%22%22%7D
&response_type=code
&immediate=FALSE
&client_id=cb2340ce-2cc4-46f8-ac05-f75d40a96699
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Example of OAuth Authorize Proxy Request for VIVEPORT Desktop

```text
https://account.htcvive.com/SS/api/gateway/v1/desktop/infinity/rapid
?redirection_url=http%3A%2F%2Flocalhost%3A5566
```

