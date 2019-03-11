# Integration Guide for VIVE Video Client

For VIVE Video client, due to the OS url length limit for launching default browser, we will compose the API for the client but also need query params for dynamically assignment.

{% api-method method="get" host="https://account.htcvive.com" path="/SS/api/proxy/v1/hmd/infinity" %}
{% api-method-summary %}
OAuth Authorize Proxy for AIO Client
{% endapi-method-summary %}

{% api-method-description %}
OAuth Authorize API proxy for VIVE Video client, it set up and handled by account service gateway.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="redirection\_url" type="string" required=true %}
VIVE VIDEO client's final redirection url, should be VIVEPORT web's url
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=302 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```bash
https://account.htcvive.com/SS/Services/OAuth/Authorize
?redirection_url=http%3A%2F%2Flocalhost%3A5566
&state=%7B%22clientId%22%3A%22cb2340ce-2cc4-46f8-ac05-f75d40a96699%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fviveport-web-mock-site.com%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-up%22%2C%22viewToggles%22%3A%5B%22-sign-in%22%5D%2C%22requireAuthCode%22%3Afalse%2C%22preSignUpUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%7D
&response_type=token
&immediate=FALSE
&client_id=cb2340ce-2cc4-46f8-ac05-f75d40a96699
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Example of OAuth Authorize Proxy Request for VIVE Video Client

```bash
https://account.htcvive.com/SS/api/proxy/v1/hmd/infinity
?redirection_url=https%3A%2F%2Fviveport-web-mock-site.com
```



