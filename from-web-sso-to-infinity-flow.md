---
description: >-
  We release a new version of Web SSO SDK which aims to revise the legacy flow
  of sign-in/sign-up to Infinity Omnify flow. In this section, we will introduce
  the change of WEB SSO v2.
---

# From WEB SSO to INFINITY FLOW

## API Backward Compatibility 

This new version aims to reduce the minimum adjustment for clients and then change to infinity flow. We adjust the `login` function so that internal mechanism is different to the old version. In infinity integration API, we setup information in `state` parameter, so we revise `state` parameter at WEB SSO `login` function. Therefore, `state` will be filled specified information automatically.  //TODO  To be continued.

```coffeescript
  login: (callback, params = {}) ->
    #...
    next_url = window.location.href
    if params["next_url"]
      origin_url  = "#{location.protocol}//#{location.host}/"
      next_url = params["next_url"]
      next_url = "#{origin_url}#{if next_url.indexOf("/") is 0 then next_url.substring(1) else next_url}"

    statePayload = if State.isJsonString(params["state"]) then JSON.parse(params["state"]) else {}
    statePayload = JSON.stringify(Object.assign(statePayload,{redirectionUrl:next_url,clientId:htcaccount_option["appid"]}))

    url = oauthUtil.create_oauth_login_url(
      next_url,
      htcaccount_option["appid"],
      htcaccount_option["scope"],
      params["authorities"] || htcaccount_option["authorities"],
      State.generate(statePayload || callback, null, callback),
      false,
      if use_redirect then "redirect" else "popup"
    )
    #...
```



## WEB SSO support the configs of INFINITY

//TODO  To be continued.

## Limitation / Deprecation

We aim to convert the `login` function and avoid increasing too many efforts for clients. Therefore, we keep the legacy mechanism of WEB SSO v2 and convert the necessary data information. However, some features are be deprecated because of modification.

The first, we don't accept the pure text value for `state` parameter. If clients use the format which doesn't consist of JSON, We will convert to a JSON format which has default information.

```coffeescript
#...
statePayload = if State.isJsonString(params["state"]) then JSON.parse(params["state"]) else {}
statePayload = JSON.stringify(Object.assign(statePayload,{redirectionUrl:next_url,clientId:htcaccount_option["appid"]}))
#...
```

The second, we don't support `popup` mode anymore. In WEB SSO v2, clients can still trigger `popup` mode by configs. However, the flow will not redirect to target url at the final step. We don't remove the configuration because some clients still need to execute the legacy flow. Therefore, if client ids are not in the list of legacy clients, please change the type from `popup` to `redirect`.

```coffeescript
// Cusomized options
HTCAccount.login({ /* state callback object */ }, {
    type: "redirect",
    next_url: "/sub/path",
    authorities: "-qq.com"
})
```

