# API Specification

Client only need to call the OAuth authorize API with `state` payload to enable infinity authentication page, the authorize API will check and validate query params based on OAuth setting, then 302 redirect to infinity authentication page.&#x20;

{% hint style="success" %}
To simplify integration effort, we provide the API spec and integration guide for each client. You can by-pass this page and get quick access via below pages:
{% endhint %}

{% hint style="info" %}
Authorize API  will **check and validate query params based on OAuth setting and may throw error message,**  please resolve issues during development/test phase, these errors are usually occurred by in-appropriate OAuth setting, or invalid preset query params.
{% endhint %}

{% hint style="danger" %}
If you **need customization for pre/post sign-up urls**, please contact account team to get authorized permission, **otherwise the requests will be prohibited as malicious user behavior**.
{% endhint %}

{% swagger baseUrl="https://account.htcvive.com" path="/SS/Services/OAuth/Authorize" method="get" summary="OAuth Authorize" %}
{% swagger-description %}
Validate OAuth setting and redirect to infinity authentication url
{% endswagger-description %}

{% swagger-parameter in="query" name="state.cookieConsent" type="array" %}
An array of string to overwrite default cookie consents in account enroll flow.\
\
For Example:\
\
`["performance", "functional"]`\
\
If no value specified for this property , the default value will be empty array and is the same as not carry this property, which means user does not consent to send 3rd party cookies\
\
Currently support toggles:\
\
1\. **performance**\
2\. **functional**\
3\. **targeting**\
4\. **social-media**\
&#x20;
{% endswagger-parameter %}

{% swagger-parameter in="query" name="scope" type="string" %}
URL encoded scopes string, delimited by space. Only set it when need broader scope support
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state" type="string" %}
an URL-encoded state JSON string to trigger UI flow, please check each state.\* fields
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.sessionId" type="string" %}
BI session id, please reuse this id if present
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.clientId" type="string" %}
same as below `client_id,` this is for message passing
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.redirectionUrl" type="string" %}
same as below `redirection_url` , this is for message passing
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.flow" type="string" %}
constant value **infinity**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.requireAuthCode" type="boolean" %}
boolean value to indicate **whether client need authorization code** instead of **access token cookie**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.initView" type="string" %}
either **sign-in** or **sign-up,** default is **sign-in**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.preSignUpUrl" type="string" %}
a customized URL which is injected before infinity sign-up flow, **empty string is not allowed**\
\
**Only supply if needed, or preSignUpUrl may lead redirection loop.**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.viewToggles" type="array" %}
an array of strings to toggle different UI paths in current flow\
\
For Example:\
\
**`["-sign-in"]`** will disable sign-in link in all pages.\
**`["-signup"]`** will disable sign-up link in all pages.\
**`["-promotion"]`** will disable promotion view.\
**`["+org-view"]`** will enable organization profile flow\
**`["logo-htc"]`** will replace logo to HTC & VIVE logo\
\
Currently support toggle string:\
\
**"-sign-in",**\
**"-signup",**\
**"-promotion",**\
**"+org-view",**\
**"log-htc"**\
\
\

{% endswagger-parameter %}

{% swagger-parameter in="query" name="client_id" type="string" %}
client's UUID, ex: _33035df5-7ddd-4417-a20a-e56722489550_
{% endswagger-parameter %}

{% swagger-parameter in="query" name="redirection_url" type="string" %}
an account FE end wrapped url, it will include client's callback url in its **`origin`**  query param, please check below spec to compose this url
{% endswagger-parameter %}

{% swagger-parameter in="query" name="response_type" type="string" %}
constant value **token**
{% endswagger-parameter %}

{% swagger-parameter in="query" name="state.postSignUpUrl" type="string" %}
a customized URL which is injected after infinity sign-up flow, **empty string is not allowed**.\
\
**Only supply if needed, or postSignUpUrl may lead redirection loop.**
{% endswagger-parameter %}

{% swagger-response status="302" description="" %}
```bash
https://account.htcvive.com/SS/Services/OAuth/Authorize
?redirection_url=https%3A%2F%2Fcsdev.htcwowdev.com%2Fhtcaccount%2Fcallback%2F%3Fclient_id%3D33035df5-7ddd-4417-a20a-e56722489550%26origin%3Dhttps%253A%252F%252Fid-dev-websso.htcwowdev.com%252F19%252Fdev.html%26type%3Dredirect
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%7D
&response_type=token
&immediate=FALSE
&client_id=33035df5-7ddd-4417-a20a-e56722489550
```
{% endswagger-response %}
{% endswagger %}

## Redirection URL Format

This url should be registered in OAuth settings for each client, the base url format is:

```
https://account.htcvive.com/htcaccount/callback/?client_id=
```

Query params will be carried:

| Query Parameter Name | Value                                           |
| -------------------- | ----------------------------------------------- |
| client\_id           | @param { Client's OAuth client id }             |
| origin               | @param { URL encoded client's redirection url } |
| type                 | constant value **redirect**                     |

#### Example of Redirection URL

```
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
&state=%7B%22clientId%22%3A%2233035df5-7ddd-4417-a20a-e56722489550%22%2C%22redirectionUrl%22%3A%22https%3A%2F%2Fid-dev-websso.htcwowdev.com%2F19%2Fdev.html%22%2C%22flow%22%3A%22infinity%22%2C%22initView%22%3A%22sign-in%22%2C%22viewToggles%22%3A%5B%5D%2C%22preSignUpUrl%22%3A%22%22%2C%22postSignUpUrl%22%3A%22%22%2C%22cookieConsent%22%3A%5B%22performance%22%2C%22functional%22%5D%7D
&response_type=token
&immediate=FALSE
&client_id=33035df5-7ddd-4417-a20a-e56722489550
```
