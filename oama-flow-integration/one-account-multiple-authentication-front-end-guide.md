# Integration Guide

## Preface

This document provide an integration with HTC account front end javascript library for One Account Multiple Authentication \(OAMA\). This would help any 3rd party web client to get/create OAMA status for HTC account user.

Supported web browsers are IE11+, Firefox 56+, Chrome 61+, and Safari 11+.

## Set Up HTCAccountHost Global Variable

Before start the OAMA process, the client should hold several requirements to process it, the ver **first** requirement is to set up **which HTC account environment you will request for:**

```javascript
//stage
window.HTCAccountHost = "account-stage.htcvive.com"

//prod
window.HTCAccountHost = "account.htcvive.com"
```

This global variable is the same setup as HTC account web SSO javascript library demanded. If you already set it in your html file, you are good to go.

## Load Script

Embed one of javascript source tag based on your environment in your html file:

```javascript
// development, testing , or stage environment
<script>https://account-stage.htcvive.com/oama/lib.js</script>

// production
<script>https://account.htcvive.com/oama/lib.js</script>
```

After resource loading, a global object **HTCOAMA** would be a helper to trigger the process of HTC account OAMA.

> **WARNING!**  
> Be aware of where the javascript resource from. If you are under development or testing, it should be loaded from **account-stage.htcvive.com** domain, otherwise you should use **account.htcvive.com**.

## Check HTC Account Status

Before triggering OAMA process, you should always make sure user's HTC Account is already logged in. In order to do so, invoke `HTCAccount.getLoginStatus` function. **If client's HTC Account authKey already expired, please refresh it.** The logic are shown below:

```javascript
// assume already load htcaccount.js and initialized.

var resp = HTCAccount.getAuthResponseV2();
// or HTCAccount.getAuthResponse()

HTCAccount.getLoginStatus(function(status) {
    // status check
    // if status is false, start re-login in HTC Account flow, may need to clear the session of your site.
    // if valid, start ot process OAMA
}, true);
```

## Query OAMA Enabled Status

When all set, we can call get API, for example:

```javascript
window.HTCOAMA.get(success_callback, failed_callback);
```

Response:

```text
{"maEnabled": false}
```

> **WARNING!**  
> you will need to inform HTC Account Team to allow CORS for your site.

## Launch OAMA Creation Flow

### Request Payload

You can create OAMA account by invoke `create()` function with an JSON option object:

```javascript
window.HTCOAMA.create(option = {});
```

The format of the _option_ object is as following:

| Name | Description | required | default value | sample value |
| :--- | :--- | :--- | :--- | :--- |
| client\_id | application id, should be obtained from OAuth registration service | Y | N/A | 33035df5-7ddd-4417-a20a-e56722489550 |
| redirect\_uri | callback URI for notifying verify status to application, **must no URI escaped.** | Y | N/A | [https://www.google.com?search=htc](https://www.google.com?search=htc) |

{% hint style="warning" %}
If required field is missing, then the validate function will throw Error message in javascript development console as:

`#HtcAccountException - please supply required valid client_id and redirect_uri`

if you actually fire the request to server, then it will redirect to HTC account front end 404 not found page.
{% endhint %}

### Create OAMA

When all set, we can simply call the **create** API with corresponding option JSON object, for example:

```javascript
var authResp = HTCAccount.getAuthResponseV2();
// or HTCAccount.getAuthResponse();

var oamaCreateOptions = {
    "client_id": authResp.client_id,
    "redirect_uri": `${window.location.href}`
}

window.HTCOAMA.create(oamaCreateOptions);
```

Then the page will be **redirect** to HTC account web as similar pattern:

```bash
https://csdev.htcwowdev.com/oama/create?
client_id=33035df5-7ddd-4417-a20a-e56722489550&
redirect_uri=https%253A%252F%252Fwww.google.com%253Fsearch%253Dhtc
```

The OAMA create page will be rendered.

## Case Handling

There are three types of status as query parameters in your redirection URI:

| query parameter | description |
| :--- | :--- |
| oama\_create\_stat=succeed | oama created |
| oama\_create\_stat=failed&code=9999 | create oama failed with error code |

### Error Code

| code | reason |
| :--- | :--- |
| 4001 | Cannot create MA for native account type |
| 4002 | Get MA status failed |
| 4003 | MA already enabled , will return `oama_create_stat=succeed&code=4003` |
| 4004 | Create MA by email failed |
| 9999 | Service Error |

Once the redirection is back to your site, these cases should be properly handled. Then either to trigger the verification process again, or into other UI flow.

## Revision Note

**2019/01/30**

* change query OAMA function interface

**2019/01/25**

* add error code and response

**2019/01/21**

* first draft

