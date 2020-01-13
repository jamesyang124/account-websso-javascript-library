# Integration Example

{% api-method method="get" host="https://account.htcvivce.com" path="/RCN/SS/api/real-name-service/v1verify" %}
{% api-method-summary %}
Query real name verify status
{% endapi-method-summary %}

{% api-method-description %}
To use this API, be sure to have valid CORS permission, please connect with OPS to solve this issue if it occurred.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="AuthKey" type="string" required=true %}
Header
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    isVerified: Boolean
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Code Example

Example payload for \*\*validate \*\*function:

```javascript
// with minimum required properties
{
  "client_id": "33035df5-7ddd-4417-a20a-e56722489550",
  "account_id": "832e6453-137f-4319-a471-0d85ac1b0915",
  "redirect_uri": "https://www.google.com?search=htc",
}
// with custom scope or authorities
{
  "client_id": "33035df5-7ddd-4417-a20a-e56722489550",
  "account_id": "832e6453-137f-4319-a471-0d85ac1b0915",
  "redirect_uri": "https://www.google.com?search=htc",
  "scope": "email birthday",
  "authorities": "-facebook.com google.com -weibo.com htc.com"
}
```

Code:

```javascript
var getParameterById = function() { 
  /* any possible behavior of capturing related value */ 
};

window.HTCAccountHost = "// should be set correspondingly. //"

<script>`https://${window.HTCAccountHost}/real-name-verify/lib.js`</script>

window.runRealNameVerification = function(){
  var authResp = HTCAccount.getAuthResponseV2();

  var realNameVerifyOptions = {
    "client_id": getParameterById("client_id").trim() || authResp.client_id,
    "scope": getParameterById("scope").trim() || authResp.scope,
    "authorities": getParameterById("authorities").trim() || "",
    "account_id": getParameterById("account_id").trim() || authResp.uid,
    "redirect_uri": getParameterById("redire_uri").trim() || `${window.location.href}`
  }

  window.HTCRealNameVerify.validate(realNameVerifyOptions);
}

/* if client site not guarantee HTC account loginStatus always valid */

window.runRealNameVerification();


/* if client site not regularly update HTC account loginStatus */

var resp = HTCAccount.getAuthResponseV2();
// or HTCAccount.getAuthResponse()

HTCAccount.getLoginStatus(function(resp) {
    if (resp.status !== 'unknown') {
      window.runRealNameVerification();
    } else {
      // clean session and HTC Account auth key, start re-logged in process.
    }
}, true);
```

