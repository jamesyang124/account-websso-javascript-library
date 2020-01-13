---
description: VIVEPORT Web only verify when user's country setting is at CN
---

# Preset

## Set Up HTCAccountHost Global Variable

Before start the verification process, the client should hold several requirements to process it, the very **first** requirement is to set up **which HTC account environment you will request for:**

```javascript
// stage 
window.HTCAccountHost = "accoun-stage.htcvive.com"

// prod
window.HTCAccountHost = "account.htcvive.com"
```

This global variable is the same setup as HTC account web SSO javascript library demand to. If your html file already set it, then you should be good to go.

## Load Script

Embed one of javascript source tag based on your environment in your html file:

```markup
// development, testing, or stage environment
<script>https://accoun-stage.htcvive.com/real-name-verify/lib.js</script>

// production 
<script>https://accoun.htcvive.com/real-name-verify/lib.js</script>
```

After resource loading, a global object `HTC.RealNameVerify` would be a helper to trigger the process of HTC account real name verification.

{% hint style="warning" %}
Be aware of where the javascript resource from. If you are under development or testing, it should be loaded from **account-stage.htcvive.com** domain, o.w. **account.htcvive.com**.
{% endhint %}

## Check HTC Account Status

Before triggering real name verification process, you should check **user's HTC Account is still in valid login status,** in order to do so, the `HTCAccount.getLoginStatus` function would be called. **If the client side HTC account auth key already expired, please refresh it**. The logic flow should be:

```javascript
// assume already load htcaccount.js and initialized.

var resp = HTCAccount.getAuthResponseV2();
// or HTCAccount.getAuthResponse()

HTCAccount.getLoginStatus(function(status) {
    // status check
    // if status is false, start re-logged in HTC Account flow, may need to clear the session of your site 
    // if valid, start to process real name verification
}, true);
```

