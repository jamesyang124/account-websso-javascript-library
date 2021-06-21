# Basic Usuage

HTC Account Web SSO Javascript Library is obtained from `htcaccount.js`. You can call the `HTCAccount.init()` with config to initialize the library service.

A simeple example should as follow:

```markup
// this should based on your deployment environment
<script src="https://account.htcvive.com/htcaccount.js"></script>
<script>
  var config = { appid: "$APPID", scope: "email birthday" };
  var authConfigs = {
    "sessionId": "4686d579-4176-46fc-8636-660643cf1f8f",
    "clientId": config.appid,
    "flow": "infinity",
    "initView": "sign-in",
    "scope": config.scope,
    "viewToggles": [],
    "requireAuthCode": false
  };
  HTCAccount.init(config);
  HTCAccount.Event.subscribe('auth.login', function(response){
    if (resp.status === 'connected') {
      // get user profile
    } else {
      HTCAccount.login(() => {}, {
        type: 'redreict',
        next_url: location.pathname,
        state: JSON.stringify(authConfigs)
      });
    }
  });
</script>
```

We also provide asynchronous mode to help user able to load the `htcaccount.js` with `async/defer` attribute or after the inline javascript code:

## Caveat - Always put inline script above the async javascript resource loading.

```markup
<script>
 window.htcAccountAsyncInit = function() {
  var config = {appid: "$APPID"};
  HTCAccount.init(config);
  HTCAccount.getLoginStatus(function() {
    // APP init
  });
}
</script>
<script src="https://account.htcvive.com/htcaccount.js"></script>
<script async src="https://account.htcvive.com/htcaccount.js"></script>
```

## Change Default Language

If you need to change the host language during login flow, you may append below query string to switch locale:

```markup
<script async src="https://account.htcvive.com/htcaccount.js?hl={localeName}"></script>
```

The feasible locale list:

```text
ar    da    es-US fi    it    ko    nl    ru    zh-CN
cs    de    es    fr    ja    nb    pl    sv    zh-TW
en
```

Example:

```markup
<script async src="https://account.htcvive.com/htcaccount.js?hl=fr"></script>
```

## Customize Sign-in Options

If you need to only **HTC account native sign-in by \(email/phone\)**, manually set up in `HTCAccount.init` call:

```javascript
HTCAccount.init({
    appid: "0058ef13-1fed-4a0a-b6fb-1181cc525507",
    scope: "email birthday",
    authorities: "htc.com"
})
```



