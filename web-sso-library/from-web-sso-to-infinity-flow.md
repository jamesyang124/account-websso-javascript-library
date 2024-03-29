---
description: >-
  Web SSO v2 aims to revise the current web SSO v1 for better UX experience.
  This section will introduce the change and its compatibility for web SSO v1
  configurations.
---

# Migrate From V1 to V2 \[DEPRECATED]

{% hint style="danger" %}
We are going to retire WEB SSO V2 support due to maintenance issue. Please switch to server-to-server SSO if have server stack for your app.



Since Feb. 2024, the WEB SSO SDK V1 will silently bump to V3, and should be compatible with Chrome's updated 3rd party cookie policy. The integration should be the same for V1 & V3 SDK. If client's integration is before Feb. 2024, please send us feedback if regression issue occurred.
{% endhint %}

## API Backward Compatibility&#x20;

**Web SSO v2** support the minimum adjustment for web SSO v1 clients to seamlessly configure **with Web SSO v2's default configs**. Current web SSO v1 clients should have no code base change for the upgrade.&#x20;

{% hint style="info" %}
If you need customization for v2's configs, please check below link:

Or if you want directly integrate with Web SSO v2, please check below link:
{% endhint %}

### Web SSO V2 Default Configs for Web SSO V1 API

We adjust the **web SSO v1's** `HTCAccount.login` function parameters for interface compatibility. **The function** `HTCAccount.login` **'s second parameter now accept the optional `state` field which is a JSON string as web SSO v2's auth configs for the customization:**

```coffeescript
  # source code of HTCAccount.login API
  login: (callback, params = {}) ->
    #...
    statePayload = if State.isJsonString(params["state"]) then JSON.parse(params["state"]) else {}
    statePayload = JSON.stringify(
      Object.assign(statePayload, {
        redirectionUrl: next_url, 
        clientId: htcaccount_option["appid"]
    }))
    #...
```

If that **state** field is missing, the default config for web SSO v2 should have at least have `redirectionUrl` and `clientId` fields.

## Migration Tips

We aim to convert the `login` function and avoid increasing extra code change for web SSO V1 clients. Therefore, we keep the WEB SSO v1 API interface and convert the necessary data information for v2 configs. However, some features will be deprecated or modified for the upgrade:

### 1. State Parameter in HTCAccount.login API

The `state` JSON property in `HTCAccount.login`'s second parameter must have JSON string value for customization. We will convert to a JSON format if the value which has default information:

```coffeescript
#...
statePayload = if State.isJsonString(params["state"]) then JSON.parse(params["state"]) else {}
statePayload = JSON.stringify(Object.assign(statePayload,{redirectionUrl:next_url,clientId:htcaccount_option["appid"]}))
#...
```

### 2. Deprecation of Window Popup mode in Web SSO V2

**We don't support `popup` mode anymore in WEB SSO v2**. Current field `type` will no longer take effect for switching popup or redirection mode, **but required to be set as `redirect`**. **WEB SSO v2 will only take url redirection when the flow is finished**.&#x20;

Please change the **type** field from `popup` to `redirect`as below sample code:

```javascript
// Cusomized options for web SSO v2
HTCAccount.login({ /* state callback object */ }, {
    type: "redirect",       // should change to redirect in any case for v2
    next_url: "/sub/path",
    // authorities field will be deprecated, and no effect for v2
    // please move this field to authConfigs as state's JSON string
    authorities: "-qq.com",
    state: JSON.stringify(authConfigs)
})
```

### 3. Deprecation of Social Authorities UI Customization in Web SSO V2

Currently web SSO V2 not support social authorities customization, and will no longer take effect from the authorities field in `HTCAccount.login`function's second parameter.
