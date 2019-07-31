# Example Code

Below is one of the production code snippet we suggested, don't directly redirect location inside `HTCAccount.login`'s callback. This may lead browser constraint issue.

```markup
<script src="https://account.htcvive.com/htcaccount.js"></script>
<script>
var SsoObject = (function() {
    'use strict';

    var SsoObject = {
        returnCookieDomain: function() {
            var domainName = $.url().attr('host'),
                domainCookie;

            switch (domainName) {
                case 'production-domain':
                    domainCookie = 'prod';
                    break;
                case 'stage-domain':
                case 'test-domain':
                    domainCookie = 'stage-or-test-domain';
                    break;
                default:
                    domainCookie = 'local-domain';
                    break;
            }
            return domainCookie;
        },
        init: function() {
            var returnUrl = $.url().param('prevUrl');
            HTCAccount.init(SsoObject.settings());

            switch ($.url().param('action')) {
                case 'login':
                    console.log('ssologin.html :: login');
                    /* Global HTCAccount */

                    // SsoObject.loginPageUrl is a relaive path url
                    var redirectUrl = SsoObject.loginPageUrl + '?action=savecookie&prevUrl=' + encodeURIComponent(returnUrl);

                    HTCAccount.login(function(data) {}, SsoObject.settingRedirect(redirectUrl));
                    break;
                case 'savecookie':
                    console.log('ssologin.html :: savecookie');

                    HTCAccount.getLoginStatus(function(data) {
                        if (data.status === 'connected') {
                            SsoObject.storeCookie('htc-account-token', data.authResponse.access_token);
                            SsoObject.storeCookie('htc-uid', data.authResponse.uid);

                            HTCAccount.getProfile(function(data) {
                                console.log('getProfile ok');

                                SsoObject.storeCookie('htc-has-sso', JSON.stringify(data));

                                location.href = returnUrl;
                            }, function() {
                                console.log('getProfile fail');

                                $.removeCookie('htc-has-sso');
                                location.href = returnUrl;
                            });
                        } else {
                            location.href = returnUrl;
                        }
                    }, true);
                    break;
                case 'logout':
                    console.log('ssologin.html :: logout');

                    HTCAccount.logout(
                        function(data) {
                            if (data.status === 'unknown') {
                                $.removeCookie('htc-has-sso', {
                                    domain: SsoObject.returnCookieDomain(),
                                    path: '/'
                                });
                                $.removeCookie('htc-account-token', {
                                    domain: SsoObject.returnCookieDomain(),
                                    path: '/'
                                });
                                location.href = returnUrl;
                            }
                        }
                    );
                    break;
                case "checkstatus":
                    console.log('ssologin.html :: checkstatus');

                    HTCAccount.getLoginStatus(function(data) {
                        if (data.status === 'connected') {
                            HTCAccount.getProfile(function(data) {
                                SsoObject.storeCookie('htc-has-sso', JSON.stringify(data));
                                $('input.htc-has-sso').val(JSON.stringify(data));
                                document.write('[' + JSON.stringify(data) + ']');
                            }, function() {});
                        }
                    }, true);
                    break;
            }
        },
        loginPageUrl: '/us/ssologin',
        settings: function() {
            appConf = {
                appid: appKey,
                scope: 'some scopes'
            };
            return appConf;
        },
        settingRedirect: function(redirectUrl) {
            var loginoption = {
                type: "redirect",
                next_url: redirectUrl,
                scope: 'some scopes'
            };
            return loginoption;
        },
        storeCookie: function(keyName, cookieValue) {
            $.cookie(keyName, cookieValue, {
                domain: SsoObject.returnCookieDomain(),
                path: '/'
            });
        }
    };

    return SsoObject;
}());

$(document).ready(function() {
    SsoObject.init();
});
</script>
```

