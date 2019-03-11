# Q&A

1. How can I change test environment during development?

   > By default WEB SSO library will send the request to production host.
   >
   > In order to change the host environment, please set up global variable `window.HTCAccountHost` before loading `htcaccount.js`

   ```markup
    <script>
    window.HTCAccountHost = "account-stage.htcvive.com"
    </script>
    // this should based on your deployment environment
    <script src="https://account-stage.htcvive.com/htcaccount.js"></script>
   ```

2. Why I get `invalid_client` response when call `HTCAccount.login`?

   > Check the app id is correctly registered in right environment. And the scope is filled with all permitted permission.  
   > Also, make sure `HTCAccount.init()` was called one time only, the later `init` is invalid.

3. Should I also include `htcaccount.js` in my redirection page?

   > Since user already login to HTC account service, then you should be able to check login status or get profile via `HTCAccount` object.

4. I am using mobile app id, is it available for WEB SSO library?

   > No, the WEB SSO library is only for web application. Please contact HTC account team to register a new app id for web view sign-in scenario.

5. I usually get login failed status from user, how can I resolve this?

   > Make sure the 3rd party site cookie is not disabled, because HTC **`websso`** leverage cookie based authentication, so the integration App may notify user not to disable 3rd party site cookie access.  
   > [http://whatis.techtarget.com/definition/third-party-cookie](http://whatis.techtarget.com/definition/third-party-cookie)

