# Configuration

### Configure Payload

This library provide only **validate** function which carry an JSON option object:

```javascript
window.HTCRealNameVerify.validate(option = {});
```

The format of the _option_ object is as following:

| name          | description                                                                            | required | default value | sample value                         |
| ------------- | -------------------------------------------------------------------------------------- | -------- | ------------- | ------------------------------------ |
| client\_id    | application id, should obtained from OAuth registration service                        | Y        | N/A           | 33035df5-7ddd-4417-a20a-e56722489550 |
| account\_id   | user account id                                                                        | Y        | N/A           | 832e6453-137f-4319-a471-0d85ac1b0915 |
| redirect\_uri | callback URI for notifying verify status to application, **must no URI escaped.**      | Y        | N/A           | https://www.google.com?search=htc    |
| scope         | same setting as HTC account web SSO integration, **keep blank as default scope**       | N        | N/A           | "email birthday"                     |
| authorities   | same setting as HTC account web SSO integration, **keep blank as default authorities** | N        | N/A           | "-facebook.com google.com"           |

In most cases you may not need to customize **scope** and **authorities** two fields. Please refer to **HTC account web SSO javascript library guide** when customization is necessary.

{% hint style="warning" %}
If required field is missing, then the **validate** function will **throw Error message** **in javascript development console as**:

`#HtcAccountException - please supply required valid client_id, account_id, or redirect_uri`

if you actually fire the request to server, then **it will redirect to HTC account front end 404 not found page**.
{% endhint %}
