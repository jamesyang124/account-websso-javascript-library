# Process Verification

### Trigger Real Name Verification

When all set, we can simply call the **validate** API with corresponding option JSON object, for example:

```javascript
var authResp = HTCAccount.getAuthResponseV2();
// or HTCAccount.getAuthResponse();

var realNameVerifyOptions = {
    "client_id": authResp.client_id,
    "scope": authResp.scope,
    "authorities": "",
    "account_id": authResp.uid,
    "redirect_uri": `${window.location.href}`
}

window.HTCRealNameVerify.validate(realNameVerifyOptions);
```

Then the page will be **redirect** to HTC account web as similar pattern:

```javascript
https://csdev.htcwowdev.com/real-name-verify/validate?
client_id=33035df5-7ddd-4417-a20a-e56722489550&
redirect_uri=https%253A%252F%252Fwww.google.com%253Fsearch%253Dhtc&
account_id=832e6453-137f-4319-a471-0d85ac1b0915&
scope=&
authorities=&
view_mode=%257B%2522viewMode%2522%253A%2522-signup%2520-forgotpwd%2522%257D
```

Now, user may be required to sign-in HTC account if the **AuthKey** cookie is invalid or missing, otherwise the real name verification UI will be rendered.

### Case Handling

During sign-in process, if user sign-in to different account, then the popup modal will notify it, and force user back to **redirect\_uri with a query parameter which indicate the status as failed** such as:

```text
https://www.google.com/?search=htc&rnv_stat=failed
```

There are three types of verification status as query parameters in your redirection URI:

| query parameter | description |
| :--- | :--- |
| **rnv\_stat=err-acct-incst** | indicate that the user logged in with inconsistent account. |
| **rnv\_stat=succeed** | pass the real name verification |
| **rnv\_stat=canceled** | user canceled the verification process via UI action |

Once the redirection is back to your site, these cases should be properly handled. Then either to trigger the verification process again, or into other UI flow.

