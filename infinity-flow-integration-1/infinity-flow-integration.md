# API Specification

Client only need to call the OAuth authorize API with `state` payload to enable infinity authentication page, the authorize API will check and validate query params based on OAuth setting, then 302 redirect to infinity authentication page. 

{% hint style="success" %}
To simplify integration effort, we provide the API spec and integration guide for each client. You can by-pass this page and get quick access via below pages:

{% page-ref page="guide-for-pc-client.md" %}

{% page-ref page="guide-for-vive-video-client.md" %}

{% page-ref page="guide-for-viveport-web.md" %}
{% endhint %}

{% hint style="info" %}
Authorize API  will **check and validate query params based on OAuth setting and may throw error message,**  please resolve issues during development/test phase, these errors are usually occurred by in-appropriate OAuth setting, or invalid preset query params.
{% endhint %}

{% api-method method="get" host="https://account.htcvive.com" path="/SS/Services/OAuth/Authorize" %}
{% api-method-summary %}
OAuth Authorize
{% endapi-method-summary %}

{% api-method-description %}
Validate OAuth setting and redirect to infinity authentication url
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="state" type="string" required=true %}
an URL-encoded state JSON string to trigger infinity UI flow, please check each state.\* fields
{% endapi-method-parameter %}

{% api-method-parameter name="state.clientId" type="string" required=true %}
same as below `client_id,` this is for message passing
{% endapi-method-parameter %}

{% api-method-parameter name="state.redirectionUrl" type="string" required=true %}
same as below `redirection_url` , this is for message passing
{% endapi-method-parameter %}

{% api-method-parameter name="state.flow" type="string" required=true %}
constant value **infinity**
{% endapi-method-parameter %}

{% api-method-parameter name="state.requireAuthCode" type="boolean" required=true %}
boolean value to indicate **whether client need authorization code** instead of **access token cookie**
{% endapi-method-parameter %}

{% api-method-parameter name="state.initView" type="string" required=true %}
either **sign-in** or **sign-up,** default is **sign-in**
{% endapi-method-parameter %}

{% api-method-parameter name="state.preSignUpUrl" type="string" required=false %}
a customized URL which is injected before infinity sign-up flow, **empty string is not allowed  
  
Only supply if needed, or preSignUpUrl may lead redirection loop.**
{% endapi-method-parameter %}

{% api-method-parameter name="state.viewToggles" type="array" required=true %}
an array of strings to toggle different UI paths in current flow  
  
For Example:  
  
**`["-forgot-pwd"]`** will disable forgot password link in pages.  
  
Currently support toggle string:  
  
**"-sign-in"**  
{% endapi-method-parameter %}

{% api-method-parameter name="client\_id" type="string" required=true %}
client's UUID, ex: _33035df5-7ddd-4417-a20a-e56722489550_
{% endapi-method-parameter %}

{% api-method-parameter name="redirection\_url" type="string" required=true %}
an account FE end wrapped url, it will include client's callback url in its **`origin`**  query param, please check below spec to compose this url
{% endapi-method-parameter %}

{% api-method-parameter name="response\_type" type="string" required=true %}
constant value **token**
{% endapi-method-parameter %}

{% api-method-parameter name="immediate" type="string" required=true %}
constant value **FALSE**
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=302 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```bash
https://account.htcvive.com/SS/Services/OAuth/Authorize
?redirection_url=https%3A%2F%2Fcsdev.htcwowdev.com%2Fhtcaccount%2Fcallback%2F%3Fclient_id%3D33035df5-7ddd-4417-a20a-e56722489550%26origin%3Dhttps%253A%252F%252Fid-dev-websso.htcwowdev.com%252F19%252Fdev.html%26type%3Dredirect
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%7D
&response_type=token
&immediate=FALSE
&client_id=33035df5-7ddd-4417-a20a-e56722489550
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## Redirection URL Format

This url should be registered in OAuth settings for each client, the base url format is:

```text
https://account.htcvive.com/htcaccount/callback/?client_id=
```

Query params will be carried:

| Query Parameter Name | Value |
| :--- | :--- |
| client\_id | @param { Client's OAuth client id } |
| origin | @param { URL encoded client's redirection url } |
| type | constant value **redirect** |

#### Example of Redirection URL

```text
https://account.htcvive.com/htcaccount/callback/
?client_id=33035df5-7ddd-4417-a20a-e56722489550
&origin=https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html
&type=redirect
```

{% hint style="warning" %}
The **https://account.htcvive.com/htcaccount/callback/?client\_id=** is a fixed url pattern, your should compose url with this format to pass OAuth setting validation check.
{% endhint %}

### Example of OAuth Authorize API Request

```bash
https://account.htcvive.com/SS/Services/OAuth/Authorize
?redirection_url=https%3A%2F%2Fcsdev.htcwowdev.com%2Fhtcaccount%2Fcallback%2F%3Fclient_id%3D33035df5-7ddd-4417-a20a-e56722489550%26origin%3Dhttps%253A%252F%252Fid-dev-websso.htcwowdev.com%252F19%252Fdev.html%26type%3Dredirect
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%7D
&response_type=token
&immediate=FALSE
&client_id=33035df5-7ddd-4417-a20a-e56722489550
```
