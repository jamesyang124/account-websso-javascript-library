# Internal Mechanism

Below is the study of the source code of Web SSO. It comprises of `main.js.coffee` and `web_sso_frame.js.coffee` .

The consumer\(`main`\), which embedded in client service and must launched before provider\(`web_sso_frame`\) initialized.

Both file will load the asset `htcaccount.js` which serve as a single file. It should be separated code base in future.

In the very beginning, `main.js.coffee` will start to initialize global object. One is remote which is an `easyXDMobject` for RPC.

## Init

`HTCAccount.init`will create that remote easyXDM object in client's host, and embedded iframe will locate its path at HTC account's host with `/htcaccount/remotemethod.html.`

```coffeescript
initialized_promise = promise.join([createRemotePromise, loginCallbackPromise])
```

The `createRemotePromise`will be fulfilled when `HTCAcccount.init` runs normally.

### createRemotePromise

Now we describe that `login_callback` which is a local `easyXDM` method in consumer side, but will be called by provider in later.

```coffeescript
login_callback: (msg) ->
          # params
          #   client_id
          #   origin
          # fragments
          #   access_token
          #   expires_in
          #   expire      (calculated from expires_in)
          #   token_type
          #   scope
          #   state

          # check state
          unless callback = State.validate(msg["state"])
            log "[htcaccount websso] state doesnt match"
            return
          # ...
```

`State.validate()` will not just validate, but will try to return a callback which is as first parameter in `HTCAccount.login(callback, opts).`

This is triggered by:

```coffeescript
login: (callback, params = {}) ->
   # ...
   url = oauthUtil.create_oauth_login_url(
      next_url,
      htcaccount_option["appid"],
      htcaccount_option["scope"],
      params["authorities"] || htcaccount_option["authorities"],
      State.generate(callback || true),
      false,
      if use_redirect then "redirect" else "popup"
    )
```

The `State.generate(callback || true)` will try to inject that callback inside State object. This exported State object will store that callback in `_callbacks = {}` property.

```coffeescript
class CallbackableState
  constructor: ->
    _default_state = util.randomString()
    _callbacks = {}

    @generate = (callback = true, prefix = null) ->
      state = if prefix
        prefix + '-' + util.randomString()
      else
        util.randomString()

      _callbacks[state] = callback
      state

    @validate = (state) ->
      # use callback to do assignment
      # but not avoid === vs == strictly equal issue in pure JS
      # "" == false => true, "" === false => false
      if callback = _callbacks[state]
        delete _callbacks[state]
        return callback
      else if state is _default_state
        return true
      else
        return false

module.exports = CallbackableState
```

It will import as instance by `State = new (require('./lib/callbackable_state'))()`. Instance property `@generate` and `@validate` can be use. By closure feature, these can access `_callbacks` and `_default_state` local variables.

### loginCallbackPromise

Once create remote has done, we can go further for rest of the `HTCAccount.init:`

```coffeescript
# convert htc.com%20facebook.com to str seperated by spaces
    if option["authorities"]
      htcaccount_option["authorities"] = authoritiesUtil.parseAuthoritiesParam(option["authorities"])

remote.checkMainSiteLoginStatus(
      htcaccount_option["appid"],
      htcaccount_option["scope"],
      htcaccount_option["authorities"],
      checkMainSiteLoginStatusCallback
    )
```

`checkMainSiteLoginStatusCallback` will fulfilled another promise `loginCallbackPromise.`

`remote.checkMainSiteLoginStatus` will call provider's method`checkMainSiteLoginStatus,` and with a success callback which is optional for `easyXDM` remote method:

```coffeescript
checkMainSiteLoginStatus: (appid, scope, authorities) ->
          #
          # return
          #   status: boolean
          #   uid:
          #
          result =
            status: false
            uid:    false

          trackInfos(appid, scope, authorities)

          if !HtcAuthInfo.getAuthKey()

          else if HtcAuthInfo.isSessionExpired()
            HtcAuthInfo.empty()
          else if !HtcAuthInfo.getHTCAccountId()
            # if no AccountId, logout!
            HtcAuthInfo.empty()
          else
            result["status"]  = true
            result["uid"]     = HtcAuthInfo.getHTCAccountId()

          # result will pass back to consumer's easyXDM success callback
          return result
```

Here `HtcAuthInfo` will read cookies from HTC account web site, such as :

```coffeescript
COOKIE_DATACENTER              = "dc"
COOKIE_HTCSENSE                = "htcsense"
COOKIE_SESSION_EXPIRATION_TIME = "se"
COOKIE_SESSION_GRANTED_TIME    = "sg"
COOKIE_REMEMBER_EMAIL          = "re"
COOKIE_LOCALE                  = "lc"
```

It supports several authentication operation:

```coffeescript
HtcAuthInfo =
  getAuth: ->
    @refresh() unless refreshed
    auth

  getAuthKey: ->
    @getAuth()['AuthKey']

  _composeAuth: (token, type = "htc") ->
    # timestamp expiration check
    # ...

    auth =
      AuthKey:           parts[1]
      Property:          token.Property
      AccountId:         token.AccountId
      IsEmailVerified:   if token.IsEmailVerified is false then false else true
      ProfileUri:        profile_service_uri
      Life:              expiresIn

    auth


  saveToken: (token) ->
    # timestamp expiration check
    # ...
    auth = @_composeAuth(token)
    cookie.set(COOKIE_HTCSENSE, null, removeDomainSessionCookieOption)
```

`checkMainSiteLoginStatusCallback` for remote calling succeed case:

```coffeescript
# ...
if mainSiteLoginStatus["status"]
  # ...
  _.bind(loginCallbackPromise.done, loginCallbackPromise)
else
  loginCallbackPromise.done()

return
```

This will make `loginCallbackPromise` also fulfilled. Since both promises are fulfilled, then the joined promise will run:

```coffeescript
initialized_promise.then( ->
  htcaccount_option["real_initialized"] = true
  refresher.start(0)
  return
)
```

### Popup Login

The `HTCAccount.login` will either be **redirect** or **popup** mode. This will trigger `login_callback` as in **popup** mode. In this mode, host site should have `_htcsso` cookie has been set.

In **popup** mode, we have an extra popup window from provider iframe, so it must send message back to that iframe through HTML5 postMessage once sign-in successfully.

You can `getAuthResponseV2` by this cookie, if you are using `redirect` mode, `getAuthResponseV2` will not return session info for you. Should call `getLoginStatus` instead.

## getLoginStatus

The core logic is to prepare a**`thenable`**`callback when it has been resolved.`

```coffeescript
initialized_promise.then( =>
  if fresh
    renewToken(callback)
  else
    authResponse = @getAuthResponseV2()

    debugger
    loginStatus =
      status:       if authResponse then "connected" else "unknown"
      authResponse: authResponse

    callback(loginStatus)

  return
)
return
```

